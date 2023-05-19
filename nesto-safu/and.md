# 계약 & 타임록

## 주소록     &#x20;

사용되는 모든 주소는 오픈 소스이며 확인 가능합니다. Nesto 체인에서 DeFi 개발에 유용한 주소 모음은 GitHub의[ ~~Address Book~~](https://github.com/beefyfinance/beefy-api/tree/master/packages/address-book)에 저장되어 있습니다.

## 컨트렉트

Vault UI를 통해 전략 주소와 Vault 주소를 쉽게 찾을 수 있습니다. 또한, 모든 Nesto 보관소 컨트렉트는 [dashboard.Nesto.finance](https://dashboard.beefy.finance/)에서 확인할 수 있습니다. 예를 들어,&#x20;

이 대시보드를 사용하여 보관소의 [수확 및 복리 적용 비율](https://github.com/beefyfinance/beefy-api/tree/master/packages/address-book)을 확인할 수 있습니다.

## 오라클

Nesto 컨트렉트는 외부 오라클를 사용하지 않습니다. 간단히 말해서, 오라클의 데이터는 부정확하거나 조작될 수 있으며, 신뢰할 수 없는 오라클은 악용에 이어질 수 있습니다. Nesto 컨트렉트는 자산 가격과 같은 어떠한 형태의 외부 데이터에도 의존하지 않기 때문에, 우리의 보관소는 플래시론 악용에 취약하지 않습니다.

## 컨트렉트 예시

~~l  DAI/USDC/USDT (Curve - Avalanche) vault code: https://snowtrace.io/address/0x79A44dc13e5863Cf4AB36ab13e038A5F16861Abc#code~~

~~l  WBTC (Scream - Fantom) lending strategy code: https://ftmscan.com/address/0x4374207377C1A36e386A757B774D53a0B6Ff2cEE#code~~

~~l  CAKE-BNB (PancakeSwap - BNB Chain) regular strategy code: https://bscscan.com/address/0xDE238C509bcCBCd91B90dE40dF3e25B43A131311#code~~

## 타임록

컨트렉트는 타임록 및 멀티 싸인 개발 ​​지갑으로 보호됩니다. 6시간 타임록은 컨트렉트를 안전하게 유지하기 위해 필요한 변경을 민첩하게 수행하는 데 사용되며 추가 보호 계층으로 타임록은 3/5 서명자 멀티 싸인에 의해 관리됩니다.

~~l  Arbitrum (6 hours):~~ [~~0x6d28afD25a1FBC5409B1BeFFf6AEfEEe2902D89F~~](https://arbiscan.io/address/0x6d28afD25a1FBC5409B1BeFFf6AEfEEe2902D89F)

~~l  Avalanche (6 hours):~~ [~~0x37DC61A76113E7840d4A8F1c1B799cC9ac5Aa854~~](https://snowtrace.io/address/0x37DC61A76113E7840d4A8F1c1B799cC9ac5Aa854)

~~l  BSC (6 hours):~~ [~~0x65CF7E8C0d431f59787D07Fa1A9f8725bbC33F7E~~](https://bscscan.com/address/0x65cf7e8c0d431f59787d07fa1a9f8725bbc33f7e)

~~l  Celo (6 hours):~~ [~~0x5B96bbAca98D777cb736dd89A519015315E00D02~~](https://explorer.celo.org/address/0x5B96bbAca98D777cb736dd89A519015315E00D02/transactions)

~~l  Cronos (6 hours):~~ [~~0x4f4DB83d75876f34fd927d5fa78D5D7b4479E6ce~~](https://cronoscan.com/address/0x4f4DB83d75876f34fd927d5fa78D5D7b4479E6ce)

~~l  Fantom (6 hours):~~ [~~0x847298aC8C28A9D66859E750456b92C2A67b876D~~](https://ftmscan.com/address/0x847298aC8C28A9D66859E750456b92C2A67b876D)

~~l  Fuse (6 hours):~~ [~~0xa9E6E271b27b20F65394914f8784B3B860dBd259~~](https://explorer.fuse.io/address/0xa9E6E271b27b20F65394914f8784B3B860dBd259/transactions)

~~l  HECO (6 hours):~~ [~~0x587479672077fBD7cb08EE1fd13fca6a9ef69d9e~~](https://hecoinfo.com/address/0x587479672077fBD7cb08EE1fd13fca6a9ef69d9e)

~~l  Metis (6 hours):~~ [~~0xdf68Bf80D427A5827Ff2c06A9c70D407e17DC041~~](https://andromeda-explorer.metis.io/address/0xdf68Bf80D427A5827Ff2c06A9c70D407e17DC041/transactions)

~~l  Ammnriver (6 hours):~~ [~~0xc8BD4Ae3d3A69f0d75e3788d2ee557E66EBC98D8~~](https://moonriver.moonscan.io/address/0xc8BD4Ae3d3A69f0d75e3788d2ee557E66EBC98D8)

~~l  Harmony One (6 hours):~~ [~~0x6d28afD25a1FBC5409B1BeFFf6AEfEEe2902D89F~~](https://explorer.harmony.one/address/0x6d28afd25a1fbc5409b1befff6aefeee2902d89f)

~~l  Polygon (6 hours):~~ [~~0x6fd13191539e0e13B381e1a3770F28D96705ce91~~](https://polygonscan.com/address/0x6fd13191539e0e13b381e1a3770f28d96705ce91)

&#x20;

## 개발자 멀티시그

멀티 싸인 개발 ​​지갑은 Vault 전략 업그레이드와 같은 컨트렉트 변경 사항을 배포하는 데 사용됩니다. 이를 통해 Nesto의 개발자가 모든 변경 사항을 승인하는 안전한 워크플로우를 보장합니다.

~~l  Arbitrum:~~ [~~0xf7EC8986c660Fa8269f6440A631B22337f398Ccd~~](https://gnosis-safe.io/app/arb1:0xf7EC8986c660Fa8269f6440A631B22337f398Ccd/)

~~l  Avalanche:~~ [~~0x3A0b8B7a3ea8D1670e000b1Da5bD41373bF8da42~~](https://gnosis-safe.io/app/avax:0x3A0b8B7a3ea8D1670e000b1Da5bD41373bF8da42/balances)

~~l  BSC:~~ [~~0x44b74ED902e6423B51Bd9e44B6e5646749376943~~](https://gnosis-safe.io/app/bnb:0x44b74ED902e6423B51Bd9e44B6e5646749376943/)

~~l  Fantom:~~ [~~0x238dc3781DD668abd5135e233e395885657D304A~~](https://safe.fantom.network/#/safes/0x238dc3781DD668abd5135e233e395885657D304A/)

~~l  Fuse:~~ [~~0xe26a8aC2936F338Fd4DAebA4BD22a7ec86465fE1~~](https://gnosis-safe.fuse.io/fuse:0xe26a8aC2936F338Fd4DAebA4BD22a7ec86465fE1/)

~~l  Harmony:~~ [~~0xE3c985f5e317eFd4aca1f00aa5F1DFEC40b2Af74~~](https://multisig.harmony.one/#/safes/0xE3c985f5e317eFd4aca1f00aa5F1DFEC40b2Af74/)

~~l  Polygon:~~ [~~0x09dc95959978800E57464E962724a34Bb4Ac1253~~](https://gnosis-safe.io/app/matic:0x09dc95959978800E57464E962724a34Bb4Ac1253/)

~~l  Metis:~~ [~~0xFf9810A3dA8a554B84Ed79D67461eCA6Eb3fA9BD~~](https://metissafe.tech/metis-andromeda:0xFf9810A3dA8a554B84Ed79D67461eCA6Eb3fA9BD/)

~~l  Ammnriver:~~ [~~0x1fdd00b45eba7f6d35b92803eaddd68f7cc4a193~~](https://multisig.moonbeam.network/mriver:0x1fdd00b45eba7f6d35b92803eaddd68f7cc4a193/)

&#x20;

## 재무부 멀티시그

Nesto 자금 지출은 신뢰할 수 있는 (커뮤니티) 회원들의 여러 서명이 필요로 하여 보호됩니다. DAO에서 투표를 통해 선출된 다음 회원들이 자금 위원회를 대표합니다: Power, AllTrades, Pablo, mjoaris, TBC, DefiDebauchery 및 YR2150. Nesto 자금에 대한 자세한 정보는 자금 페이지를 참조하십시오.

~~l  Arbitrum:~~ [~~0x3f5eddad52C665A4AA011cd11A21E1d5107d7862~~](https://gnosis-safe.io/app/arb1:0x3f5eddad52C665A4AA011cd11A21E1d5107d7862/balances)

~~l  Aurora:~~ [~~0x088C70Ddff3a3774825dd5e5EaDB356404248d83~~](https://app.safe.global/home?safe=aurora:0x088C70Ddff3a3774825dd5e5EaDB356404248d83)

~~l  Avalanche:~~ [~~0x26dE4EBffBE8d3d632A292c972E3594eFc2eCeEd~~](https://gnosis-safe.io/app/avax:0x26dE4EBffBE8d3d632A292c972E3594eFc2eCeEd/balances)

~~l  BSC:~~ [~~0x7C780b8A63eE9B7d0F985E8a922Be38a1F7B2141~~](https://gnosis-safe.io/app/bnb:0x7C780b8A63eE9B7d0F985E8a922Be38a1F7B2141/balances)

~~l  Canto:~~ [~~0xF09d213EE8a8B159C884b276b86E08E26B3bfF75~~](https://safe.neobase.one/canto:0xF09d213EE8a8B159C884b276b86E08E26B3bfF75/home)

~~l  Celo:~~ [~~0xca807d809f9639cefb3d31a7951cec3ab405a2fd~~](https://www.xdao.app/42220/dao/0xCA807D809f9639CEfb3d31a7951Cec3ab405a2fd)

~~l  Cronos:~~ [~~0xa9721Ae5042482D7a884A2138f580459B680920f~~](https://cronos-safe.org/cro:0xa9721Ae5042482D7a884A2138f580459B680920f/home)

~~l  Ethereum:~~ [~~0xc9C61194682a3A5f56BF9Cd5B59EE63028aB6041~~](https://gnosis-safe.io/app/eth:0xc9C61194682a3A5f56BF9Cd5B59EE63028aB6041/home)

~~l  Emerald:~~ [~~0x8fd0869271d26e6653f5d5650685630f75b6aedf~~](https://www.xdao.app/42262/dao/0x8FD0869271d26E6653f5d5650685630F75b6AEDf)

~~l  Fantom:~~ [~~0xdFf234670038dEfB2115Cf103F86dA5fB7CfD2D2~~](https://safe.fantom.network/#/safes/0xdFf234670038dEfB2115Cf103F86dA5fB7CfD2D2/balances)

~~l  Fuse:~~ [~~0x1C124c2CaB83b3C3B5D0f0899CeeA5e06964599F~~](https://gnosis-safe.fuse.io/fuse:0x1C124c2CaB83b3C3B5D0f0899CeeA5e06964599F/balances)

~~l  Harmony:~~ [~~0x523154a03180FD1CB26F39087441c9F91BcD0389~~](https://multisig.harmony.one/#/safes/0x523154a03180FD1CB26F39087441c9F91BcD0389/balances)

~~l  HECO :~~ [~~0xdbb72c8b7ebdd52a4813b9d262386dfdab69c9ba~~](https://www.xdao.app/128/dao/0xdbB72c8B7eBdD52A4813B9D262386dfDAB69c9bA)

~~l  Kava:~~ [~~0x07F29FE11FbC17876D9376E3CD6F2112e81feA6F~~](https://app.oryy.io/kava:0x07F29FE11FbC17876D9376E3CD6F2112e81feA6F/home)

~~l  Metis:~~ [~~0x0f9602B7E7146a9BaE16dB948281BebDb7C2D095~~](https://metissafe.tech/metis-andromeda:0x0f9602B7E7146a9BaE16dB948281BebDb7C2D095/balances)

~~l  Ammnbeam:~~ [~~0x3E7F60B442CEAE0FE5e48e07EB85Cfb1Ed60e81A~~](https://multisig.moonbeam.network/mbeam:0x3E7F60B442CEAE0FE5e48e07EB85Cfb1Ed60e81A/home)

~~l  Ammnriver:~~ [~~0x617f12E04097F16e73934e84f35175a1B8196551~~](https://multisig.moonbeam.network/mriver:0x617f12E04097F16e73934e84f35175a1B8196551/balances)

~~l  Optimism:~~ [~~0x4ABa01FB8E1f6BFE80c56Deb367f19F35Df0f4aE~~](https://gnosis-safe.io/app/oeth:0x4ABa01FB8E1f6BFE80c56Deb367f19F35Df0f4aE/home)

~~l  Polygon:~~ [~~0xe37dD9A535c1D3c9fC33e3295B7e08bD1C42218D~~](https://gnosis-safe.io/app/matic:0xe37dD9A535c1D3c9fC33e3295B7e08bD1C42218D/balances)

