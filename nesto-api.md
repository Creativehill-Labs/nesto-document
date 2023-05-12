# NESTO API

이 페이지는 타사 사이트의 웹 애플리케이션, 대시보드 및 페이지를 지원하는 Nesto의 REST API의 기능 및 작동에 대한 자세한 내용을 제공합니다.

[https://api.Nesto.finance/](https://api.beefy.finance/) 에서 API에 액세스할 수 있으며 공개 리포지토리는 [Nesto GitHub](https://github.com/beefyfinance/beefy-api) 에서 사용할 수 있습니다 . API는 MIT 라이선스에 따라 유지 관리되며 자세한 내용은 [GitHub 저장소](https://github.com/beefyfinance/beefy-api/blob/master/LICENSE) 에서 확인할 수 있습니다 .

## 종료포인트

당사의 API는 웹 앱, 백엔드 및 대시보드뿐만 아니라 일부 타사 서비스에 필요한 전체 데이터 선택을 포괄하는 다양한 퍼블릭 엔드포인트를 제공합니다.

## Nesto Vault 종료포인트

[Ample 애플리케이션](https://app.beefy.finance/) 에서 Vault의 실시간 특성과 성능을 사용자에게 표시하기 위해 개발된 엔드포인트입니다 .

<details>

<summary>GET /Vault</summary>

폐기된(eol) 보관소를 포함하여 각 Beefy 보관소에 대한 실시간 정보를 제공합니다.&#x20;

정보에는 관련 볼트의 이름/ID, 체인, 토큰, 기본 자산, 관련 계약 및 현재 상태에 대한 필드가 포함됩니다. 또한 금고의 안전 점수를 계산하는 데 사용되는 위험 요소 매트릭스에서 가져온 금고의 기능을 나열하는 "위험" 필드도 포함됩니다.

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

</details>
