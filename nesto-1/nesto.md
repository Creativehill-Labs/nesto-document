# NESTO 래퍼 계약

NestoWrapper [컨트랙트는](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/vaults/BeefyWrapper.sol) Nesto를 만드는 ERC-4626 어댑터 인터페이스입니다.[Vault 컨트랙트](https://docs.beefy.finance/developer-documentation/vault-contract)ERC-4626 [표준](https://eips.ethereum.org/EIPS/eip-4626) 과 호환됩니다 . 표준의 구성 가능성을 잠금 해제하고 추가 어댑터 및 플러그인 없이도 외부 프로토콜을 통해 Nestp Vaults와의 원활한 인터페이스 및 상호 작용을 가능하게 합니다.

이 페이지에서는 ERC-4626 표준의 일부 배경과 NestoWrapper 컨트랙트의 기능을 설명합니다.

왜 ERC-4626인가?

ERC-4626 표준의 목적은 DeFi에서 발견되는 Vault 디자인의 다양성으로 인해 발생하는 문제를 해결하는 것입니다. 많은 프로토콜이 고유한 아키텍처 및 사용 사례에 맞게 휠을 재창조하여 Vault 개념을 아키텍처에 통합했습니다. 즉, 다양한 Vault를 구현하려는 외부 프로젝트는 코드를 조정하고 플러그하여 각 프로토콜의 고유한 Vault 디자인의 단점과 조화를 이루어야 합니다.

새로운 표준은 대부분의 Vault에서 공통 기능을 인식하고 이 기능을 단일 표준으로 통합하면 대부분의 Vault에서 작동하는 데 필요한 적응 및 플러그인의 양을 줄이는 데 도움이 될 수 있음을 제안합니다. Nesto와 같은 프로토콜의 경우 Nesto Vault에 ERC-4626을 채택하면 제품을 기반으로 하는 신제품 개발을 촉진하는 데 도움이 되며 결과적으로 사용자가 사용할 수 있는 사용 사례의 범위를 늘릴 수 있습니다.

### 컨트랙트 기능

NestoWrapper 컨트랙트의 기능은 호출자의 Nesto Vault 토큰 전송에 대한 대가로 래핑된 Nesto Vault 토큰의 발행 및 소각을 용이하게 합니다. 또한 래핑된 Nesto Vault 토큰의 주조 및 소각에 대한 대가로 기본 Nesto Vault에 대한 예금을 용이하게 하기 위해 표준 입금 및 인출 기능을 무시합니다.

### 래핑()

호출자의 지정된 양의 Nesto Vault 토큰을 래퍼 컨트랙트로 전송하여 동일한 양의 래핑된 Nesto Vault 토큰을 호출자에게 발행합니다.

```
// Wraps an amount of vault share tokens.
/// "amount" parameter is the total amount of vault share tokens to be wrapped.

function wrap(uint256 amount) public {

    // Transfers the specified amount of the caller's Nesto Vault tokens to the wrapper.
    IERC20Upgradeable(vault).safeTransferFrom(msg.sender, address(this), amount);
    
    // Mints the specified amount wrapped Nesto Vault tokens to the caller.
    _mint(msg.sender, amount);
}
```

### wrapAll()

wrap() 함수를 사용하지만 호출자의 전체 잔액을 "amount" 매개변수로 사용합니다.

```
// Wraps all vault share tokens owned by the caller.

function wrapAll() external {
    wrap(IERC20Upgradeable(vault).balanceOf(msg.sender));
}
```

### 풀다()

호출자의 래핑된 Nesto Vault 토큰의 지정된 양을 소각하여 동일한 양의 래핑되지 않은 토큰을 래퍼 컨트랙트에서 호출자에게 다시 전송합니다.

```
// Unwraps an amount of vault share tokens.
/// "amount" parameter is the total amount of vault share tokens to be unwrapped.

function unwrap(uint256 amount) public {

    // Burns the specified amount of the caller's wrapped Nesto Vault tokens.
    _burn(msg.sender, amount);
    
    // Transfers the specified amount of Nesto Vault tokens back to the caller.
    IERC20Upgradeable(vault).safeTransfer(msg.sender, amount);
}
```

### 언랩올()

unwrap() 함수를 활용하지만 호출자의 전체 잔액을 "amount" 매개변수로 사용합니다.

```
// Unwraps all wrapped tokens owned by the caller.

function unwrapAll() external {
    unwrap(balanceOf(msg.sender));
}
```

### \_보증금()

래핑되지 않은 버전 대신 래퍼 컨트렉트 및 발급된 래핑된 Nesto Vault 토큰과 상호 작용하도록 표준 \_deposit() 함수를 재정의합니다. 그렇지 않으면 Nesto Vault로의 일반적인 전송을 용이하게 합니다.

```
// Burn wrapped tokens and withdraw assets from the vault.
/// "caller" parameter is the address of the caller of the withdraw.
/// "receiver" parameter is the address of the receiver of the assets.
/// "owner" parameter is the address of the owner of the burnt shares.
/// "assets" parameter is the amount of assets being withdrawn.
/// "shares parameter is the amount of shares being burnt.

function _withdraw(address caller, address receiver, address owner, uint256 assets, uint256 shares) internal virtual override {
    
    // Checks caller is not the contract's owner, and that the caller's spend                 allowance is sufficient for the call.
    if (caller != owner) {
        _spendAllowance(owner, caller, shares);
    }
    
    // Burns the caller's wrapped tokens.
    _burn(owner, shares);

    // Withdraws the caller's assets from the Nesto Vault.
    IVault(vault).withdraw(shares);
    uint balance = IERC20Upgradeable(asset()).balanceOf(address(this));
    if (assets > balance) {
        assets = balance;
    }

    // Transfers the caller's assets back to the receiver.
    IERC20Upgradeable(asset()).safeTransfer(receiver, assets);
    
    // Emits the Withdraw event to signify a successful withdrawal.
    emit Withdraw(caller, receiver, owner, assets, shares);
}
```

총 자산()

Vault가 보유한 총 자산을 가져오도록 표준 totalAssets() 함수를 재정의합니다.

```
// Fetches and returns the total assets as a uint256 value.

function totalAssets() public view virtual override returns (uint256) {

    return IVault(vault).balance();

}
```

### 총공급()

표준 totalSupply() 함수를 재정의하여 Vault의 총 공유 문제를 가져옵니다.

```
// Fetches and returns the total vault shares as a uint256 value.

function totalSupply() public view virtual override(ERC20Upgradeable, IERC20Upgradeable) returns (uint256) {

    return IERC20Upgradeable(vault).totalSupply();
    
}
```

## NestoWrapperFactory 컨트랙트

NestoWrapperFactory [컨트랙트는](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/vaults/BeefyWrapperFactory.sol) 새로운 Nesto Vault 래퍼를 만드는 데 사용할 최소한의 프록시 패턴을 제공하는 공장 컨트랙트입니다. 공장 컨트랙트를 통해 사용자는 논리가 있는 동일한 구현 컨트랙트를 가리키는 고유한 프록시 컨트랙트를 생성하고 배포할 수 있습니다.

NestoWrapperFactory를 통해 NestoWrapper 컨트랙트를 모든 Vault에 배포할 수 있습니다. 팩토리 컨트랙트에는 하나의 핵심 기능인 clone()만 있습니다.

### 클론()

OpenZeppelin 표준 프록시 템플릿 [ClonesUpgradeable.sol을](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/proxy/ClonesUpgradeable.sol) 사용하여 NestoWrapper 컨트랙트의 복제본인 프록시 컨트랙트를 생성합니다.

```
// Creates a new Nesto Vault wrapper as a proxy of the template instance.
/// "_vault" parameter is the cloned Nesto Vault.
/// "proxy" return is the new proxied Nesto Vault wrapper.

function clone(address _vault) external returns (address proxy) {
    
    // Proxy is set as a clone of the instance of the NestoWrapper contract.
    proxy = implementation.clone();
    
    // Initializes the wrapper proxy set above.
    IWrapper(proxy).initialize(
        _vault,
        string.concat("W", IVault(_vault).name()),
        string.concat("w", IVault(_vault).symbol())
    );
    
    // Emits the ProxyCreated event to signify a successful deployment.
    emit ProxyCreated(proxy);
}

```

### 컨트랙트

템플릿 Nesto Vault 래퍼 컨트랙트는 Nesto의 GitHub 리포지토리에서 공개적으로 유지 관리됩니다. [NestoWrapper.sol](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/vaults/BeefyWrapper.sol) 및 [NestoWrapperFactory.sol 을](https://github.com/beefyfinance/beefy-contracts/blob/master/contracts/BIFI/vaults/BeefyWrapperFactory.sol) 참조하십시오 .

각 Nesto Vault에 대한 래퍼 컨트랙트는 관련 체인에 별도로 배포되며 반드시 유사한 컨트랙트 주소에 배포되지는 않습니다. [Polygon 블록체인에 배포된 샘플 컨트랙트를 테스트하려면 이 NestoWrapper.sol](https://polygonscan.com/address/0x776994eab59b894fb892d08a46329c5077c9e226) 인스턴스 및 이 [NestoWrapperFactory.sol](https://polygonscan.com/address/0xd1cedfb11994ebbc1608ae46d7c7176294bdd599) 인스턴스를 참조하십시오 .

\
