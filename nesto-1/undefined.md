# 수수료 설정 계약

NestoFeeConfigurator [컨트랙트는](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/infra/BeefyFeeConfigurator.sol) Nesto가 배포한 각 블록체인에서 호스팅되는 인프라 컨트랙트입니다. 컨트랙트는 관련 체인의 각 전략에 대한 수수료 구성을 관리하며 [StratFeeManager 컨트랙트](https://docs.beefy.finance/developer-documentation/strategy-contract/stratfeemanager-contract)는 [IFeeConfig.sol 을](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/interfaces/common/IFeeConfig.sol) 통해 인터페이스로 연결됩니다.

_각 체인의 FeeConfigurator_ 컨트랙트_( "NestoFeeConfig" )_ 관련 주소는 [GET/config](https://docs.beefy.finance/developer-documentation/beefy-api#get-config) 엔드포인트를 사용하여 Nesto API에 표시됩니다.

## 제어자

쓰기 기능에 대한 액세스를 제어하는 ​​표준 _onlyManager() 제어자를 포함합니다._

```
modifier onlyManager() {
    require(msg.sender == owner() || msg.sender == keeper, "!manager");
    _;
}
```

## 기능 보기

### getFees()

_특정 전략 주소 인수에 대한 수수료카테고리(FeeCategory) 구조를_ 반환합니다. 청구된 총 수수료, Nesto, 수확 호출자 및 전략가에 대한 수수료, 수수료 카테고리 유형에 대한 설명을 표시하는 문자열 및 수수료 카테고리의 설정 여부를 표시하는 "활성화된"(active) boolean 변수를 표시합니다.

_"\_adjust"_ boolean 변수 인수를 포함합니다. 이 인수는 true로 설정된 경우 총 수확량의 %로 수수료를 표시하고 false로 설정된 경우 총 수수료의 %로 수수료를표시합니다.

```
function getFees(address _strategy) external view returns (FeeCategory memory) {
    return getFeeCategory(stratFeeId[_strategy], false);
}

function getFees(address _strategy, bool _adjust) external view returns (FeeCategory memory) {
    return getFeeCategory(stratFeeId[_strategy], _adjust);
}
```

### getFeeCategory()

위에서 설명한 대로 특정 전략 ID 정수(integer)에 대한 _FeeCategory 구조를_ 반환합니다 . 또한 _"\_adjust"_ boolean 변수 옵션을 포함합니다.

```
function getFeeCategory(uint256 _id, bool _adjust) public view returns (FeeCategory memory fees) {
    uint256 id = feeCategory[_id].active ? _id : 0;
    fees = feeCategory[id];
    if (_adjust) {
        uint256 _totalFee = fees.total;
        fees.Nesto = fees.Nesto * _totalFee / DIVISOR;
        fees.call = fees.call * _totalFee / DIVISOR;
        fees.strategist = fees.strategist * _totalFee / DIVISOR;
    }
}
```

## 쓰기 기능

### setStratFeeId()

_stratFeeId 매핑을_ 업데이트하여 중간 _feeCategory_ 매핑 및 _feeId_ 정수 값을 통해 최종적으로 특정 전략에서 사용되는 _FeeCategory 구조를_ 보여줍니다.

_여기에는 자신의 feeId를 업데이트하는 전략, 전략 주소 및 feeId를 인수_로 지정하는 전략, 전략 주소 배열과 _feeId 배열을 모두_ 인수로 제공하여 전략 범위를 설정하는 옵션 등 3가지 옵션이 포함됩니다. 세 가지 각각은 내부 _\_setStratFeeId()_ 함수를 사용하여 각 전략을 업데이트합니다.

```
function setStratFeeId(uint256 _feeId) external {
_setStratFeeId(msg.sender, _feeId);
}

function setStratFeeId(address _strategy, uint256 _feeId) external onlyManager {
    _setStratFeeId(_strategy, _feeId);
}

function setStratFeeId(address[] memory _strategies, uint256[] memory _feeIds) external onlyManager {
    uint256 stratLength = _strategies.length;
    for (uint256 i = 0; i < stratLength; i++) {
        _setStratFeeId(_strategies[i], _feeIds[i]);
    }
}

function _setStratFeeId(address _strategy, uint256 _feeId) internal {
    stratFeeId[_strategy] = _feeId;
    emit SetStratFeeId(_strategy, _feeId);
}
```

### setFeeCategory()

Nesto, 수확 요청자(호출자) 및 전략가 간의 수수료 분할을 포함하여 지정된 _FeeCategory 구조(신규 또는 기존)에 대한 매개변수를 설정합니다._

```
function setFeeCategory(
    uint256 _id,
    uint256 _total,
    uint256 _call,
    uint256 _strategist,
    string memory _label,
    bool _active,
    bool _adjust
) external onlyOwner {
    require(_total <= totalLimit, ">totalLimit");
    if (_adjust) {
        _call = _call * DIVISOR / _total;
        _strategist = _strategist * DIVISOR / _total;
    }
    uint256 Nesto = DIVISOR - _call - _strategist;
    FeeCategory memory category = FeeCategory(_total, Nesto, _call, _strategist, _label, _active);
    feeCategory[_id] = category;
    emit SetFeeCategory(_id, _total, Nesto, _call, _strategist, _label, _active);
}
```

### setKeeper()

FeeConfigurator 컨트랙트에서 명명된 키퍼(keeper)를 업데이트합니다.

```
function setKeeper(address _keeper) external onlyManager {
    keeper = _keeper;
    emit SetKeeper(_keeper);
}
```

### pause() / unpause()

특정 FeeCategory(카테고리 ID를 인수로 사용)를 활성("진행됨"-카테고리로 설정된 전략이 해당 카테고리를 사용함을 의미) 또는 비활성("일시 중지됨"-전략이 기본 수수료 구성으로 되돌아감)으로 설정합니다.&#x20;

```
function pause(uint256 _id) external onlyManager {
    feeCategory[_id].active = false;
    emit Pause(_id);
}

function unpause(uint256 _id) external onlyManager {
    feeCategory[_id].active = true;
    emit Unpause(_id);
}
```
