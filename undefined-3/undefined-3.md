# 자금

Nesto 운영의 중심에는 Core Contributor 팀이 관리하는 Treasury가 있습니다. 우리의 $AMFI 토큰은 나중에 사용하기 위해 예약된 토큰 없이 완전히 배포되기 때문에 우리의 전체 운영은 지속적인 수익 창출 활동으로 자금을 조달합니다. 조명을 켜고 Vault 자동 합성을 유지하기 위해 Nesto는 DAO의 최대 이익을 염두에 두고 책임감 있게 재무부 사용을 관리하기 위해 재무 팀의 지속적인 경계에 의존합니다.

이 페이지는 Nesto Treasury의 역사와 구조를 설명하고 재무 관리에 대한 접근 방식을 요약하며 실시간 재무 활동에 액세스하는 데 필요한 세부 정보를 제공합니다.

## 역사

Nesto의 핵심 제품인 Nesto Vaults는 처음부터 프로젝트에 대한 수수료를 생성하도록 설계되었습니다(참조:[강력한 수수료 내역](https://docs.beefy.finance/ecosystem/beefy-bulletins/beefy-finance-fees-breakdown)) 활동을 지속하기 위해. 이와 같이 우리는 항상 수익을 창출하는 프로젝트였으며 항상 어느 정도의 자금 유입 및 유출이 있었습니다.

시간이 지남에 따라 우리의 재무 활동은 점점 더 정교해졌습니다. 처음에 프로젝트의 창립자는 프로토콜 운영 및 유지 관리 비용을 충당하기 위해 스마트 컨트렉트의 유입을 비공개로 관리했습니다. 원래 [NestoTreasury 컨트렉트는 2020년 11월 6일에 ](https://bscscan.com/address/0x4a32de8c248533c28904b24b4cfcfe18e9f2ad01#code)[배포](https://bscscan.com/tx/0xccc2a94cde6aa10e3928a837b17cb7705f8ec3104afb66842627cc9cdaf4621c) 되었습니다 . Treasury 컨트렉트는 기능이 매우 기본적이어서 기본 체인 토큰과 ERC-20/BEP-20 토큰을 소유자(NestoDeployer EOA)가 인출할 수 [있습니다](https://bscscan.com/address/0x565eb5e5b21f97ae9200d121e77d2760fff106cb) .

2021년 3월까지 Nesto는 더 많은 기여자를 포함하도록 확장되었으며 핵심 기여자 팀은 모든 재무 관련 문제를 논의할 장소로 [Nesto Discord](https://discord.gg/yq8wfHd) 에 #💵-treasury 채널을 만들었습니다 . 다음 몇 달 동안 Nesto는 점점 더 멀티체인이 되었고 추가 NestoTreasury 컨트렉트가 다른 초기 체인과 함께 [Avalanche](https://snowtrace.io/address/0xa3e3af161943cfb3941b631676134bb048739727#code) , [Polygon](https://polygonscan.com/address/0x09ef0e7b555599a9f810789fff68db8dbf4c51a0#code) , [Fantom](https://ftmscan.com/address/0xe6cce165aa3e52b2cc55f17b1dbc6a8fe5d66610#code) 에 배포되었습니다 . 그러나 핵심 팀은 초기 Treasury 설계의 한계를 인식했고 MultiSig 솔루션이 모든 체인에서 사용할 수 없음에도 불구하고 MultiSig 지갑 이동에 대해 논의했습니다.

2021년 9월까지 DAO는 BSC 및 Polygon(당시 사용 가능한 MultiSig 솔루션이 있는 두 개의 체인)의 MultiSig 지갑으로 이동하고 MultiSig의 서명자로 Treasury Council을 설립하기로 결정했습니다. 2021년 9월 3일, Discord에서 Treasury Council의 새 구성원을 임명하기 위한 소프트 투표가 완료되었습니다. 이후 초기 멤버 7명을 확정한 [스냅샷 제안서가](https://vote-archive.beefy.finance/#/beefy/proposal/QmafULDojJ4StzJtuwjBZutnUkzb9TUhTLCLZe5R7deWLo) 이어졌습니다 . 9월 11일에 BSC의 원래 NestoTreasury 컨트렉트 소유권이 새로운 MultiSig로 [이전되었습니다 . ](https://bscscan.com/tx/0x34a89b3cd5564fbd22672169b9cce43a538cf764cb73e1a28fe4db89241ae7ad)그 이후로 MultiSig 시스템과 Treasury Council은 계속해서 Nesto의 새로운 체인 각각으로 확장되었습니다.

또한 2021년 9월에 Nesto의 핵심 팀은 #💵-treasury 채널에서 봇을 구현하기 시작했으며 처음에는 DAO 회원의 이익을 위해 Treasury의 가치를 표시했습니다. 2022년 10월까지 이것은 재무부의 다중서명 지갑에서 게시될 때 각각의 새로운 거래가 실시간으로 포함되도록 발전했습니다. 주요 봇 명령 중 일부는 다음과 같습니다.

* **전체 재무부 보기:** `@Bitch I'm a Cow#9189 treasury`
* **지정된 체인에 전체 자금 표시:**`@Bitch I'm a Cow#9189 treasury {blockchain}`
* **현재 수집가 잔액 표시:** `@Bitch I'm a Cow#9189 cowllector balances`

## 재무위원회

위와 같이 재무위원회는 재무부 관리를 위한 모든 재무 거래 및 관련 활동을 수행하고 관리하는 팀으로 2021년 9월에 구성되었습니다. Treasury Council의 역할은 점진적으로 확장되어 현재 다음을 포함합니다.

* 모든 Treasury 거래(예: Treasury에서 지불) 조정, 서명 및 실행
* 프로토콜 소유 유동성 생성 및 종료, 보조금 보유, 보류 중인 보상 및 기타 외부 자산과 같은 재무부 자산 관리
* 당사의 Cowllector, Fee Batch Harvester 및 기여자 EOA를 포함한 Nesto의 인프라에 대한 운영 자본(예: 가스) 모니터링 및 제공
* 기고자 급여, 후원, 정기 호스팅 및 서비스 수수료, 기고자 비용 상환을 포함한 주요 비용 지불 준비 그리고
* 파트너 거래소에 대한 정기적인 뇌물 지급 처리.

Treasury Council에는 항상 7명의 위원이 있으며 MultiSig 트랜잭션이 실행되기 전에 승인하려면 4명의 합의가 필요합니다. 현재 7명의 멤버와 Treasury EOA 지갑에 대한 세부 정보는 다음과 같습니다.

* AllTrades: 0xa75209dC118dF7B6541db5b7Be0DE9485Ebaa907
* DefiDebauchery: 0x037465bF6a4A8D7F552AE18046478C6A727178F3
* Mjoaris: 0xBFEB0756f09f73A19CE62FBa6a8Db4e922E73A14Pablo: 0xDB583b636f995eF1EF28ac96B9bA235916bd1583
* Power: 0x6fCE222540015290FB572C82622dc73a431CdF3F
* TBC: 0x428b2F01Bfb0917FE6FF463f37B0c47F1782B9Cd
* YR2150: 0xF24f555d6765D559BFF4C5557dD9024CBA10d30e

Treasury Council에 대한 모든 질문은 [Nesto Discord](https://discord.gg/yq8wfHd) 의 #💵-treasury 채널로 보내야 합니다 . 보안을 위해 위원회 구성원에게 직접 메시지를 보내지 마십시오. 그렇지 않으면 귀하의 메시지가 무시되고 잠재적으로 차단되거나 보고될 수 있습니다.

## 재무 인프라

Nesto's Treasury는 인프라의 몇 가지 핵심 빌딩 블록에 의존하여 자금의 안전한 유입 및 유출을 촉진합니다. 핵심 요소는 다음과 같습니다.

**Treasury MultiSig** - Treasury Council에서 관리하는 다중 서명 지갑으로 재무부의 모든 주요 자산을 안전하게 보관하고 개인이 악용할 수 없는 방식으로 사용됩니다.

**Treasury Contributor Wallets -** Treasury Council 구성원 및 기타 주요 DAO 기여자와 같은 다양한 Nesto 기여자가 관리하는 외부 소유 계정( **EOA )입니다.** EOA를 사용하여 외부 프로토콜 및 컨트렉트와 상호 작용함으로써 우리는 Nesto Treasury를 외부로부터 격리하고 외부 승인을 전혀 또는 매우 제한적으로 제공함으로써 Nesto Treasury에 영향을 미치는 해킹 또는 익스플로잇의 위험을 격리합니다.

**Cowllector** - Nesto Cowllector는 Nesto Core에서 만들고 유지 관리하는 봇으로 수익성 있는 기회가 있을 때마다 정기적으로 Nesto 보관소를 수집합니다. 수확 호출자가 수확의 일부로 작은 수수료를 청구하면 Cowllector는 수확 비용 대비 잠재적 보상의 크기를 분석하고 적절한 경우 자동 호출을 수행합니다. 그리고

**Fee Batcher** - Nesto Fee Batcher는 Nesto Core 팀에서 만들고 관리하는 또 다른 봇으로, Nesto의 Vault 수확물 수수료를 한 곳에서 집계하여 집계된 수수료를 체인의 기본 통화로 효율적으로 교환할 수 있습니다. Nesto 재무부에 수수료를 깔끔한 배치로 출력합니다.

Treasury MultiSig는 다음 체인에 대한 다음 주소에서 찾을 수 있습니다.

~~·  Arbitrum:~~ [~~0x3f5eddad52C665A4AA011cd11A21E1d5107d7862~~](https://gnosis-safe.io/app/arb1:0x3f5eddad52C665A4AA011cd11A21E1d5107d7862/balances)

~~·  Aurora:~~ [~~0x088C70Ddff3a3774825dd5e5EaDB356404248d83~~](https://app.safe.global/home?safe=aurora:0x088C70Ddff3a3774825dd5e5EaDB356404248d83)

~~·  Avalanche:~~ [~~0x26dE4EBffBE8d3d632A292c972E3594eFc2eCeEd~~](https://gnosis-safe.io/app/avax:0x26dE4EBffBE8d3d632A292c972E3594eFc2eCeEd/balances)

~~·  BSC:~~ [~~0x7C780b8A63eE9B7d0F985E8a922Be38a1F7B2141~~](https://gnosis-safe.io/app/bnb:0x7C780b8A63eE9B7d0F985E8a922Be38a1F7B2141/balances)

~~·  Canto:~~ [~~0xF09d213EE8a8B159C884b276b86E08E26B3bfF75~~](https://safe.neobase.one/canto:0xF09d213EE8a8B159C884b276b86E08E26B3bfF75/home)

~~·  Celo:~~ [~~0xca807d809f9639cefb3d31a7951cec3ab405a2fd~~](https://www.xdao.app/42220/dao/0xCA807D809f9639CEfb3d31a7951Cec3ab405a2fd)

~~·  Cronos:~~ [~~0xa9721Ae5042482D7a884A2138f580459B680920f~~](https://cronos-safe.org/cro:0xa9721Ae5042482D7a884A2138f580459B680920f/home)

~~·  Ethereum:~~ [~~0xc9C61194682a3A5f56BF9Cd5B59EE63028aB6041~~](https://gnosis-safe.io/app/eth:0xc9C61194682a3A5f56BF9Cd5B59EE63028aB6041/home)

~~·  Emerald:~~ [~~0x8fd0869271d26e6653f5d5650685630f75b6aedf~~](https://www.xdao.app/42262/dao/0x8FD0869271d26E6653f5d5650685630F75b6AEDf)

~~·  Fantom:~~ [~~0xdFf234670038dEfB2115Cf103F86dA5fB7CfD2D2~~](https://safe.fantom.network/#/safes/0xdFf234670038dEfB2115Cf103F86dA5fB7CfD2D2/balances)

~~·  Fuse:~~ [~~0x1C124c2CaB83b3C3B5D0f0899CeeA5e06964599F~~](https://gnosis-safe.fuse.io/fuse:0x1C124c2CaB83b3C3B5D0f0899CeeA5e06964599F/balances)

~~·  Harmony:~~ [~~0x523154a03180FD1CB26F39087441c9F91BcD0389~~](https://multisig.harmony.one/#/safes/0x523154a03180FD1CB26F39087441c9F91BcD0389/balances)

~~·  HECO :~~ [~~0xdbb72c8b7ebdd52a4813b9d262386dfdab69c9ba~~](https://www.xdao.app/128/dao/0xdbB72c8B7eBdD52A4813B9D262386dfDAB69c9bA)

~~·  Kava:~~ [~~0x07F29FE11FbC17876D9376E3CD6F2112e81feA6F~~](https://app.oryy.io/kava:0x07F29FE11FbC17876D9376E3CD6F2112e81feA6F/home)

~~·  Metis:~~ [~~0x0f9602B7E7146a9BaE16dB948281BebDb7C2D095~~](https://metissafe.tech/metis-andromeda:0x0f9602B7E7146a9BaE16dB948281BebDb7C2D095/balances)

~~·  Moonbeam:~~ [~~0x3E7F60B442CEAE0FE5e48e07EB85Cfb1Ed60e81A~~](https://multisig.moonbeam.network/mbeam:0x3E7F60B442CEAE0FE5e48e07EB85Cfb1Ed60e81A/home)

~~·  Moonriver:~~ [~~0x617f12E04097F16e73934e84f35175a1B8196551~~](https://multisig.moonbeam.network/mriver:0x617f12E04097F16e73934e84f35175a1B8196551/balances)

~~·  Optimism:~~ [~~0x4ABa01FB8E1f6BFE80c56Deb367f19F35Df0f4aE~~](https://gnosis-safe.io/app/oeth:0x4ABa01FB8E1f6BFE80c56Deb367f19F35Df0f4aE/home)

~~·  Polygon:~~ [~~0xe37dD9A535c1D3c9fC33e3295B7e08bD1C42218D~~](https://gnosis-safe.io/app/matic:0xe37dD9A535c1D3c9fC33e3295B7e08bD1C42218D/balances)

재무 관리

Nesto's Treasury의 관리는 프로토콜과 DAO의 변화하는 요구 사항을 반영하기 위해 시간이 지남에 따라 발전했습니다. 각각 고유한 Vault, 인프라 및 MultiSig 지갑 세트가 있는 수많은 체인에서 운영되므로 재무부를 능동적으로 관리하는 것은 복잡한 프로세스입니다. 대신 현재의 모범 사례를 안내하는 여러 중요한 원칙이 등장했습니다.

무엇보다도 Nesto의 기여자 팀은 변동성 자산 보유의 하방 위험을 완화하기 위해 주로 스테이블 코인으로 각 주요 체인에서 재무부를 운영하기로 결정했습니다. 이를 통해 Nesto는 현재 시장 상황의 위험 없이 자금을 사용하는 방법에 대해 명확하고 신중한 결정을 내릴 수 있습니다. 따라서 각 주요 체인의 수수료 배처는 Nesto의 모든 Vault 수수료를 해당 체인의 지정된 통화로 스왑합니다.[유입 통화](https://docs.beefy.finance/community-governance/treasury#inflow-currencies)아래에.

둘째, 소극적 소득을 창출하기 위해 재무 자산의 일부를 Nesto Vaults에 투자하는 것이 현명하고 현명한 자금 사용이 될 것이라고 결정했습니다. 일반적으로 말해서, 우리의 주요 체인에 보관된 재무부 중 극히 일부만이 Nesto Vault에 투자되며 항상 스테이블 코인 전용 Vault에 할당됩니다. 투자된 펀드의 수익률은 일반적으로 상대적으로 작지만 이 메커니즘은 스테이블코인 평가의 인플레이션 특성을 완화하는 데 도움이 됩니다.

마지막으로, 서로 다른 체인에서 토큰의 유동성을 촉진하기 위해 Nesto는 또한 우리 토큰과 토큰의 유동성 포지션에 투자하고 유지합니다.[NEFI 토큰](https://docs.beefy.finance/ecosystem/bifi-token), 그리고 우리의 [Nesto-escrowed 토큰](https://docs.beefy.finance/products/beefy-escrowed-tokens). 우리의 NEFI 토큰은 일반적으로 관련 체인의 기본 또는 가스 토큰과 쌍을 이루어 체인에 새로 도착하는 토큰에 대한 가장 쉬운 경로를 제공합니다. 브리지된 NEFI 토큰 컨트렉트가 새 체인에 배포되면 컨트렉트가 Nesto Bridge와 Multichain의 파트너에 연결되어 더 많은 NEFI가 체인에 연결되고 초기 LP가 생성될 수 있습니다.

모든 유동, 스테이킹/투자 및 잠긴 토큰과 유동성 포지션을 포함하여 Nesto Treasury의 현재 상태에 대한 실시간 요약 세부 정보는 Nesto 앱의 [Treasury Dashboard 페이지에서 확인할 수 있습니다.](https://app.beefy.finance/treasury)

NestoTreasury 대시보드는 Nesto Treasury의 현재 상태에 대한 즉각적인 요약 통찰력을 제공하기 위해 2023년 1월에 도입되었습니다.

\--------이미지영역--------------

유입 통화

위에서 설명한 바와 같이 [재무 관리](https://docs.beefy.finance/community-governance/treasury#treasury-management), 주요 블록체인의 유입은 자금 관리 목적으로 스테이블 코인으로 스왑되며, 각 체인의 수수료 배처는 운영을 위해 주요 스테이블 코인을 채택합니다. 다음 체인에는 다음과 같은 지정 통화가 있습니다.

~~·  Arbitrum: USDC~~

~~·  Avalanche: USDT~~

~~·  BSC: BUSD~~

~~·  Canto: USDC~~

~~·  Cronos: USDC~~

~~·  Ethereum: USDC~~

~~·  Ethereum Validator: ETH (retained in validator)~~

~~·  Fantom: USDC~~

~~·  Fantom Validator: WBTC (gradually bridged to Ethereum)~~

~~·  Fuse: BUSD~~

~~·  Fuse Validator: Fuse (retained in validator)~~

~~·  Kava: USDC~~

~~·  Metis: USDC~~

~~·  Moonbeam: MAI~~

~~·  Moonriver: USDC~~

~~·  Optimism: USDC~~

~~·  Polygon: USDC~~

Aurora, Celo, Emerald, Harmony 및 HECO의 거래량과 유동성은 현재 스테이블 코인 관리를 보장하기에는 너무 작기 때문에 Vault 수수료 유입은 일반적으로 관련 기본 체인 토큰으로 스왑됩니다.
