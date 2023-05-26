# 보관소(Vaults) 계약

Nesto [Vault 컨트렉트는](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/vaults/BeefyVaultV7.sol) Nesto 프로토콜의 중앙 사용자 대면 구현으로, 사용자 예치금을 수락 및 관리하고 인출을 용이하게 하기 위한 수령 증명으로 ammToken을 생성합니다. [대체 가능하고 양도 가능한 토큰에 대한 ERC-20 표준을](https://eips.ethereum.org/EIPS/eip-20) 따릅니다 .

예금과 인출을 처리하는 것 외에도 Vault의 주요 기능은 예금된 자금을 관련 자동 복리로 보내는 것입니다.[전략 컨트렉트](https://docs.beefy.finance/developer-documentation/strategy-contract). Vault 및 전략 컨트렉트는 사용자 예치금에서 전략의 모든 위험을 격리하기 위해 별도로 유지됩니다.

## 기능 보기

### want()

Nesto Vault 및 전략 컨트렉트 모두에서 사용되는 기본 팜 토큰(예: LP 토큰)의 주소를 반환합니다. 이는 팜에 사용되는 기본 자산과 동일하지 않습니다.

```
function want() public view returns (IERC20Upgradeable) {
    return IERC20Upgradeable(strategy.want());
}
```

### balance()

Vault와 전략 및 수익 소스에 저장된 "원"(예: 기본 팜 토큰)의 양을 정수로 반환합니다.

```
function balance() public view returns (uint) {
    return want().balanceOf(address(this)) + IStrategyV7(strategy).balanceOf();
}
```

### available()

Vault에만 정수로 저장된 "원하는"(예: 기본 팜 토큰)의 양을 반환합니다.

```
function available() public view returns (uint256) {
    return want().balanceOf(address(this));
}
```

### totalSupply()

발행된 ammTokens의 총량을 정수로 반환하며 항상 18자리 토큰으로 표시됩니다. 이것은 ERC-20 표준에서 상속된 표준 방법입니다. 보다[ammToken이 무엇인가요?](https://docs.beefy.finance/products/vaults#what-are-mootokens)상세 사항은.

```
함수 totalSupply () 공개 보기 가상 재정의 반환 ( uint256 ) {    
    반환 _totalSupply ;
}
```

### getPricePerFullShare()

Vault의 주당 현재 가격(예: ammToken당)을 "원함"(예: 기본 팜 토큰)에 지정된 정수로 반환합니다. _전체 주당 가격_ =[균형()](https://docs.beefy.finance/developer-documentation/vault-contract#balance)_/_[총공급()](https://docs.beefy.finance/developer-documentation/vault-contract#totalsupply).

```
function totalSupply() public view virtual override returns (uint256) {
    return _totalSupply;
}
```

### strategy()

Vault가 수익을 생성하는 데 사용하는 현재 기본 전략 컨트렉트의 주소를 반환합니다.

```
function strategy() external view returns (address);
```

## 쓰기 기능

### deposit()

예금자로부터 Vault로 지정된 양의 "원하는"(예: 기본 팜 토큰) 전송을 실행한 다음 그에 대한 대가로 예금자에게 ammTokens의 비례 수량을 발행합니다.

```
function deposit(uint _amount) public nonReentrant {
    strategy.beforeDeposit();
    uint256 _pool = balance();
    want().safeTransferFrom(msg.sender, address(this), _amount);
    earn();
    uint256 _after = balance();
    _amount = _after - _pool; // Additional check for deflationary tokens
    uint256 shares = 0;
    if (totalSupply() == 0) {
        shares = _amount;
    } else {
        shares = (_amount * totalSupply()) / _pool;
    }
    _mint(msg.sender, shares);
}
```

또한 거래 시 사용자의 지갑에 "want"의 전체 잔액을 입금하는 도우미 함수 _depositAll()이_ 있습니다 .

### withdraw()

예금자로부터 ammTokens의 지정된 \_amount의 소각을 실행한 다음 비례 수량의 "원하는"(예: 기본 팜 토큰)을 예금자에게 전송합니다.

```
function withdraw(uint256 _shares) public {
    uint256 r = (balance() * _shares) / totalSupply();
    _burn(msg.sender, _shares);
    uint b = want().balanceOf(address(this));
    if (b < r) {
        uint _withdraw = r - b;
        strategy.withdraw(_withdraw);
        uint _after = want().balanceOf(address(this));
        uint _diff = _after - b;
        if (_diff < _withdraw) {
            r = b + _diff;
        }
    }
    want().safeTransfer(msg.sender, r);
}
```

유사하게[보증금()](https://docs.beefy.finance/developer-documentation/vault-contract#deposit)_,_ 트랜잭션 시점에 사용자의 지갑에 있는 ammTokens의 전체 잔액을 인출하는 helper 함수 _withdrawAll()_ 이 있습니다 .

### earn()

전송을 실행합니다[사용 가능()](https://docs.beefy.finance/developer-documentation/vault-contract#available)Vault 컨트렉트에서 전략 컨트렉트로 "원"(예: 기본 팜 토큰)하고 전략의 _deposit()_ 기능을 트리거하여 자금을 배포하고 수익을 시작합니다.

```
function earn() public {
    uint _bal = available();
    want().safeTransfer(address(strategy), _bal);
    strategy.deposit();
}
```

### proposeStrat()

다음을 사용하여 현재 전략을 대체 전략으로 업그레이드할 것을 예상하여 대체 전략의 주소를 Vault 컨트렉트의 메모리에 씁니다.[업그레이드전략()](https://docs.beefy.finance/developer-documentation/vault-contract#upgradestrat).

```
function proposeStrat(address _implementation) public onlyOwner {
    require(address(this) == IStrategyV7(_implementation).vault(), "Proposal not valid for this Vault");
    require(want() == IStrategyV7(_implementation).want(), "Different want");
    stratCandidate = StratCandidate({
        implementation: _implementation,
        proposedTime: block.timestamp
    });
    emit NewStratCandidate(_implementation);
}
```

### upgradeStrat()

현재 전략의 주소를 다음에 지정된 대체 전략으로 바꿉니다.[프로포즈스트랫()](https://docs.beefy.finance/developer-documentation/vault-contract#proposestrat).

```
function upgradeStrat() public onlyOwner {
    require(stratCandidate.implementation != address(0), "There is no candidate");
    require(stratCandidate.proposedTime + approvalDelay < block.timestamp, "Delay has not passed");
    emit UpgradeStrat(stratCandidate.implementation);
    strategy.retireStrat();
    strategy = IStrategyV7(stratCandidate.implementation);
    stratCandidate.implementation = address(0);
    stratCandidate.proposedTime = 5000000000;
    earn();
}
```

## NestoVaultV7.sol

표준 Nesto Vault 컨트렉트의 현재 릴리스는 2022년 8월에 [릴리스된 ](https://github.com/beefyfinance/beefy-contracts/pull/83)[NestoVaultV7.sol](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/vaults/BeefyVaultV7.sol) 입니다 . V7 릴리스는 몇 가지 주요 방식에서 이전 버전보다 개선되었습니다.

* 사용 중단 및 재배포할 필요 없이 라이브 Nesto Vault에 대한 업데이트 및 변경을 용이하게 하기 위해 프록시 패턴을 통해 Vault 업그레이드 기능을 도입했습니다.
* 업그레이드 가능한 전략을 허용하도록 전략 인터페이스를 업데이트했습니다.
* Solidity v0.8에 기능을 통합한 후 일반적으로 폐기된 SafeMath 라이브러리에 대한 의존도를 제거하기 위해 모든 컨트렉트를 수정했습니다.

이와는 별도로 2022년 11월 V7 Vault에 대한 ERC-4646 호환 래퍼 컨트렉트가 출시되어 개발자가 표준화된 Vault 기능 및 인터페이스를 사용하여 Nesto Vault를 프로젝트에 통합할 수 있습니다. 보다 자세한 내용은 [NestoWrapper 컨트랙트](https://docs.beefy.finance/developer-documentation/other-beefy-contracts/beefywrapper-contract) 확인하세요
