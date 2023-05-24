# 전략 계약

전략[ 컨트렉트는](https://github.com/beefyfinance/beefy-contracts/tree/master/contracts/BIFI/strategies) Nesto 투자 모델의 주요 동인이며, 이는 수확량 농장 보상의 자동 합성을 용이하게 합니다. Nesto의 프로세스에는 세 가지 주요 단계가 있습니다. (1) 관련 농장에 예치된 토큰을 스테이킹합니다. (2) 수확 보상; (3) 보상을 더 많은 예치 토큰으로 교환하고 수익금을 재투자합니다.

각 전략 컨트렉트는 궁극적으로 [Vault 컨트렉트](https://docs.beefy.finance/developer-documentation/vault-contract)그들이 배포하는 자본에 대해 일반 사용자와 직접적인 상호 작용이 없습니다. Vault 및 전략 컨트렉트는 사용자 예치금에서 전략의 모든 위험을 격리하기 위해 별도로 유지됩니다.

## 종속성

모든 Nesto 전략은 배포 시 전략 컨트렉트로 가져오는 다양한 종속성 및 인터페이스에 의존합니다. 전략이 다양한 기능을 상속할 수 있도록 하는 핵심 종속성은 다음과 같습니다.

* [StratFeeManager 컨트렉트](https://docs.beefy.finance/developer-documentation/strategy-contract/stratfeemanager-contract); 그리고
* [GasFeeThrottler 컨트렉트](https://docs.beefy.finance/developer-documentation/strategy-contract/gasfeethrottler-contract).

## 인터페이스

전략이 제3자 컨트렉트와 상호 작용할 수 있도록 하는 주요 인터페이스는 다음과 같습니다.

* **라우터 컨트랙트 인터페이스** - 자동 합성 프로세스에 포함된 서로 다른 토큰 간의 스왑을 허용합니다(예: IUniswapRouterETH.sol).
* **유동성 풀 컨트렉트 인터페이스** - Vault가 유동성을 제공하고 농장이 그 위에 구축되는 기본 풀입니다(예: IUniswapV2Pair.sol). 그리고
* **요리사 컨트렉트 인터페이스** - 유동성 제공에 대한 보상을 발행하는 농장(예: IMiniChefV2.sol).

## 기능 보기

### balanceOf() / balanceOfWant()

전략에 저장된 기본 팜 토큰(또는 "원")의 양을 확인합니다. 특정 양의 토큰을 반환합니다.

```
function balanceOf() public view returns (uint256) {
    return balanceOfWant() + balanceOfPool();
}

function balanceOfWant() public view returns (uint256) {
    return IERC20(want).balanceOf(address(this));
}
```

### 균형 풀()

요리사 컨트렉트에 저장된 기본 팜 토큰(또는 "원")의 양을 확인합니다. 특정 양의 토큰을 반환합니다.

```
function balanceOfPool() public view returns (uint256) {
    (uint256 _amount, ) = IMiniChefV2(chef).userInfo(poolId, address(this));
    return _amount;
}
```

### 보상 사용 가능()

전략 컨트랙트에서 청구할 수 있는 셰프 컨트랙트에서 보유하고 있는 보류 중인 보상의 양을 확인합니다. 특정 양의 토큰을 반환합니다.

```
function rewardsAvailable() public view returns (uint256) {
    return IMiniChefV2(chef).pendingSushi(poolId, address(this));
}
```

### callReward()

전략 컨트랙트에서 청구할 수 있는 셰프 컨트랙트에서 보유하고 있는 보류 중인 보상의 양을 확인합니다. 특정 양의 토큰을 반환합니다.

```
function callReward() external view returns (uint256) {
    uint256 pendingReward;
    address rewarder = IMiniChefV2(chef).rewarder(poolId);
    if (rewarder != address(0)) {
        pendingReward = IRewarder(rewarder).pendingToken(poolId, address(this));
    }
    uint256 outputBal = rewardsAvailable();
    uint256 nativeOut;
    if (reward == native) {
        nativeOut = pendingReward;
    } else if (pendingReward > 0) {
        uint256 poolLength = params.rewardToNative.path.length;
        uint256 amount = pendingReward;
        for (uint i; i < poolLength;) {
            bytes memory data = abi.encode(routes.rewardToNative[i], amount);
            amount = IBentoPool(params.rewardToNative.path[i].pool).getAmountOut(data);
            unchecked { ++i; }
        }
        nativeOut = amount;
    }
    if (outputBal > 0) {
        bytes memory data = abi.encode(output, outputBal);
        nativeOut += IBentoPool(params.outputToNative.path[0].pool).getAmountOut(data);
    }
    IFeeConfig.FeeCategory memory fees = getFees();
    return nativeOut * fees.total / DIVISOR * fees.call / DIVISOR;
}
```

## 쓰기 기능

### 보증금()

연결된 셰프 컨트렉트를 통해 기본 팜 토큰(또는 "원함")을 팜에 입금합니다. 먼저 셰프에게 전체 잔액을 예치하기 전에 전략이 기본 팜 토큰(또는 "원함") 중 일부를 보유하고 있는지 확인합니다.

```
function deposit() public whenNotPaused {
    uint256 wantBal = IERC20(want).balanceOf(address(this));
    if (wantBal > 0) {
        IMiniChefV2(chef).deposit(poolId, wantBal, address(this));
        emit Deposit(balanceOf());
    }
}
```

### 철회

사용자 인출을 용이하게 하기 위해 Vault에서 호출하는 외부 기능. 먼저 기본 팜 토큰(또는 "want")의 잔액이 요청을 이행하기에 충분한지 확인한 다음 Vault 컨트렉트로 다시 전송하기 전에 요리사 컨트렉트에서 해당 금액을 인출합니다.

```
function withdraw(uint256 _amount) external {
    require(msg.sender == vault, "!vault");
    uint256 wantBal = IERC20(want).balanceOf(address(this));
    if (wantBal < _amount) {
        IMiniChefV2(chef).withdraw(poolId, _amount.sub(wantBal), address(this));
        wantBal = IERC20(want).balanceOf(address(this));
    }
    if (wantBal > _amount) {
        wantBal = _amount;
    }
    if (tx.origin != owner() && !paused()) {
        uint256 withdrawalFeeAmount = wantBal * withdrawalFee / WITHDRAWAL_MAX;
        wantBal = wantBal - withdrawalFeeAmount;
    }
    IERC20(want).safeTransfer(vault, wantBal);
    emit Withdraw(balanceOf());
}
```

### 수확

Harvest는 모든 사용자를 위해 Vault 합성을 호출합니다. 특히 이것은 셰프 컨트렉트에서 수확하고 수확에 대한 수수료를 부과한 다음 수확된 보상을 농장에 다시 예치하여 자동 합성 효과를 달성합니다.

이 함수는 완전히 분산되어 있어 누구나 함수를 호출할 수 있으며 총 수익률의 0.05\~0.5% 사이의 보상을 받을 수 있습니다. 이것은 아래에 자세히 설명된 세 가지 방법 중 하나로 호출할 수 있습니다.

```
// @dev Default harvest() method.
function harvest() external virtual gasThrottle {
    _harvest(tx.origin);
}

// @dev Alternative harvest() method, where caller receives a fee.
function harvest(address callFeeRecipient) external virtual {
    _harvest(callFeeRecipient);
}

// @dev Alternative harvest() method, where manager calls without gas throttling.
function managerHarvest() external onlyManager {
    _harvest(tx.origin);
}

// @dev Underlying internal _harvest() function, used by all 3 public methods.
function _harvest(address callFeeRecipient) internal whenNotPaused {
    IMiniChefV2(chef).harvest(poolId, address(this));
    uint256 outputBal = IERC20(output).balanceOf(address(this));
    uint256 rewardBal = IERC20(reward).balanceOf(address(this));
    if (outputBal > 0 || rewardBal > 0) {
        chargeFees(callFeeRecipient);
        addLiquidity();
        uint256 wantHarvested = balanceOfWant();
        deposit();
        lastHarvest = block.timestamp;
        emit StratHarvest(msg.sender, wantHarvested, balanceOf());
    }
}
```

### 요금()

모든 비용을 청구하는 내부 방법[수확하다()](https://docs.beefy.finance/developer-documentation/strategy-contract#harvest)라우터 컨트렉트를 통해 전략의 기본 토큰을 출력 토큰으로 교환하여 호출합니다. 그런 다음 컨트렉트는 다른 수수료 수신자에 대한 출력을 계산하고 할당에 따라 출력 토큰을 전송합니다. 수신자는 수확 호출자, 컨트렉트를 배치한 전략가 및 Nesto 재무부입니다.

```
function chargeFees(address callFeeRecipient) internal {
    IFeeConfig.FeeCategory memory fees = getFees();
    uint256 rewardBal = IERC20(reward).balanceOf(address(this));
    if (rewardBal > 0 && reward != native) {
        ITridentRouter.ExactInputParams memory _rewardToNative = params.rewardToNative;
        _rewardToNative.amountIn = rewardBal;
        ITridentRouter(unirouter).exactInputWithNativeToken(_rewardToNative);
    }
    uint256 outputBal = IERC20(output).balanceOf(address(this));
    if (outputBal > 0) {
        ITridentRouter.ExactInputParams memory _outputToNative = params.outputToNative;
        _outputToNative.amountIn = outputBal;
        ITridentRouter(unirouter).exactInputWithNativeToken(_outputToNative);
    }
    uint256 nativeBal = IERC20(native).balanceOf(address(this)) * fees.total / DIVISOR;
    uint256 callFeeAmount = nativeBal * fees.call / DIVISOR;
    IERC20(native).safeTransfer(callFeeRecipient, callFeeAmount);
    uint256 beefyFeeAmount = nativeBal * fees.beefy / DIVISOR;
    IERC20(native).safeTransfer(beefyFeeRecipient, beefyFeeAmount);
    uint256 strategistFeeAmount = nativeBal * fees.strategist / DIVISOR;
    IERC20(native).safeTransfer(strategist, strategistFeeAmount);
    emit ChargedFees(callFeeAmount, beefyFeeAmount, strategistFeeAmount);
}

```

### addLiquidity()

의 일부로 팜의 기본 풀에 유동성을 추가하는 내부 방법 [수확하다()](https://docs.beefy.finance/developer-documentation/strategy-contract#harvest)기능. 출력 토큰을 팜의 기본 토큰으로 교환한 다음 유동성 풀에 둘 다 추가하여 기본 팜 예금 토큰(또는 "원")을 얻습니다. _그런 다음 수확() 호출_ 의 나머지 부분 에서 이러한 토큰을 팜에 보관합니다.

```
function addLiquidity() internal {
    uint256 nativeHalf = IERC20(native).balanceOf(address(this)) / 2;
    if (lpToken0 != native) {
        ITridentRouter.ExactInputParams memory _nativeToLp0 = params.nativeToLp0;
        _nativeToLp0.amountIn = nativeHalf;
        ITridentRouter(unirouter).exactInputWithNativeToken(_nativeToLp0);
    }
    if (lpToken1 != native) {
        ITridentRouter.ExactInputParams memory _nativeToLp1 = params.nativeToLp1;
        _nativeToLp1.amountIn = nativeHalf;
        ITridentRouter(unirouter).exactInputWithNativeToken(_nativeToLp1);
    }
    ITridentRouter.TokenInput[] memory tokens = new ITridentRouter.TokenInput[](2);
    uint256 lp0Bal = IERC20(lpToken0).balanceOf(address(this));
    uint256 lp1Bal = IERC20(lpToken1).balanceOf(address(this));
    tokens[0] = ITridentRouter.TokenInput(lpToken0, true, lp0Bal);
    tokens[1] = ITridentRouter.TokenInput(lpToken1, true, lp1Bal);
    bytes memory data = abi.encode(address(this));
    ITridentRouter(unirouter).addLiquidity(tokens, want, 1, data);
}
```

### setHarvestOnDeposit()

대부분의 Nesto Vault는 [예치금으로 수확합니다](https://docs.beefy.finance/products/vaults#what-is-harvesting-on-deposit) . 즉, 사용자의 자금이 전략에 들어가기 전에 전체 Vault의 수익이 수집되어 재투자됩니다. 이것은 새로운 예금자가 기존 예금자의 수익률을 훔치는 것을 방지합니다. 결과적으로 입금 시 수확하도록 설정된 모든 Vault는 인출 수수료를 완전히 제거할 수 있습니다.

_harvestOnDeposit_ 은 저장소가 예치금에서 수확할 때 true로 설정되는 부울 변수입니다. 이는 아래에 설명된 _setHarvestOnDeposit() 함수에 의해 토글됩니다._

```
bool public harvestOnDeposit;

function setHarvestOnDeposit(bool _harvestOnDeposit) external onlyManager {
    harvestOnDeposit = _harvestOnDeposit;
    if (harvestOnDeposit) {
        setWithdrawalFee(0);
    } else {
        setWithdrawalFee(10);
    }
}
```

### 입금 전전()

활성화된 경우 예치금 수확을 촉진하는 데 사용되는 외부 기능입니다. 수확하기 전에 예금에 대한 수확이 활성화되어 있고 호출자가 Vault인지 먼저 확인합니다.

```
function beforeDeposit() external override {
    if (harvestOnDeposit) {
        require(msg.sender == vault, "!vault");
        _harvest(tx.origin);
    }
}
```

### 공황()

Nesto는 프로토콜에 보관된 사용자 자금을 직접 건드리지 않습니다. _불확실하거나 기본 수익률 농장으로 업그레이드하는 동안 Nesto는 제3자 컨트렉트에서 모든 자금을 인출하고 panic() 기능_ 을 사용하여 전략에 안전하게 보관할 수 있습니다 . 전략을 "패닉"함으로써 사용자는 지연이나 제3자 위험에 노출되지 않고 Vault에서 자금을 인출할 수 있습니다. 이 기능은 또한 UniRouter와 기본 이자 농사 컨트렉트에 대한 모든 허용치를 제거하여 해당 컨트렉트로 자금이 인출되지 않도록 합니다.

```
function pause() public onlyManager {
    _pause();
    _removeAllowances();
}
```

### 일시정지() / 일시정지해제()

모든 Nesto 전략은 일시 중지할 수 있습니다. 즉, 전략 관리자가 전략의 일반 작업 중에 기능을 중지할 수 있습니다. 이는 [StratManager.sol 을](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/strategies/Common/StratManager.sol) 통해 상속되며 표준 [Pausable.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/Pausable.sol) 추상 컨트렉트에 의존합니다. 이 기능은 또한 UniRouter와 기본 이자 농사 컨트렉트에 대한 모든 허용치를 제거하여 해당 컨트렉트로 자금이 인출되지 않도록 합니다.

```
function pause() public onlyManager {
    _pause();
    _removeAllowances();
}
```

반대로, 전략은 일시 중지 기능에서 수행된 작업을 반대로 실행하여 일시 중지를 해제할 수도 있습니다.

```
function unpause() external onlyManager {
    _unpause();
    _giveAllowances();
    deposit();
}
```

대부분의 전략 컨트렉트에서 일시 중지의 영향을 받는 기능은 다음과 같습니다.[보증금()](https://docs.beefy.finance/developer-documentation/strategy-contract#deposit),[철회하다()](https://docs.beefy.finance/developer-documentation/strategy-contract#withdraw)그리고[수확하다()](https://docs.beefy.finance/developer-documentation/strategy-contract#harvest).

### \_giveAllowances / \_removeAllowances()

제3자 컨트렉트가 전략에서 자금을 인출하는 데 필요한 권한이 있는지 여부를 제어하기 위해 제3자 컨트렉트로 모든 수당을 설정하고 제거하는 데 사용되는 내부 기능. 관련 컨트렉트는 기본 팜 토큰/ _want() (예: LP 토큰), 전략 출력 토큰(종종 want()_ 와 동일 ), 네이티브 체인 토큰(가스에 사용됨) 및 팜에 사용되는 기본 토큰입니다.&#x20;

```
function _giveAllowances() internal {
    IERC20(want).safeApprove(chef, type(uint).max);
    IERC20(output).safeApprove(unirouter, type(uint).max);
    IERC20(native).safeApprove(unirouter, type(uint).max);
    IERC20(lpToken0).safeApprove(unirouter, 0);
    IERC20(lpToken0).safeApprove(unirouter, type(uint).max);
    IERC20(lpToken1).safeApprove(unirouter, 0);
    IERC20(lpToken1).safeApprove(unirouter, type(uint).max);
}

function _removeAllowances() internal {
    IERC20(want).safeApprove(chef, 0);
    IERC20(output).safeApprove(unirouter, 0);
    IERC20(native).safeApprove(unirouter, 0);
    IERC20(lpToken0).safeApprove(unirouter, 0);
    IERC20(lpToken1).safeApprove(unirouter, 0);
}

```

퇴역전략()

한 전략에서 다른 전략으로의 마이그레이션의 일부로 사용되는 외부 기능. 이것은 모든 자금을 인출하고 Vault로 다시 이체함으로써 전략을 효과적으로 종료합니다. Vault 컨트렉트의 호출에 의해서만 트리거될 수 있습니다.

```
function retireStrat() external {
    require(msg.sender == vault, "!vault");
    IMiniChefV2(chef).emergencyWithdraw(poolId, address(this));
    uint256 wantBal = IERC20(want).balanceOf(address(this));
    IERC20(want).transfer(vault, wantBal);
}
```
