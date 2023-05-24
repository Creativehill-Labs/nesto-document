# 게이지 스테이커 계약

게이지스테이커란?

GaugeStaker [컨트렉트는](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/strategies/Gauge/GaugeStaker.sol) inSPIRIT 보유자를 위한 SpiritSwap 프로토콜 수익 수집과 다른 Nesto SpiritSwap Vault로의 inSPIRIT 팜 Aoost 전달을 모두 관리하는 [ainFTM](https://docs.beefy.finance/products/beefy-escrowed-tokens/binspirit) 토큰 의 중앙 스마트 컨트렉트입니다 .

GaugeStaker는 어떻게 작동합니까?

GaugeStaker에는 세 가지 중요한 역할이 있습니다. (1) inSPIRIT 보상을 축적하기 위해 사용자 SPIRIT 예금을 관리합니다. (2) SpiritSwap Aoost 농장에 대한 투표 관리; (3) 다른 Nesto SpiritSwap 전략과 Aoost 파밍 게이지 간에 토큰을 전달합니다.

사용자가 SPIRIT를 입금하면 GaugeStaker는 자동으로 SpiritSwap으로 SPIRIT를 스테이킹하여 양도할 수 없는 inSPIRIT를 받습니다. 스테이킹된 모든 SPIRIT도 잠금 상태여야 하므로(따라서 인출할 수 없음), GaugeStaker는 가능한 한 가장 긴 기간(현재 4년) 동안 모든 예치금을 잠가 최대 양의 inSPIRIT를 받습니다. 프로토콜 수익 보상은 잠긴 SPIRIT에서 지속적으로 발생하며 GaugeStaker는 이러한 보상을 자동으로 청구하고 정기적으로 [Nesto ainFTM Vault 에 반환하여 자동으로 합산합니다.](https://app.beefy.finance/#/vault/beefy-binspirit)

inSPIRIT 보유자는 SpiritSwap 거버넌스 및 burst된 농장 보상 분배에 [투표 할 자격이 있으므로 GaugeStaker가 이러한 투표 할당을 관리합니다.](https://docs.beefy.finance/products/beefy-escrowed-tokens/binspirit#can-i-vote-with-binspirit)

마지막으로, inSPIRIT 보유자는 일부 SpiritSwap 농장에서 더 많은 보상을 받았습니다. 모든 burst된 Nesto SpiritSwap Vault는 게이지스테이커를 통해 모든 예금, 인출 및 보상 수확을 전달하여 burst의 혜택을 받도록 구성됩니다. 이것은 GaugeStaker가 Nesto 의 축적된 inSPIRIT의 전체 집중을 유지하기 때문에 각 농장에서 가능한 가장 높은 Burst를 제공합니다.

게이지스테이커 기능

GaugeStaker 컨트렉트는 두 가지 역할을 실행하기 위해 다양한 기능과 방법을 통합합니다. 여기에는 다음이 포함됩니다.

민트 ainFTM에 SPIRIT 입금

사용자는 SPIRIT( `want`)을 입금할 수 있으며 컨트랙트는 이체 전후 잔액을 확인하여 입금된 금액을 확인합니다. 받은 금액이 0이 아닌 경우 SPIRIT에 대한 기존 잠금이 존재하는지 확인합니다. 잠금이 이전에 시작되지 않았거나 만료되도록 남아 있지 않는 한 그럴 것입니다. 만약 잠금이 존재한다면 현재 잠금 시간이 전체 금액보다 적다면 전체 4년으로 연장되며, 받은 SPIRIT의 잔액은 1:1 금액의 inSPIRIT을 얻기 위해 잠깁니다. 현재 잠금이 존재하지 않는 경우 새 잠금을 생성하고 컨트렉트에서 SPIRIT의 균형을 잠급니다. 마지막으로 사용자로부터 받은 SPIRIT 잔액과 동일한 양의 ainFTM를 발행합니다.

```
// deposit 'want' and lock
function _deposit(address _user, uint256 _amount) internal nonReentrant whenNotPaused {
    uint256 _pool = balanceOfWant();    
    want.safeTransferFrom(msg.sender, address(this), _amount);
    uint256 _after = balanceOfWant();    
    _amount = _after.sub(_pool); // Additional check for deflationary tokens
    if (_amount > 0) {
        if (balanceOfVe() > 0) {
            increaseUnlockTime();
            veWant.increase_amount(_amount);        
        } else {            
            _createLock();
        }        
        _mint(_user, _amount);        
        emit DepositWant(balanceOfVe());    
    }
}
```

### Burst할 게이지 투표

Nesto Keeper는 GaugeStaker의 inSPIRIT 잔액을 보팅 파워로 사용하여 게이지 인센티브에 투표할 수 있습니다. 주로 Nesto 및 전략적 파트너의 게이지에 대한 투표에 사용되며 Nesto DAO에 의해 관리되어 게이지에 대한 다양한 인센티브에 투표할 수 있습니다. 투표 기능은 투표를 기록하고 게이지 인센티브의 분배를 결정하는 SpiritSwap의 게이지 프록시 컨트렉트에 대한 간단한 호출입니다. Nesto 키퍼는 매개변수 배열을 사용하여 단일 호출에서 여러 게이지 간에 투표권을 분할할 수 있습니다.

```
// vote on boosted farms
function vote(address[] calldata _tokenVote, uint256[] calldata _weights) external onlyManager {    
    gaugeProxy.vote(_tokenVote, _weights);    
    emit Vote(_tokenVote, _weights);
}
```

### 전략과 게이지 사이의 토큰 통과

Nesto의 SpiritSwap Vault에 대한 전략은 GaugeStaker와의 예금, 인출 및 수확을 통과해야 합니다. 화이트리스트에 있는 전략만 GaugeStaker와 상호 작용할 수 있으며 각 게이지에는 최대 하나의 전략이 할당됩니다.

입출금은 `_amount`게이지( `_underlying`)에 할당된 토큰에서 요청한 정확한 금액( )을 통해 이루어집니다. 수확( ) 은 보상을 청구할 때 GaugeStaker가 받는 `claimGaugeReward()`SPIRIT( ) 보상만 통과하며 GaugeStaker의 기존 잔액은 무시합니다. `want`어떤 자금도 GaugeStaker에 보관되지 않으며 항상 동일한 거래에서 전달됩니다.

```
// pass through a deposit to a gauge
function deposit(address _gauge, uint256 _amount) external onlyWhitelist(_gauge) {
    address _underlying = IGauge(_gauge).TOKEN();    
    IERC20Upgradeable(_underlying).safeTransferFrom(msg.sender, address(this), _amount);    
    IGauge(_gauge).deposit(_amount);
}
    
// pass through a withdrawal from a gauge
function withdraw(address _gauge, uint256 _amount) external onlyWhitelist(_gauge) {
    address _underlying = IGauge(_gauge).TOKEN();    
    IGauge(_gauge).withdraw(_amount);    
    IERC20Upgradeable(_underlying).safeTransfer(msg.sender, _amount);
}

// pass through rewards from a gauge
function claimGaugeReward(address _gauge) external onlyWhitelist(_gauge) {
    uint256 _before = balanceOfWant();
    IGauge(_gauge).getReward();
    uint256 _balance = balanceOfWant().sub(_before);
    want.safeTransfer(msg.sender, _balance);
}

```

### SpiritSwap 프로토콜 수수료 청구

inSPIRIT를 보유하면 GaugeStaker에게 SpiritSwap 프로토콜 수수료의 일부를 청구할 권리가 부여되며, 이는 보상 풀의 ainFTM 스테이커에게 분배됩니다. 프로토콜 수수료는 일주일에 한 번 SPIRIT의 형태로 분배되며 수수료 분배자 컨트렉트에서 청구해야 합니다. 보상 풀 컨트렉트는 을 통해 청구 기능을 호출합니다 `claimVeWantReward()`. 청구할 수 있는 항목이 있는 경우 SPIRIT( `want`) 보상만 보상 풀로 즉시 다시 전달됩니다.

```
// pass through rewards from the fee distributor
function claimVeWantReward() external onlyRewardPool {    
    uint256 _before = balanceOfWant();    
    feeDistributor.claim();    
    uint256 _balance = balanceOfWant().sub(_before);    
    want.safeTransfer(msg.sender, _balance);
}
```

### 화이트리스트 전략

Nesto Keeper는 새 전략과 동일한 게이지에 자금이 배치된 활성 전략이 없는 한 전략 주소를 화이트리스트에 추가할 수 있습니다. 동일한 게이지에 대한 새 전략을 테스트하기 전에 이전 전략을 패닉 상태에 빠뜨려야 사용자 자금이 항상 보호됩니다. 게이지에 할당된 토큰( )에 대한 승인이 `_want`재설정되고 게이지별 지출 최대 한도로 증가합니다. 게이지는 화이트리스트에 있는 전략에 매핑되며 전략은 지정된 게이지에 대한 GaugeStaker에 대한 액세스가 허용됩니다.

```
// whitelists a strategy address to interact with the Gauge Staker and gives approvals
function whitelistStrategy(address _strategy) external onlyManager {    
    IERC20Upgradeable _want = IGaugeStrategy(_strategy).want();    
    address _gauge = IGaugeStrategy(_strategy).gauge();    
    require(IGauge(_gauge).balanceOf(address(this)) == 0, '!inactive');    
    _want.safeApprove(_gauge, 0);    
    _want.safeApprove(_gauge, type(uint256).max);    
    whitelistedStrategy[_gauge] = _strategy;
}
```

### 업그레이드 전략

기존 전략이 있는 게이지에 대한 새로운 전략은 완전히 테스트된 후에 제안될 수 있습니다. 스위치가 성공할 수 있도록 Vault에서 `proposeStrategy()`전에 GaugeStaker에서 호출해야 합니다 . `upgradeStrat()`새 전략은 이전 전략과 동일한 게이지를 가져야 합니다. `upgradeStrategy()`이전 전략 에서만 호출되므로 `retireStrat()`저장소의 전략 주소를 업그레이드하여 저장소 소유자가 간접적으로 제어합니다.

```
// prepare a strategy to be retired and replaced with another
function proposeStrategy(address _oldStrategy, address _newStrategy) external onlyManager {    
    require(IGaugeStrategy(_oldStrategy).gauge() == IGaugeStrategy(_newStrategy).gauge(), '!gauge');    
    replacementStrategy[_oldStrategy] = _newStrategy;
}

// switch over whitelist from one strategy to another for a gauge
function upgradeStrategy(address _gauge) external onlyWhitelist(_gauge) {
    whitelistedStrategy[_gauge] = replacementStrategy[msg.sender];
}

```

### 컨트렉트

ainFTM/GaugeStaker: 0x44e314190D9E4cE6d4C0903459204F8E21ff940A

Reward Pool : 0xFAE44b30F6F9BbD44E6B7687471dd73D71FaBDC6

중요한 기능은 항상 다중 서명 트랜잭션 및 타임록을 통해 관리됩니다. 어떤 자금도 GaugeStaker에 직접 저장되지 않으며 활성 화이트리스트 전략만 게이지에 저장된 자금과 상호 작용할 수 있습니다.
