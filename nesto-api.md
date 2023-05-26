# NESTO API

이 페이지는 우리의 웹 애플리케이션, 대시보드, 그리고 서드파티 사이트의 페이지를 구동하는 Nesto의 REST API의 기능 및 운영에 대한 자세한 정보를 제공합니다.

[https://api.Nesto.finance/](https://api.beefy.finance/) 에서 API에 액세스할 수 있으며 공개 리포지토리는 [Nesto GitHub](https://github.com/beefyfinance/beefy-api) 에서 사용할 수 있습니다 . API는 MIT 라이선스에 따라 유지 관리되며 자세한 내용은 [GitHub 저장소](https://github.com/beefyfinance/beefy-api/blob/master/LICENSE) 에서 확인할 수 있습니다 .

## 엔드포인트

당사의 API는 웹 ,앱 ,백엔드 및 대시보드뿐만 아니라 일부 서드파티 서비스에 필요한 전체 데이터 선택을 포함하는 다양한 퍼블릭 엔드포인트를 제공합니다.

## Nesto Vault 엔드포인트

[Nesto 애플리케이션](https://app.beefy.finance/) 에서 보관소의 실시간 특성과 성능을 사용자에게 표시하기 위해 개발된 엔드포인트입니다 .

<details>

<summary>GET /Vault</summary>

각 Nesto 보관소에 대한 실시간 정보를 제공하며, 퇴출된 (eol) 보관소들도 포함됩니다. 정보에는 관련 보관소의 이름/ID, 체인, 토큰, 근거 자산, 관련 계약 및 현재 상태를 위한 필드가 포함됩니다. 또한 "risks" 필드를 포함하여, 보관소의 안전 점수를 계산하는데 사용되는 위험 요소 행렬에서 가져온 보관소의 특징들을 나열합니다.

```
// Sample response for the /vaults endpoint (e.g. Polygon aTriCrypto3 vault)

{
  "id": "curve-poly-atricrypto3",
  "name": "aTriCrypto3",
  "token": "crvUSDBTCETH3",
  "tokenAddress": "0xdAD97F7713Ae9437fa9249920eC8507e5FbB23d3",
  "tokenDecimals": 18,
  "tokenProviderId": "curve",
  "earnedToken": "mooCurveATriCrypto3",
  "earnedTokenAddress": "0x5A0801BAd20B6c62d86C566ca90688A6b9ea1d3f",
  "earnContractAddress": "0x5A0801BAd20B6c62d86C566ca90688A6b9ea1d3f",
  "oracle": "lps",
  "oracleId": "curve-poly-atricrypto3",
  "status": "active",
  "platformId": "curve",
  "assets": [
    "DAI",
    "USDC",
    "USDT",
    "WBTC",
    "ETH"
  ],
  "strategyTypeId": "multi-lp",
  "risks": [
    "COMPLEXITY_LOW",
    "BATTLE_TESTED",
    "IL_LOW",
    "MCAP_LARGE",
    "PLATFORM_ESTABLISHED",
    "AUDIT",
    "CONTRACTS_VERIFIED",
    "OVER_COLLAT_ALGO_STABLECOIN"
  ],
  "addLiquidityUrl": "https://polygon.curve.fi/atricrypto3/deposit",
  "network": "polygon",
  "createdAt": 1652662923,
  "chain": "polygon",
  "strategy": "0x41D7529b4C9245a50ca6A169d39719DFF117f6CA",
  "lastHarvest": 1664612723,
  "pricePerFullShare": "1178961451902175914"
},
```

**필드 참고 사항**

* **id** - 각 보관소에 할당된 고유한 식별 문자열로, 동일한 보관소의 별도 버전을 포함합니다.

<!---->

* **tokenAddress** - 주로 LP 토큰인 주요 예금 자산의 계약 주소입니다.

<!---->

* **earnedTokenAddress** - 보관소에서 사용하는 전략으로 얻은 토큰의 계약입니다. 대부분의 Nesto 보관소에서는 전략이 자동 복합이기 떄문에 보관소 계약과 같으며, 전략이 자동 복합이기 때문입니다. 수익 풀 보관소 (자동 복합되지 않음)의 경우, 이는관보소가 관련된 체인 또는 프로토콜의 네이티브 토큰이 될 것입니다.

<!---->

* **earnContractAddress** - 예금 및 인출을 처리하고 사용자에게  bird 보관소 토큰을 발급하는 Nesto 보관소 계약의 주소입니다.

<!---->

* **status** - 보관소가 활성 상태인지("active") 또는 폐지된 상태인지("eol")를 나타냅니다.

<!---->

* **assets** - 해당 보관소의 스택에 있는 기초 자산(보관소가 구축된 LP에 포함된 자산)입니다.

<!---->

* **strategyTypeID** - 보관소에서 사용되는 전략의 유형을 나타냅니다(예: "단일" 자산, "lp", "다중 LP" 등).

<!---->

* **risks** - 보관소의 적용 가능한 기능 목록으로, 보관소의 안전 점수를 계산하는 데 사용되는 요소 매트릭스에서 가져온 내용입니다.

<!---->

* **network** - 보관소가 속한 관련 블록체인입니다.

<!---->

* **createdAt** - 보관소가 생성된 관련 블록체인의 블록입니다.

<!---->

* **strategy** - 보관소에서 현재 사용 중인 전략 계약의 주소입니다.

<!---->

* **lastHarvest** - 보관소가 마지막으로 수확된 관련 블록체인의 블록으로, 전략에서 수익을 수집한 위치(자동 복리인 경우 해당)입니다.

<!---->

* **pricePerFullShare** - 보관소의 총 발행 주식의 각 전체 주당 현재 평균 가격(예금 자산으로 표시됨, 예: 기초 LP 토큰)으로, 보관소의 수명 동안 투자된 총 가치를 발행된 보관소 주식 수로 나눈 값입니다.

</details>

<details>

<summary>GET /apy</summary>

각 Nesto 보관소의 현재 및 실시간 연간 수익률을 제공합니다.

```
// Sample response for the /apy endpoint

{
  ...
  "balancer-usdc-link-eth-bal-aave": 0.03705509745347668,
  "balancer-matic-usdc-eth-bal": 0.052770609595836904,
  "baby-wbnb-busd": 0.1612595689122669,
  "baby-usdc-wbnb": 0.16031283171896837,
  "balancer-vst-dai-usdt-usdc": 0.029489187277781825,
  "balancer-bal-eth": 0.024578537703132453,
  "curve-matic-stmatic": 0.08866966650936048,
  "sushi-poly-weth-sx": 0.7135292677781775,
  "sushi-poly-bct-klima": 0.0007036903322936716,
}
```

**필드 참조 사항**

* **Vault APY** - 보관소 APY - 각 필드는 보관소의 고유한 ID 문자열을 반영하며, 실시간 APY를 소수로 나타내는 값을 반환합니다. 예를 들어, "0.037"은 3.7% APY를 나타냅니다.

</details>

<details>

<summary>GET /apy/breakdown</summary>

각 Nesto 보관소의 수익률에 관련된 더 자세한 정보를 제공합니다. 이 정보는 연이율(APR), 복리 속도, 적용 가능한 수수료 등과 같은 요소를 기반으로 예상 APY를 평가하는 데 필요합니다.

```
// Sample response from the /apy/breakdown endpoint (e.g. Polygon Cometh UST-ETH LP)

{
  "bifi-maxi": {
    "totalApy": 0.07598675804818633
  },
  "cometh-must-eth": {
    "vaultApr": 1.186973388240745,
    "compoundingsPerYear": 2190,
    "NestoPerformanceFee": 0.045,
    "vaultApy": 2.1057844292858614,
    "lpFee": 0.005,
    "tradingApr": 0.22324214039526927,
    "totalApy": 2.8825691266420788
  }
}
```

**필드 참조 사항**

* **vaultApr** - 보관소의 연간 수익률로, 보관소의 예상 연간 보상을 $USD로 환산한 값으로, 보관소에 투자된 총 금액으로 나눈 것입니다.

<!---->

* **compoundingsPerYear** - 연간 복리 이벤트("수확" 호출)의 현재 예상 횟수입니다.

<!---->

* **NestoPerformanceFee** - 계산에 포함된 고정 Nesto 성과 수수료입니다.

<!---->

* **vaultApy** - 보관소의 연간 수익률(APY)은 위에서 설명한 vaultApr을 compoundingsPerYear 숫자를 사용하여 복리하고, NestoPerformanceFee를 고려해 조정하여 계산됩니다.

<!---->

* **lpFee** - 각 거래에 부과되는 유동성 제공자(LP) 수수료입니다.

<!---->

* **tradingApr** - 복리 효과를 적용하거나 고려하지 않고 거래 수수료로부터 받는 연간 이자입니다.

<!---->

* **totalApy** - 알려진 총 APY로, totalApy = (1 + vaultApy) \* (1 + tradingApr) - 1로 계산됩니다.

</details>

<details>

<summary>GET /tvl</summary>

각 Nesto 보관소의 현재 및 실시간 총 잠긴 가치를 제공합니다. 이 값은 해당 보관소가 현재 보유한 모든 자산의 현재 시가 총액의 합으로, $USD로 표시됩니다.

```
// Sample response from the /tvl endpoint

{
    ...
    "optimism-bifi-maxi": 37679.65,
    "velodrome-wsteth-weth": 295597.74,
    "beets-lido-shuffle": 101185.39,
    "beets-yellow-submarine": 5828.15,
    "beets-its-mai-life": 178994.42,
    "velodrome-usdc-mim": 488943.72,
    "velodrome-weth-bifi": 133635.5,
    ...
}
```

</details>

<details>

<summary>GET /fees</summary>

각 Nesto 보관소의 현재 수수료 구조에 대한 자세한 내역을 제공합니다.

```
// Sample response from the /fees endpoint (e.g. Celo BIFI Maxi vault)

{
  "celo-bifi-maxi": {
    "performance": {
      "total": 0.0005,
      "strategist": 0,
      "call": 0.0005,
      "treasury": 0,
      "stakers": 0
    },
    "withdraw": 0,
    "lastUpdated": 1665603844026
  },
  ...
}
```

**필드 참조 사항**

* performance - 각 보관소의 모든 복리 이벤트("수확")에 부과되는 성과 수수료로 구성된 수수료 설정 목록입니다.
* total - 부과된 총 성과 수수료로, "performance" 목록의 다른 항목들의 합입니다.
* strategist - 보관소를 배포하는 전략가에게 지급되는 수수료로, 커뮤니티 기여를 장려하기 위한 형태로 지급됩니다.
* call - 복리를 발생시키는 수확 함수의 호출자에게 지급되는 수수료입니다.
* treasury - 프로토콜을 지원하기 위해 Nesto 자금에 지급되는 수수료입니다. stakers - NEFI 토큰의 소유자 및 스테이커에게 지급되는 수수료로, NEFI 수익 풀 보관소에 지급되거나 NEFI Maxi 보관소를 위해 NEFI 토큰을 매입하는 데 사용됩니다.
* withdraw - 보관소에서 인출 시 예금 가치에 부과되는 수수료로, 보관소의 공격 및 남용으로부터 보호하기 위해 부과됩니다.
*   lastUpdated - API의 데이터가 마지막으로 업데이트된 보관소의 관련 블록입니다.

    \


</details>

### 외부 자산 엔드포인트

[Nesto 애플리케이션](https://app.beefy.finance/)에서 사용하도록 개발된 엔드포인트는 Nesto 보관의 기본 자산에 대한 정보를 사용자에게 표시합니다.

<details>

<summary>GET /lps</summary>

각 Nesto 보관소에서 사용하는 기초 유동성 풀의 현재 실시간 가격을 제공합니다.

```
// Sample respones from the /lps endpoint

{
  ...
  "crow-crow-bnb": 17.913780228255288,
  "crow-crow-busd": 1.1650429579716788,
  "czf-czf-bnb": 0.0025782563297118174,
  "czf-czf-busd": 0.00013385738163789002,
  "dark-dark-cro": 0.07756021296662909,
  "dark-sky-cro": 1.6261613868777973,
  "dfx-nzds-usdc": 0.5422606115320028,
  "dfyn-aave-dfyn": 2.878265077862883,
  "dfyn-bifi-dfyn": 6.083434553784047,
  ...
}
```

**필드 참조 사항**

* **LP price** - 각 필드는 LP 보관소의 oracleId 문자열을 반영하며, 미국 달러로 표시된 실시간 가격을 나타내는 값을 반환합니다. 예를 들어, "1.165"는 1.17달러의 가격을 나타냅니다.

</details>

<details>

<summary>GET /lps/breakdown</summary>

각 Nesto 보관소에서 사용되는 유동성 풀에 관련된 보다 자세한 정보를 제공합니다.

```
// Sample response from the /lps/breakdown endpoint (eg. 2omb 2omb-2share LP)

{
  "2omb-2omb-2share": {
    "price": 0.29050984564246707,
    "tokens": [
      "0x7a6e4E3CC2ac9924605DCa4bA31d1831c84b44aE",
      "0xc54A1684fD1bef1f077a336E6be4Bd9a3096a6Ca"
    ],
    "balances": [
      "114463.728388652710537014",
      "391.331589320557497638"
    ],
    "totalSupply": "5873.360029904692639438"
  },
```

**필드 참조 사항**

* **price** - LP 토큰의 전체 주당 현재 및 실시간 가격으로, $USD로 표시됩니다.
* **tokens** - LP에 포함된 각 기초 자산/토큰의 계약 주소 목록입니다.
* **balances** - 보관소의 각 토큰의 현재 잔액 목록으로, 이전 필드에 나열된 기초 토큰으로 표시됩니다.
* **totalSupply** - 현재 및 실시간 발행된 LP 토큰의 총량입니다.

</details>

<details>

<summary>GET /tokens</summary>

Nesto에서 사용하는 모든 토큰에 대한 정보를 제공합니다. 개별 자산 및 통화, 스테이킹된 자산 및 LP를 블록체인별로 정렬하여 제공합니다.

```
// Sample response for /tokens endpoint (e.g. polygon spUSDC LP token)

{
  "polygon": {
    "spUSDC": {
      "name": "Stargate USD Coin LP",
      "symbol": "spUSDC",
      "address": "0x1205f31718499dBf1fCa446663B532Ef87481fe1",
      "decimals": 6
    },
    ...
}
```

**필드 참조 사항**

* **name** - 해당 ID와 관련된 토큰의 전체 이름을 나타내는 문자열입니다.
* **symbol** - 발행자가 할당한 토큰의 심볼을 나타내는 문자열입니다.
* **address** - 해당 토큰의 계약 주소입니다.
* **decimals** - 발행자가 허용한 토큰의 소수점 자릿수로, 체인 상에서의 분할 가능성을 나타냅니다.

**GET /tokens/{blockchain}**&#x20;

더 구체적인 정보를 원하신다면, /tokens 엔드포인트에 {blockchain} 매개변수를 추가하여 특정 블록체인에서만 토큰을 반환할 수 있습니다. (예: /tokens/polygon은 Polygon 블록체인에서 발행된 토큰만 반환합니다.)

</details>

### 기타 엔드포인트

[Nesto 애플리케이션](https://app.beefy.finance/)에서 개별 보관소와 관련되지 않은 기타 정보를 표시하는 데 사용하도록 개발된 엔드포인트입니다

<details>

<summary>GET /config</summary>

[Nesto 애플리케이션](https://app.beefy.finance/)에서 사용되는 각 블록체인을 운영하기 위해 사용되는 현재 지갑 구성의 주소에 대한 정보를 제공합니다.

<pre><code>// Sample response from /config endpoint (e.g. Polygon blockchain)
<strong>
</strong><strong>{
</strong>  "polygon": {
    "devMultisig": "0x09dc95959978800E57464E962724a34Bb4Ac1253",
    "treasuryMultisig": "0xe37dD9A535c1D3c9fC33e3295B7e08bD1C42218D",
    "strategyOwner": "0x6fd13191539e0e13B381e1a3770F28D96705ce91",
    "vaultOwner": "0x94A9D4d38385C7bD5715A2068D69B87FF81F4BF3",
    "keeper": "0x4fED5491693007f0CD49f4614FFC38Ab6A04B619",
    "treasurer": "0xe37dD9A535c1D3c9fC33e3295B7e08bD1C42218D",
    "launchpoolOwner": "0x09dc95959978800E57464E962724a34Bb4Ac1253",
    "rewardPool": "0xDeB0a777ba6f59C78c654B8c92F80238c8002DD2",
    "treasury": "0x09EF0e7b555599A9F810789FfF68Db8DBF4c51a0",
    "NestoFeeRecipient": "0x7313533ed72D2678bFD9393480D0A30f9AC45c1f",
    "bifiMaxiStrategy": "0xD126BA764D2fA052Fc14Ae012Aef590Bc6aE0C4f",
    "voter": "0x5e1caC103F943Cd84A1E92dAde4145664ebf692A",
    "NestoFeeConfig": "0x8E98004FE65A2eAdA63AD1DE0F5ff76d845f14E7"
  },
...
</code></pre>

**필드 참조 사항**

* **devMultisig** - 체인에서 개발 업데이트를 관리하기 위해 사용되는 Nesto 개발자 다중 서명 지갑의 주소입니다.
* **treasuryMultisig** - 체인에서 Nesto의 핵심 자금을 관리하기 위해 사용되는 Nesto 자금 다중 서명 지갑의 주소입니다.
* **strategyOwner** - 체인에서 전략 계약의 소유자 역할을 하는 일반적인 Nesto 지갑의 주소입니다.
* **vaultOwner** - 체인에서 보관소 계약의 소유자 역할을 하는 일반적인 Nesto 지갑의 주소입니다.
* **keeper** - 체인에서 보관소 계약의 관리자 역할을 하는 일반적인 Nesto 지갑의 주소입니다. 이에는 보관소에서 사용되는 전략 화이트리스트를 관리하고 필요한 경우 보관소를 일시 중지하거나 비상 상태로 전환하는 작업이 포함됩니다.
* **treasurer** - 체인에서 회계 관리자 역할을 하는 반일적인 Nesto 지갑의 주소입니다. 이에는 다양한 이유로부터의 자금 지불을 관리하며,  treasuryMultisig와 동일한 지갑입니다.
* **launchpoolOwner** - 체인에 배포된 런치풀/burst 계약의 소유자로 작동하는 일반적인 Nesto 지갑의 주소입니다. devMultisig와 동일한 지갑입니다.
* **rewardPool** - 체인에서 burst를 위해 할당된 보상을 보유하는 일반적인 Nesto 지갑의 주소입니다.
* **treasury** - 체인에서 자금관리자로 작동하는 일반적인 Nesto 지갑의 주소이며, 회계관리자와 treasuryMultisig에 의해 관리됩니다.
* **NestoFeeRecipient** - 체인 상의 모든 Nesto 보관소에서 수확 시 부과되는 성과 수수료를 수취하는 일반적인 Nesto 지갑의 주소입니다.
* **nefiMaxiStrategy** - 체인 상의 기본 NEFI Maxi 보관소에 연결된 전략의 주소입니다. voter - 다양한 타사 프로토콜에 대한 Nesto의 투표 권한을 지시하는 데 사용되는 일반적인 Nesto 지갑의 주소입니다.
* **NestoFeeConfig** - 체인 상의 보관소에 부과되는 성과 수수료 설정을 위해 사용되는 업그레이드 가능한 프록시 계약의 주소입니다.

**GET /config/{blockchain}**

더 구체적인 정보를 원하신다면, /config 엔드포인트에 {blockchain} 매개변수를 추가하여 특정 블록체인의 구성

</details>

<details>

<summary>GET /bursts</summary>

Nesto 애플리케이션에서 호스팅하는 모든 런치풀 부스트에 관한 정보를 제공합니다. 이 정보에는 실시간 및 과거의 [버스트](undefined-1/undefined-1.md#burst)도 포함됩니다.

```
// Sample response from /boosts endpoint (e.g. Optimism BIFI-WETH LP token)

{
  "id": "moo_velodrome-weth-bifi-nesto",
  "poolId": "velodrome-weth-bifi",
  "name": "Nesto",
  "assets": [
    "BIFI",
    "ETH"
  ],
  "tokenAddress": "0x3532b6f723948eF39d5DCf44C16855239aF81082",
  "earnedToken": "OP",
  "earnedTokenDecimals": 18,
  "earnedTokenAddress": "0x4200000000000000000000000000000000000042",
  "earnContractAddress": "0x8F755873546F4D0EDf7d41fF8604C8A632113eB7",
  "earnedOracle": "tokens",
  "earnedOracleId": "OP",
  "partnership": true,
  "status": "active",
  "isMooStaked": true,
  "partners": [
    "Nesto"
  ],
  "chain": "optimism",
  "periodFinish": 1667843632
},
...
```

* **id** - 동일한 보관소의 별도 버전을 포함하여 각 보관소에 할당된 고유한 식별 문자열입니다.
* **poolId** - Nesto가 보관하는 각 LP에 할당된 고유한 식별 문자열입니다. 동일한 LP의 별도 버전을 포함합니다.
* **name** - burst를 기금한 파트너(들)의 전체 이름입니다. assets - 보관소 또는 기초 LP에 사용되는 기초 자산 목록입니다.
* **tokenAddress** - 예금 및 인출을 처리하고 사용자에게 birdVault 토큰을 발급하는 Nesto 보관소 계약의 주소입니다.
* **earnedToken** - burst 참가자가 획득하는 burst 보상 토큰의 이름입니다.
* **earnedTokenDecimals** - earnedToken의 생성 시 할당된 소수점 위치의 수입니다.
* **earnTokenAddress** - earnedToken의 계약 주소입니다.
* **earnContractAddress** - burst 계약의 계약 주소로, 할당된 burst 보상을 보유하고 burst 참가자에게 분배합니다.
* **isbirdStaked** - burst를 받기 위해 사용자가 bird 토큰을 nesto와의 추가 계약에 스테이킹해야 하는지 여부입니다.
* **partners** - burst를 기금한 파트너(들)에 대한 약식 레이블입니다.
* **periodFinish** - burst가 종료되는 호스팅 블록체인의 블록입니다.

**GET /bursts/{blockchain}**

더 자세하ㄴ 정보를 원하신다면, /bursts 엔드포인트에 {blockchain} 매개변수를 추가하여 특정 블록체인의 burst만 반환할 수 있습니다. (예: /bursts/polygon은 Polygon 블록체인에서 호스팅되는 burst만 반환합니다.)

</details>

<details>

<summary>GET /NEFIbuyback</summary>

각 블록체인에서 수행되는 일일 NEFI 매입량에 대한 세부 정보를 제공합니다.

```
// Sample response from the /NEFIbuyback endpoint (e.g. BSC data)

{
  "bsc": {
    "buybackTokenAmount": "0.377849674473987141",
    "buybackUsdAmount": "121.1485184178464957989921757592912"
  },
  ...
}
```

**필드 참조 사항**

* **buybackTokenAmount** - 해당 체인에서 프로토콜이 일일로 매입하는 NEFI 토큰의 현재 양을 나타냅니다.
* **buybackUsdAmount** - 위의 양을 미국 달러로 환산한 현재 가치를 나타냅니다.

</details>

### 대시보드 엔드포인트

프로토콜에 대한 중요한 정보를 표시하기 위해 [Nesto Dashboard](https://dashboard.beefy.finance/) 용으로 개발된 엔드포인트입니다.

{% hint style="info" %}
대시보드 사이트와 /earnings 엔드포인트는 더 이상 Nesto에서 지속적인 관리를 하지 않습니다. 대시보드 사이트는 활성화된 상태로 유지되지만 구현된 모든 보관소와 Nesto가 배포한 모든 체인을 반영하지는 않습니다.
{% endhint %}

<details>

<summary>GET /holders</summary>

NEFI 토큰의 현재 보유자 수를 구체적으로 제공합니다.

```
// Sample respones from the /holders endpoint

{
  "holderCount": 36882
}
```

</details>

### 서드 파티 엔드포인트

Nesto 또는 제품에 대한 정보를 사이트에 표시하기 위해 제3자 플랫폼에서 필요하거나 활용하는 엔드포인트입니다.

<details>

<summary>GET /cmc</summary>

CoinMarketCap에서 Nesto 보관소를 수익률 농장 섹션에 표시하기 위해 필요한 정보를 제공합니다.

```
// Sample response for the /cmc endpoint

{
  "provider": "Nesto",
  "provider_logo": "https://Nesto.finance/img/Nesto.svg",
  "links": [
    {
      "title": "Twitter",
      "link": "https://twitter.com/Nestofinance"
    },
    {
      "title": "Telegram",
      "link": "https://t.me/Nestofinance"
    },
    {
      "title": "Discord",
      "link": "https://discord.gg/yq8wfHd"
    },
    {
      "title": "Medium",
      "link": "https://medium.com/Nestofinance"
    },
    {
      "title": "Github",
      "link": "https://github.com/Nestofinance"
    }
  ],
  "pools": [
    {
      "name": "BIFI Maxi",
      "pair": "BIFI",
      "pairLink": "https://app.Nesto.finance/",
      "logo": "https://Nesto.finance/vaults/bifi/BIFI.png",
      "poolRewards": [
        "BIFI"
      ],
      "apyId": "bifi-maxi",
      "contract": "0xf7069e41C57EcC5F122093811d8c75bdB5f7c14e",
      "oracle": "tokens",
      "oracleId": "BIFI"
    },
    ...
  ]
}
```



</details>

<details>

<summary>GET /supply</summary>

Coingecko에서 NEFI의 총 공급량과 유통 공급량을 사이트에 표시하기 위해 필요한 정보를 제공합니다.

```
// Sample response for the /supply endpoint

{
  "total": 80000,
  "circulating": 80000
}
```

</details>

## 하위 그래프

Nesto는 현재 프로토콜의 하위 그래프를 운영하지 않고 있으며 서브그래프만 존재합니다. 이 서브그래프는 Messari에서 BSC 체인에서의 우리를 위한 서브그래프를 개발했으며, 이는[https://api.thegraph.com/subgraphs/name/messari/Nesto-finance-bsc](https://api.thegraph.com/subgraphs/name/messari/beefy-finance-bsc) 및 The Graph의 [웹사이트](https://thegraph.com/hosted-service/subgraph/messari/beefy-finance-bsc/) (ID: QmfEtMEgjik9FSZdqAmp2DkNFG4M9TK4Go8uyCUj8EVxY6)에서 이용할 수 있습니다.&#x20;

Nesto는 이 서브그래프의 개발이나 유지보수에 아무런 역할을 하지 않았습니다. 만약 서브그래프와 관련된 도움이 필요할 경우 직접 Messari에게 문의해주시기 바랍니다.

## 기타 데이터

Nesto 및 프로토콜 성능에 대한 더 자세한정보를 원하는 사용자는 [Discord 서버](https://discord.gg/yq8wfHd) 의 #📊-Nesto-data 채널로 이동하십시오. 귀하의 질문에 기꺼이 답변해드릴 수 있으며 여러분이 저희의 데이터 작업에 대한 배경을 더 파악하기 위해 채널에서 토론 기록을 자유롭게 확인하실 수 있습니다.

또한 해당 채널에서 핵심 기여자 EPETE가 준비한 다양한 체인 및 보관소에 걸쳐 분배된 NEFI 토큰 분석에 대한 주간 보고서 모음을 찾을 수 있습니다.
