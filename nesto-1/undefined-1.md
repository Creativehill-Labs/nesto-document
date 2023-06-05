# GaugeStaker 컨트랙트

게이지스테이커(GaugeStaker)란?

GaugeStaker [컨트랙트는](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/strategies/Gauge/GaugeStaker.sol) [neSPIRIT ](../undefined-1/nesto-escrowed/nespirit.md)토큰의 중앙 스마트컨트랙트로 inSPIRIT 보유자에 대한 SpiritSwap 프로토콜 수익 획득과 inSPIRIT farm boost를 다른 Nesto SpiritSwap Vault로 전달하는 작업을 모두 관리합니다.

GaugeStaker는 어떻게 작동합니까?

GaugeStaker에는 세 가지 중요한 역할이 있습니다. (1) inSPIRIT 보상을 축적하기 위해 투자자 SPIRIT 예금을 관리합니다. (2) SpiritSwap boosted farm 에 대한 투표체계를 관리합니다. (3) 다른 Nesto SpiritSwap 전략과 boosted farming gauges 간에 토큰을 전달합니다.

투자자가 SPIRIT를 입금하면 GaugeStaker는 자동으로 SpiritSwap으로 SPIRIT을 스테이킹하여 양도할 수 없는 inSPIRIT를 받습니다. 스테이킹된 모든 SPIRIT도 잠금상태(인출불가, 락업)이어야 하므로, GaugeStaker는 가능한 한 가장 긴 기간(현재 4년) 동안 모든 예치금을 잠가 최대 양의 inSPIRIT를 받습니다. 프로토콜 수익 보상은 잠긴(locked) SPIRIT에서 지속적으로 발생하며 GaugeStaker는 이러한 보상을 자동으로 청구하고 정기적으로 [Nesto neFTM Vault에 반환하여 자동으로 재투자합니다.](https://app.beefy.finance/#/vault/beefy-binspirit)

inSPIRIT 보유자는 SpiritSwap 거버넌스와 burst된 팜 리워드(farm rewards) 분배에 대한 [투표권을 보유하므로 GaugeStaker가 이러한 투표 할당을 관리합니다.](https://docs.beefy.finance/products/beefy-escrowed-tokens/binspirit#can-i-vote-with-binspirit)

마지막으로, inSPIRIT 보유자는 일부 SpiritSwap 농장에서 더 많은 보상을 받았습니다. 모든 burst된 Nesto SpiritSwap Vault는 게이지스테이커를 통해 모든 예금, 인출 및 보상 수확을 전달하여 burst의 혜택을 받도록 구성됩니다. GaugeStaker는 Nesto의 축적된 inSPIRIT의 전체 집중도를 유지하기 때문에 각 농장에서 가능한 가장 높은 Burst를 제공합니다.

## 게이지스테이커의 기능(GaugeStaker Functionality)

GaugeStaker 컨트랙트는 두 가지 역할을 실행하기 위해 다양한 기능과 방법을 통합합니다. 여기에는 다음이 포함됩니다.

### neSPIRIT 발행(mint)을 위한 SPIRIT 입금

투자자는 SPIRIT( `want`)을 입금할 수 있으며 컨트랙트는 이체 전후 잔액을 확인하여 입금된 금액을 확인합니다. 받은 금액이 0이 아닌 경우 SPIRIT에 대한 기존 락업(lock)이 존재하는지 확인합니다. 락업이 이전에 시작되지 않았거나 만료를 위해 잔존하지 않는 한 락업은 발생할 수 있습니다. 만약 락업이 존재하고 현재 락업 시간이 4년 보다 적다면 최대 4년까지 연장되며, 수령한 SPIRIT의 잔액은 1:1 금액의 inSPIRIT을 얻기 위해 락업됩니다. 현재 락업이 존재하지 않는 경우 새 락업을 생성하고 컨트랙트에서 SPIRIT의 잔액을 락업합니다. 마지막으로 투자자로부터 받은 SPIRIT 잔액과 동일한 양의 neSPIRIT를 발행합니다.

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

### 어떤 게이지를 Burst할 지 투표

Nesto Keeper는 GaugeStaker의 inSPIRIT 잔액을 투표 권한으로 사용하여 게이지 인센티브에 투표할 수 있습니다. 해당 잔액은 주로 Nesto 및 전략적 파트너의 게이지에 대한 투표에 사용되며, Nesto DAO가 게이지에 대한 다양한 인센티브에 투표하도록 관리할 것입니다. 투표 기능은 투표를 기록하고 게이지 인센티브의 분배를 결정하는 SpiritSwap의 게이지 프록시 컨트랙트에 대한 간단한 호출입니다. Nesto Keeper는 매개변수 배열을 사용하여 단일 호출로 여러 게이지 간에 투표권을 분할할 수 있습니다.

```
// vote on boosted farms
function vote(address[] calldata _tokenVote, uint256[] calldata _weights) external onlyManager {    
    gaugeProxy.vote(_tokenVote, _weights);    
    emit Vote(_tokenVote, _weights);
}
```

### 전략(strategies)과 게이지(gauges) 사이의 토큰 전달

Nesto의 SpiritSwap Vault에 대한 전략은 GaugeStaker의 입출금과 수확(harvests)을 거쳐야 합니다. 화이트리스트에 있는 전략만 GaugeStaker와 상호 작용할 수 있으며 각 게이지에는 최대 하나의 전략이 할당됩니다.

입출금은 게이지( `_underlying`)에 할당된 토큰에서 요청한 정확한 금액(`_amount`)을 통해 이루어집니다. 수확(`claimGaugeReward`) 은 보상을 청구할 때 GaugeStaker가 받는 SPIRIT(`want`) 보상만 통과하며 GaugeStaker의 기존 잔액은 무시합니다. 어떤 자금도 GaugeStaker에 보관되지 않으며 항상 동일한 거래로 전달됩니다.

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

inSPIRIT를 보유하면 GaugeStaker에게 SpiritSwap 프로토콜 수수료의 일부를 청구할 권리가 부여되며, 이는 보상 풀(reward pool)의 neSPIRIT 스테이커에게 분배됩니다. 프로토콜 수수료는 일주일에 한 번 SPIRIT의 형태로 분배되며 수수료분배 컨트랙트(the Fee Distributor contract)에서 청구해야 합니다. 보상 풀 컨트랙트를 통해 청구 기능을 호출합니다 `claimVeWantReward()`. 청구할 수 있는 항목이 있는 경우 SPIRIT(`want`) 보상만 즉시 보상 풀로 전달됩니다.

```
// pass through rewards from the fee distributor
function claimVeWantReward() external onlyRewardPool {    
    uint256 _before = balanceOfWant();    
    feeDistributor.claim();    
    uint256 _balance = balanceOfWant().sub(_before);    
    want.safeTransfer(msg.sender, _balance);
}
```

### 전략을 화이트리스트에 추가(Whitelisting strategies)

새 전략과 동일한 게이지에 자금이 투자되어 활성화된(active) 전략이 없는 경우, Nesto Keeper는 전략의 주소를 화이트리스트에 추가할 수 있습니다. 동일한 게이지에 대한 새 전략을 테스트하기 전에 이전 전략이 패닉 상태(panicked)에 있어야, 투자자 자금이 항상 보호됩니다. 게이지에 할당된 토큰(`_want`)에 대한 승인이 재설정(reset)되어, 게이지에서 지출할 수 있는 최대 한도까지 증가합니다. 게이지는 화이트리스트에 있는 전략에 매핑되고 해당 전략은 지정된 게이지에 대해 GaugeStaker에 액세스할 수 있습니다.

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

### 전략을 업그레이드

게이지를 완전히 테스트하면, 기존 전략을 사용하는 새로운 게이지 전략을 제안할 수 있습니다. 스위치가 성공하려면 GaugeStaker에서 `upgradeStrat()`보다 먼저 Vault에서 `proposeStrategy()`를 호출해야 합니다. 새 전략은 이전 전략과 동일한 게이지를 가져야 합니다. `upgradeStrategy()`는 이전 전략의 `retireStrat()`에서만 호출되므로 보관소(vault)의 전략 주소를 업그레이드하여 보관소 소유자가 간접적으로 제어합니다.

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

### 컨트랙트

neSPIRIT/GaugeStaker: 0x44e314190D9E4cE6d4C0903459204F8E21ff940A

Reward Pool : 0xFAE44b30F6F9BbD44E6B7687471dd73D71FaBDC6

중요한 기능은 항상 다중 서명 트랜잭션 및 타임락을 통해 관리합니다. 어떤 자금도 GaugeStaker에 직접 저장되지 않으며 활성화된 화이트리스트 전략만 게이지의 투자된 자금과 상호 작용할 수 있습니다.
