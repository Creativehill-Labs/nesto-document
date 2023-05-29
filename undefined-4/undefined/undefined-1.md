# 가스 비용 제한 계약

GasFeeThrottler [컨트랙트](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/utils/GasFeeThrottler.sol) (이전의 GasThrottler)은 하위 컨트렉트 트랜잭션의 가스 가격이 항상 고정된 최대값 아래로 떨어지거나 그렇지 않으면 트랜잭션이 되돌려지도록 하는 데 사용되는 스마트 컨트렉트 장치입니다. [이를 위해 특정 GasPrice 컨트렉트을](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/utils/GasPrice.sol) 가리키며 컨트렉트 소유자가 최대 가스 가격을 구성할 수 있습니다.

## GasFeeThrottler.sol

스로틀러 컨트렉트에는 하위 컨트렉트에 의해 상속되는 세 가지 주요 요소가 있습니다.

1. shouldGasThrottle **변수** - 하위 컨트렉트가 가스 조절을 상속했음을 나타내기 위해 _"true" 로 고정된 부울입니다._
2. gasPrice **변수** - 스로틀러를 다음에 연결합니다.[GasPrice.sol](https://docs.beefy.finance/developer-documentation/strategy-contract/gasfeethrottler-contract#gasprice.sol)해당 컨트렉트에 의해 고정된 최대 가스 가격을 식별하기 위해 그리고
3. gasThrottle **() 수정자** - 하위 컨트렉트의 수정된 기능에서 발생하는 트랜잭션의 가스 가격이 항상 고정된 최대 가스 가격 이하이어야 합니다.

```
contract GasFeeThrottler {

    bool public shouldGasThrottle = true;

    address public gasprice = address(0xA43509661141F254F54D9A326E8Ec851A0b95307);

    modifier gasThrottle() {
        if (shouldGasThrottle && Address.isContract(gasprice)) {
            require(tx.gasprice <= IGasPrice(gasprice).maxGasPrice(), "gas is too high!");
        }
        _;
    }
}
```

### GasPrice.sol

GasPrice 컨트렉트는 그 목적을 용이하게 하기 위해 세 가지 핵심 요소를 제공합니다.

1. maxGasPrice **변수** - 컨트렉트에 의해 설정된 현재 최대 가스 가격 값을 저장하고 [IGasPrice.sol](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/utils/IGasPrice.sol) 인터페이스를 사용하여 외부에서 호출할 수 있습니다.
2. NewMaxGasPrice **이벤트** - maxGasPrice 변수에 대한 변경 시 트리거되고 외부 참조를 위해 이전 가격과 새 가격을 반환합니다. 그리고
3. setMaxGasPrice **함수** - 새 \_maxGasPrice에 대한 정수 값을 인수로 받아들이고 NewMaxGasPrice 이벤트를 내보내고 maxGasPrice 변수를 업데이트합니다.

```
contract GasPrice is Ownable {

    uint public maxGasPrice = 10000000000;

    event NewMaxGasPrice(uint oldPrice, uint newPrice);

    function setMaxGasPrice(uint _maxGasPrice) external onlyOwner {
        emit NewMaxGasPrice(maxGasPrice, _maxGasPrice);
        maxGasPrice = _maxGasPrice;
    }
}
```
