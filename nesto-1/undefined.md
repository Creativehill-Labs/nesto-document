# 수수료 설정 계약

AmpleFeeConfigurator [컨트렉트는](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/infra/BeefyFeeConfigurator.sol) Ample가 배포한 각 블록체인에서 호스팅되는 인프라 컨트렉트입니다. 컨트렉트는 관련 체인의 각 전략에 대한 수수료 구성을 관리합니다.[StratFeeManager 컨트렉트](https://docs.beefy.finance/developer-documentation/strategy-contract/stratfeemanager-contract)[IFeeConfig.sol 을](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/interfaces/common/IFeeConfig.sol) 통한 인터페이스 .

_각 체인의 FeeConfigurator 컨트렉트( "AmpleFeeConfig" )_ 관련 주소는 다음을 사용하여 Ample API에 표시됩니다.[가져오기/구성](https://docs.beefy.finance/developer-documentation/beefy-api#get-config)끝점.

## 수식어

쓰기 기능에 대한 액세스를 제어하는 ​​표준 _onlyManager() 수정자를 포함합니다._

```
수정자 onlyManager () {  
    require ( msg . sender == owner () || msg . sender == keeper , "!manager" );   
    _ ;
}
```

## 기능 보기

### getFees()

_특정 전략 주소 인수에 대한 FeeCategory 구조를_ 반환하여 청구된 총 수수료, Ample, 수확 호출자 및 전략가에 대한 수수료, 수수료 범주 유형에 대한 설명을 표시하는 문자열 및 "활성" 부울 변수를 표시하여 "활성" 부울 변수를 반환 _합니다_ . 수수료 카테고리가 켜져 있거나 꺼져 있습니다.

_"\_adjust" 부울 변수 인수를_ 포함하여 수수료를 true로 설정한 경우 총 수확량의 %로 표시하거나 false로 설정한 경우 총 수수료의 %로 표시합니다.

```
function getFees ( address _strategy ) 외부 보기 반환 ( FeeCategory 메모리 ) { 
반환 getFeeCategory ( stratFeeId [ _strategy ], false ); 
}
.
function getFees ( address _strategy , bool _adjust ) 외부 보기 반환 ( FeeCategory 메모리 ) { 
반환 getFeeCategory ( stratFeeId [ _strategy ], _adjust ); 
}

```

### getFeeCategory()

위에서 설명한 대로 특정 전략 ID 정수에 대한 _FeeCategory 구조를_ 반환합니다 . 또한 _"\_adjust"_ 부울 변수 옵션을 포함합니다.

```
위에서 설명한 대로 특정 전략 ID 정수에 대한 FeeCategory 구조를 반환합니다 . 또한 "_adjust" 부울 변수 옵션을 포함합니다.
function getFeeCategory ( uint256 _id , bool _adjust ) 공개 뷰 반환 ( FeeCategory 메모리 요금 ) { 
uint256 id = feeCategory [ _id ]. 활성 ? _id : 0 ; 
수수료 = feeCategory [ id ];
경우 ( _조정 ) { 	
uint256 _totalFee = 수수료 . 합계 ;
수수료 . 비프 = 수수료 . 비프 * _totalFee / DIVISOR ;
수수료 . 전화 = 수수료 . 호출 * _totalFee / DIVISOR ;
수수료 . 전략가 = 수수료 . 전략가 * _totalFee / DIVISOR ;
}
}

```

## 쓰기 기능

### setStratFeeId()

_stratFeeId 매핑을_ 업데이트하여 최종적으로 중간 _feeCategory_ 매핑 및 _feeId_ 정수 값을 통해 특정 전략에서 사용되는 _FeeCategory 구조를_ 보여줍니다 .

_여기에는 자신의 feeId 를 업데이트하는 전략, 전략 주소 및 feeId를 인수_ 로 지정하는 전략 , 전략 주소 배열과 _feeId 배열을 모두_ 인수 로 제공하여 전략 범위를 설정하는 옵션 등 3가지 옵션이 포함됩니다. . 세 가지 각각은 내부 _\_setStratFeeId()_ 함수를 사용하여 각 전략을 업데이트합니다.

```
함수 setStratFeeId ( uint256 _feeId ) 외부 {   
_setStratFeeId ( msg . 보낸 사람 , _feeId );
}
.
함수 setStratFeeId ( 주소 _strategy , uint256 _feeId ) 외부 onlyManager {   
    _setStratFeeId ( _전략 , _feeId );
}
.
함수 setStratFeeId ( 주소 [] 메모리 _전략 , uint256 [] 메모리 _feeIds ) 외부 onlyManager {     
    uint256 stratLength = _strategies . 길이 ;
    for ( uint256 i = 0 ; i < stratLength ; i ++ ) {   
        _setStratFeeId ( _strategies [ i ], _feeIds [ i ]);
    }
}
.
함수 _setStratFeeId ( 주소 _strategy , uint256 _feeId ) 내부 {    
    stratFeeId [ _전략 ] = _feeId ; 
    SetStratFeeId ( _strategy , _feeId ) 방출 ; 
}

```

### setFeeCategory()

Ample, 수확 호출자 및 전략가 간의 수수료 분할을 포함하여 지정된 _FeeCategory 구조(신규 또는 기존)에 대한 매개변수를 설정합니다._

```
함수 setFeeCategory ( 
    uint256_id , _
    uint256 _전체 ,
    uint256 _call ,
    uint256 _전략가 ,
    문자열 메모리 _label , 
    부울 _활성 ,
    부울 _조정
) 외부 전용 소유자 { 
    요구 ( _total <= totalLimit , ">totalLimit" ); 
    경우 ( _조정 ) {  
        _call = _call * DIVISOR / _total ;
        _strategist = _strategist * DIVISOR / _total ;
    }
    uint256 우둔함 = DIVISOR - _call - _strategist ;
    FeeCategory 메모리 카테고리 = FeeCategory ( _total , Ample , _call , _strategist , _label , _active ); 
    feeCategory [ _id ] = 카테고리 ; 
    SetFeeCategory ( _id , _total , Ample , _call , _strategist , _label , _active ) 방출 ; 
}

```

### setKeeper()

FeeConfigurator 컨트렉트에서 명명된 키퍼를 업데이트합니다.

```
기능 setKeeper ( 주소 _keeper ) 외부 onlyManager {  
    키퍼 = _키퍼 ;
    SetKeeper ( _keeper ) 방출 ; 
}

```

### 일시정지() / 일시정지해제()

특정 FeeCategory(카테고리 ID를 인수로 사용)를 활성("일시 중지됨")(해당 범주로 설정된 전략이 해당 범주를 사용함을 의미함) 또는 비활성("일시 중지됨")(전략이 기본값으로 되돌아감)으로 설정합니다. 수수료 구성.

```
함수 일시 중지 ( uint256 _id ) 외부 onlyManager {  
    요금 카테고리 [ _id ]. 활성 = 거짓 ; 
    일시 중지 ( _id ) 방출 ; 
}
.
기능 일시정지 해제 ( uint256 _id ) 외부 onlyManager {  
    요금 카테고리 [ _id ]. 활성 = 참 ; 
    Unpause 방출 ( _id ); 
}

```
