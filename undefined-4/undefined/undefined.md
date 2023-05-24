# 전략적 수수료 관리 계약

StratFeeManager [컨트렉트는](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/strategies/Common/StratFeeManagerInitializable.sol) 모든 Nesto에서 가져오고 사용되는 중요한 종속성 모음입니다.[전략 컨트렉트](https://docs.beefy.finance/developer-documentation/strategy-contract).

[원래 이러한 종속성은 StratManager.sol](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/strategies/Common/StratManager.sol) 및 [FeeManager.sol](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/strategies/Common/FeeManager.sol) 의 두 컨트렉트로 분할되었습니다. Solidity V0.8로 이동한 후 이 둘은 단일 컨트렉트인 [StratFeeManager.sol](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/strategies/Common/StratFeeManager.sol) 로 결합되었습니다. 현재 버전인 [StratFeeManagerInitializable.sol](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/strategies/Common/StratFeeManagerInitializable.sol) 은 모든 단일 전략을 개별적으로 배포할 필요가 없도록 프록시 복제 전략(전략 및 해당 종속성에 대한 관련 인수로 초기화해야 함)으로의 이동을 촉진했습니다.

## 종속성

StratFeeManager 컨트렉트는 또한 추가 종속성 자체를 소개합니다. 특히 [Ownable.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol) 은 컨트렉트의 소유자를 설정하고 기능을 소유자에게만 제한하는 기능을 도입하고 [Pausable.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/Pausable.sol) 은 컨트렉트를 일시 중지하여 컨트렉트의 기능을 동결하는 기능을 도입합니다. 두 종속성은 궁극적으로 모든 Nesto 전략 컨트렉트에 포함됩니다.

## 수식어

전략 관리자만 사용할 함수를 제한하는 _onlyManager() 수정자를_ 도입합니다.

```
modifier onlyManager() {
    require(msg.sender == owner() || msg.sender == keeper, "!manager");
    _;
}
```

## 기능 보기

### getFees()

수수료 구성 컨트렉트의 모든 수수료 값을 반환합니다.

```
function getAllFees() external view returns (IFeeConfig.AllFees memory) {
    return IFeeConfig.AllFees(getFees(), depositFee(), withdrawFee());
}
```

### getAllFees()

수수료 구성 컨트렉트의 모든 수수료 값과 동적 입출금 수수료를 반환합니다.

```
function getAllFees() external view returns (IFeeConfig.AllFees memory) {
    return IFeeConfig.AllFees(getFees(), depositFee(), withdrawFee());
}
```

### getStratFeeId()

수수료 구성 컨트렉트에서 전략 수수료 ID의 정수 값을 반환합니다.

```
function getStratFeeId() external view returns (uint256) {
    return beefyFeeConfig.stratFeeId(address(this));
}
```

## 쓰기 기능

### setStratFeeId()

전략에 대한 관련 수수료를 나타내는 전략 수수료 ID의 새 정수 값을 설정합니다.

```
function setStratFeeId(uint256 _feeId) external onlyManager {
    beefyFeeConfig.setStratFeeId(_feeId);
    emit SetStratFeeId(_feeId);
}
```

### setWithdrawalFee()

수확할 때마다 청구되는 컨트렉트의 인출 수수료에 대한 새로운 정수 값을 설정합니다.

```
function setWithdrawalFee(uint256 _fee) public onlyManager {
    require(_fee <= WITHDRAWAL_FEE_CAP, "!cap");
    withdrawalFee = _fee;
    emit SetWithdrawalFee(_fee);
}
```

### setVault()

사용자 자금을 관리하는 컨트렉트 Vault의 새 주소를 설정합니다.

```
function setVault(address _vault) external onlyOwner {
    vault = _vault;
    emit SetVault(_vault);
}
```

### setUnirouter()

컨트렉트 내에서 스왑을 처리하는 컨트렉트 라우터의 새 주소를 설정합니다.

```
function setUnirouter(address _unirouter) external onlyOwner {
    unirouter = _unirouter;
    emit SetUnirouter(_unirouter);
}
```

### setKeeper()

전략을 "패닉"시킬 수 있는 컨트렉트 관리자를 위한 새 주소를 설정합니다.

```
function setKeeper(address _keeper) external onlyManager {
    keeper = _keeper;
    emit SetKeeper(_keeper);
}
```

### setStrategist()

전략가 수수료를 받는 컨트렉트 전략가의 새 주소를 설정합니다.

```
function setStrategist(address _strategist) external {
    require(msg.sender == strategist, "!strategist");
    strategist = _strategist;
    emit SetStrategist(_strategist);
}
```

### setNestoFeeRecipient()

수확에 대한 Nesto의 수수료(일반적으로 Nesto 재무부 컨트렉트)를 받는 사람의 새 주소를 설정합니다.

```
function setBeefyFeeRecipient(address _beefyFeeRecipient) external onlyOwner {
    beefyFeeRecipient = _beefyFeeRecipient;
    emit SetBeefyFeeRecipient(_beefyFeeRecipient);
}
```

### setNestoFeeConfig()

수수료를 가져오기 위해 전략에서 사용하는 수수료 구성 컨트렉트의 새 주소를 설정합니다.

```
function setBeefyFeeConfig(address _beefyFeeConfig) external onlyOwner {
    beefyFeeConfig = IFeeConfig(_beefyFeeConfig);
    emit SetBeefyFeeConfig(_beefyFeeConfig);
}
```
