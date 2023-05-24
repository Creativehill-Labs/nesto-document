# NESTO API

이 페이지는 타사 사이트의 웹 애플리케이션, 대시보드 및 페이지를 지원하는 Nesto의 REST API의 기능 및 작동에 대한 자세한 내용을 제공합니다.

[https://api.Nesto.finance/](https://api.beefy.finance/) 에서 API에 액세스할 수 있으며 공개 리포지토리는 [Nesto GitHub](https://github.com/beefyfinance/beefy-api) 에서 사용할 수 있습니다 . API는 MIT 라이선스에 따라 유지 관리되며 자세한 내용은 [GitHub 저장소](https://github.com/beefyfinance/beefy-api/blob/master/LICENSE) 에서 확인할 수 있습니다 .

## 엔드포인트

당사의 API는 웹 앱, 백엔드 및 대시보드뿐만 아니라 일부 타사 서비스에 필요한 전체 데이터 선택을 포괄하는 다양한 퍼블릭 엔드포인트를 제공합니다.

## Nesto Vault 엔드포인트

[Nesto 애플리케이션](https://app.beefy.finance/) 에서 Vault의 실시간 특성과 성능을 사용자에게 표시하기 위해 개발된 엔드포인트입니다 .

<details>

<summary>GET /Vault</summary>

다음은 각 Nesto 보관소에 대한 실시간 정보입니다. 이 정보에는 해당 보관소의 이름/ID, 체인, 토큰, 기초 자산, 관련 계약 및 현재 상태를 나타내는 필드가 포함됩니다. 또한 "위험" 필드에는 보관소의 안전 점수를 계산하는 데 사용되는 위험 요인 매트릭스에서 가져온 보관소의 특징이 나열됩니다.

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

* **tokenAddress** - 주요 예금 자산에 대한 계약 주소로, 일반적으로 LP 토큰입니다.

<!---->

* **earnedTokenAddress** - 보관소에서 사용하는 전략에 의해 획득되는 토큰의 계약입니다. 대부분의 Beefy 보관소에서는 전략이 자동 복리인 경우에는 보관소 계약과 동일합니다. 수익 풀 보관소(자동 복리가 아닌 경우)의 경우, 이것은 보관소와 관련된 체인 또는 프로토콜의 원래 토큰입니다.

<!---->

* **earnContractAddress** - 예금 및 인출을 처리하고 사용자에게 mooVault 토큰을 발급하는 Beefy 보관소 계약의 주소입니다.

<!---->

* **status** - 보관소가 활성 상태인지("active") 또는 폐지된 상태인지("eol")를 나타냅니다.

<!---->

* **assets** - 해당 보관소의 스택에 있는 기초 자산(일반적으로 보관소가 구축된 LP에 포함된 자산)입니다.

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



</details>

<details>

<summary>GET /apy/breakdown</summary>



</details>

<details>

<summary>GET /tvl</summary>



</details>

<details>

<summary>GET /fees</summary>



</details>

### 외부 자산 엔드포인트

Nesto 또는 그 제품에 대한 정보를 사이트에 표시하기 위해 제3자 플랫폼에서 필요하거나 활용하는 엔드포인트.

<details>

<summary>GET /lps</summary>



</details>

<details>

<summary>GET /lps/breakdown</summary>



</details>

<details>

<summary>GET /tokens</summary>



</details>

### 기타 엔드포인트

[Nesto 애플리케이션](https://app.beefy.finance/) 에서 개별 Vault와 관련되지 않은 기타 정보를 표시하는 데 사용하도록 개발된 엔드포인트입니다

<details>

<summary>GET /config</summary>



</details>

<details>

<summary>GET /bursts</summary>



</details>

<details>

<summary>GET /NEFIbuyback</summary>



</details>

### 대시보드 엔드포인트

프로토콜에 대한 중요한 정보를 표시하기 위해 [Nesto Dashboard](https://dashboard.beefy.finance/) 용으로 개발된 엔드포인트입니다.

{% hint style="info" %}
대시보드 사이트와 /earnings 엔드포인트는 더 이상 Nesto에서 적극적으로 유지 관리하지 않습니다. 대시보드 사이트는 활성 상태로 유지되지만 구현된 모든 Vault와 Nesto가 배포한 모든 체인을 반영하지는 않습니다.
{% endhint %}

<details>

<summary>GET /holders</summary>



</details>

### 서드 파티 엔드포인트

Nesto 또는 그 제품에 대한 정보를 사이트에 표시하기 위해 제3자 플랫폼에서 필요하거나 활용하는 엔드포인트.

<details>

<summary>GET /cmc</summary>



</details>

<details>

<summary>GET /supply</summary>



</details>

## 하위 그래프

Nesto는 현재 프로토콜의 하위 그래프를 운영하거나 유지하지 않습니다.

[Messari의 친구들은 https://api.thegraph.com/subgraphs/name/messari/Nesto-finance-bsc](https://api.thegraph.com/subgraphs/name/messari/beefy-finance-bsc) 및 The Graph [웹사이트](https://thegraph.com/hosted-service/subgraph/messari/beefy-finance-bsc) (ID: QmfEtMEgjik9FSZdqAmp2DkNFG4M9TK4Go8uyCUj8EVxY6) 에서 BSC 체인에 대한 하위 그래프를 개발했습니다. ). Nesto는 이 하위 그래프의 개발이나 유지 관리에 아무런 역할도 하지 않았습니다. 이 하위 그래프와 관련된 질문이나 지원 요청은 Messari에 직접 전달하십시오.

## 기타 데이터

Nesto 및 프로토콜 성능에 대한 추가 정보를 원하는 독자는 [Discord 서버](https://discord.gg/yq8wfHd) 의 #📊-Nesto-data 채널로 이동하십시오. 귀하의 질문에 기꺼이 답변해 드리며 데이터 작업에 대한 추가 배경 정보를 위해 해당 채널에서 토론 기록을 검토하실 수 있습니다.

또한 해당 채널에서 핵심 기여자 EPETE가 친절하게 준비한 다양한 체인 및 저장소에 걸친 NEFI 토큰 분석에 대한 주간 보고서 모음을 찾을 수 있습니다.
