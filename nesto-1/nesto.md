# NESTO 래퍼 계약

NestoWrapper [컨트렉트는](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/vaults/BeefyWrapper.sol) Nesto를 만드는 ERC-4626 어댑터 인터페이스입니다.[Vault 컨트렉트](https://docs.beefy.finance/developer-documentation/vault-contract)ERC-4626 [표준](https://eips.ethereum.org/EIPS/eip-4626) 과 호환됩니다 . 표준의 구성 가능성을 잠금 해제하고 추가 어댑터 및 플러그인 없이도 외부 프로토콜을 통해 Nestp Vaults와의 원활한 인터페이스 및 상호 작용을 가능하게 합니다.

이 페이지에서는 ERC-4626 표준의 일부 배경과 NestoWrapper 컨트렉트의 기능을 설명합니다.

왜 ERC-4626인가?

ERC-4626 표준의 목적은 DeFi에서 발견되는 Vault 디자인의 다양성으로 인해 발생하는 문제를 해결하는 것입니다. 많은 프로토콜이 고유한 아키텍처 및 사용 사례에 맞게 휠을 재창조하여 Vault 개념을 아키텍처에 통합했습니다. 즉, 다양한 Vault를 구현하려는 외부 프로젝트는 코드를 조정하고 플러그하여 각 프로토콜의 고유한 Vault 디자인의 단점과 조화를 이루어야 합니다.

새로운 표준은 대부분의 Vault에서 공통 기능을 인식하고 이 기능을 단일 표준으로 통합하면 대부분의 Vault에서 작동하는 데 필요한 적응 및 플러그인의 양을 줄이는 데 도움이 될 수 있음을 제안합니다. Nesto와 같은 프로토콜의 경우 Nesto Vault에 ERC-4626을 채택하면 제품을 기반으로 하는 신제품 개발을 촉진하는 데 도움이 되며 결과적으로 사용자가 사용할 수 있는 사용 사례의 범위를 늘릴 수 있습니다.

### 컨트렉트 기능

NestoWrapper 컨트렉트의 기능은 호출자의 Nesto Vault 토큰 전송에 대한 대가로 래핑된 Nesto Vault 토큰의 발행 및 소각을 용이하게 합니다. 또한 래핑된 Nesto Vault 토큰의 주조 및 소각에 대한 대가로 기본 Nesto Vault에 대한 예금을 용이하게 하기 위해 표준 입금 및 인출 기능을 무시합니다.

### 래핑()

호출자의 지정된 양의 Nesto Vault 토큰을 래퍼 컨트렉트로 전송하여 동일한 양의 래핑된 Nesto Vault 토큰을 호출자에게 발행합니다.

```
// 일정량의 Vault 공유 토큰을 래핑합니다.
/// "amount" 매개변수는 래핑할 Vault 공유 토큰의 총량입니다.
.
함수 랩 ( uint256 amount ) public { 
.
// 지정된 양의 발신자의 Nesto Vault 토큰을 래퍼로 전송합니다.
IERC20업그레이드 가능 ( Vault ). safeTransferFrom ( msg . 보낸 사람 , 주소 ( 이 ), 금액 ); 
// 호출자에게 래핑된 지정된 양의 Nesto Vault 토큰을 생성합니다.
_mint ( 메시지 . 보낸 사람 , 금액 );
}

```

### wrapAll()

wrap() 함수를 사용하지만 호출자의 전체 잔액을 "amount" 매개변수로 사용합니다.

```
// 호출자가 소유한 모든 Vault 공유 토큰을 래핑합니다.
.
함수 wrapAll () 외부 { 
wrap ( IERC20Upgradeable ( Vault ) .balanceOf ( msg.sender ) ) ;
}
```

### 풀다()

호출자의 래핑된 Nesto Vault 토큰의 지정된 양을 소각하여 동일한 양의 래핑되지 않은 토큰을 래퍼 컨트렉트에서 호출자에게 다시 전송합니다.

```
// 일정량의 Vault 공유 토큰을 풉니다.
/// "amount" 매개변수는 언래핑할 Vault 공유 토큰의 총량입니다.
.
함수 풀기 ( uint256 양 ) public { 
.
// 발신자의 래핑된 Nesto Vault 토큰의 지정된 양을 소각합니다.
_burn ( 메시지 . 보낸 사람 , 금액 );
// 지정된 양의 Nesto Vault 토큰을 호출자에게 다시 전송합니다.
IERC20업그레이드 가능 ( Vault ). safeTransfer ( msg . 보낸 사람 , 금액 );
}

```

### 언랩올()

unwrap() 함수를 활용하지만 호출자의 전체 잔액을 "amount" 매개변수로 사용합니다.

```
// 호출자가 소유한 모든 래핑된 토큰을 언래핑합니다.
.
함수 unwrapAll () 외부 { 
unwrap ( balanceOf ( msg.sender ) ) ;
}

```

### \_보증금()

래핑되지 않은 버전 대신 래퍼 컨트렉트 및 발급된 래핑된 Nesto Vault 토큰과 상호 작용하도록 표준 \_deposit() 함수를 재정의합니다. 그렇지 않으면 Nesto Vault로의 일반적인 전송을 용이하게 합니다.

```
// 자산을 Vault에 예치하고 동일한 수의 래핑된 토큰을 Vault 공유에 발행합니다.
/// "caller" 매개변수는 자산 발신자의 주소입니다.
/// "수신자" 매개변수는 래핑된 토큰의 수신자 주소입니다.
/// "assets" 매개변수는 예치되는 자산의 양입니다.
/// "shares 매개변수는 발행되는 주식의 양입니다.
.
함수 _deposit ( 주소 호출자 , 주소 수신자 , uint256 자산 , uint256 공유 ) 내부 가상 재정의 { 
.
// 호출자의 토큰을 래퍼로 전송합니다.
IERC20업그레이드 가능 ( 자산 ()). safeTransferFrom ( 호출자 , 주소 ( this ), 자산 ); 
uint balance = IERC20Upgradeable ( Vault ). balanceOf ( 주소 ( 이 )); 
// 호출자의 토큰을 Nesto Vault에 보관합니다.
IVault ( Vault ). 예금 ( 자산 );
.
// 수신자에게 래핑된 토큰을 생성합니다.
공유 = IERC20Upgradeable ( 저장소 ). balanceOf ( 주소 ( 이 )) - 잔액 ; 
_mint ( 수신자 , 공유 );
.
// 입금 성공을 알리는 Deposit 이벤트를 발생시킵니다.
Deposit ( 발신자 , 수신자 , 자산 , 공유 ) 방출 ; 
}

```

총 자산()

Vault가 보유한 총 자산을 가져오도록 표준 totalAssets() 함수를 재정의합니다.

```
// 총 자산을 uint256 값으로 가져와서 반환합니다.
.
함수 totalAssets () 공개 보기 가상 재정의 반환 ( uint256 ) { 
.
IVault ( Vault )를 반환합니다 . 균형 (); 
.
}

```

### 총공급()

표준 totalSupply() 함수를 재정의하여 Vault의 총 공유 문제를 가져옵니다.

```
// 총 Vault 공유를 uint256 값으로 가져와서 반환합니다.
.
함수 totalSupply () 공개 보기 가상 재정의 ( ERC20Upgradeable , IERC20Upgradeable ) 반환 ( uint256 ) { 
.
IERC20Upgradeable ( Vault )을 반환합니다 . 총공급 (); 
}

```

## NestoWrapperFactory 컨트렉트

NestoWrapperFactory [컨트렉트는](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/vaults/BeefyWrapperFactory.sol) 새로운 Nesto Vault 래퍼를 만드는 데 사용할 최소한의 프록시 패턴을 제공하는 공장 컨트렉트입니다. 공장 컨트렉트를 통해 사용자는 논리가 있는 동일한 구현 컨트렉트를 가리키는 고유한 프록시 컨트렉트를 생성하고 배포할 수 있습니다.

NestoWrapperFactory를 통해 NestoWrapper 컨트렉트를 모든 Vault에 배포할 수 있습니다. 팩토리 컨트렉트에는 하나의 핵심 기능인 clone()만 있습니다.

### 클론()

OpenZeppelin 표준 프록시 템플릿 [ClonesUpgradeable.sol을](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/proxy/ClonesUpgradeable.sol) 사용하여 NestoWrapper 컨트렉트의 복제본인 프록시 컨트렉트를 생성합니다.

```
// 템플릿 인스턴스의 프록시로 새 Nesto Vault 래퍼를 생성합니다.
/// "_vault" 매개변수는 복제된 Nesto Vault입니다.
/// "proxy" 반환은 새로운 프록시된 Nesto Vault 래퍼입니다.
.
함수 복제 ( 주소 _vault ) 외부 반환 ( 주소 프록시 ) { 
// 프록시는 NestoWrapper 컨트렉트 인스턴스의 복제본으로 설정됩니다.
프록시 = 구현 . 클론 ();
// 위에서 설정한 래퍼 프록시를 초기화합니다.
IWrapper ( 프록시 ). 초기화 (
_Vault ,
문자열 . concat ( "W" , IVault ( _vault ). 이름 ()), 
문자열 . concat ( "w" , IVault ( _vault ). 기호 ()) 
);
// ProxyCreated 이벤트를 내보내 성공적인 배포를 나타냅니다.
ProxyCreated ( 프록시 ) 방출 ; 
}

```

### 컨트렉트

템플릿 Nesto Vault 래퍼 컨트렉트는 Nesto의 GitHub 리포지토리에서 공개적으로 유지 관리됩니다. [NestoWrapper.sol](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/vaults/BeefyWrapper.sol) 및 [NestoWrapperFactory.sol 을](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/vaults/BeefyWrapperFactory.sol) 참조하십시오 .

각 Nesto Vault에 대한 래퍼 컨트렉트는 관련 체인에 별도로 배포되며 반드시 유사한 컨트렉트 주소에 배포되지는 않습니다. [Polygon 블록체인에 배포된 샘플 컨트렉트를 테스트하려면 이 NestoWrapper.sol](https://polygonscan.com/address/0x776994eab59b894fb892d08a46329c5077c9e226) 인스턴스 및 이 [NestoWrapperFactory.sol](https://polygonscan.com/address/0xd1cedfb11994ebbc1608ae46d7c7176294bdd599) 인스턴스를 참조하십시오 .

\
