---
description: Nesto inspirit
---

# neSPIRIT

## SPIRIT이란 무엇인가요?

SPIRIT은 SpiritSwap의 네이티브 토큰으로, Fantom 블록체인 기반의 분산형 거래소입니다. SPIRIT은 고정된 공급 및 감소하는 배출 모델을 가지고 있습니다.

사용자는 SPIRIT 토큰을 일정 기간 동안 SpiritSwap에 지분을 갖고 잠금으로 보유할 수 있습니다. 이를 InSPIRIT이라고 합니다. InSPIRIT 보유자는 다음과 같은 다양한 혜택을 받을 수 있습니다. 첫째, 주당 프로토콜 수익의 일정 비율을 받습니다. 둘째, 프로토콜 거버넌스 및 볼트 인센티브 게이지의 투표권이 있습니다. 셋째, Spirit Swap 팜으로부터 최대 2.5배의 보상 증가를 받을 수 있습니다. 넷째, 제3자 인센티브 게이지 투표에 대한 영향력을 사용가능합니다.

inSPIRIT은 전송할 수 없으며 잠금이 끝나면 보유한 양이 꾸준히 감소합니다. 또한 사용자는 시간 잠금이 끝날 때까지 잠긴 SPIRIT 포지션을 삭제하거나 전송할 수 없습니다.

## neSPIRIT은 무엇인가요?

neSPIRIT은 inSPIRIT의 고정된 비프 에스크로 버전으로, SPIRIT에 축적하기 위한 토큰입니다. 이 토큰은 inSPIRIT의 모든 혜택을 제공하지만 SpiritSwap의 잠금 메커니즘에 제한을 받지 않습니다. neSPIRIT은 SPIRIT에 1:1로 완전히 지원되지만, 영구적으로 SPIRIT 토큰으로 다시 인출할 수 없으며, 대신 ERC-20 토큰으로 전송 가능하므로 분산 거래소의 다른 토큰과 교환할 수 있습니다. 이를 통해 beSPIRIT 보유자는 언제든지 자신의 포지션을 변경할 수 있습니다.

## neSPIRIT은 어떻게 얻나요?

사용자는[ neSPIRIT 볼트 페이지](https://app.beefy.finance/vault/beefy-binspirit)에서 1:1 비율로 neSPIRIT을 생성할 수 있습니다. 만약 neSPIRIT을 사는 것이 더 이익이 된다면, nesto의 smart minter는 그대로 실행 할것이며, 사용자는 그들의 SPIRIT에 더 많은 neSPIRIT을 얻을 수 있습니다. 사용자는 또한 분산된 거래소에서 neSPIRIT을 구매할 수 있습니다.

\-----------이미지영역--------------

> neMINT는 가장 수익성이 높은 전략을 선택합니다.

## neSPIRIT은 어떻게 작동하나요?

Nesto는 SPIRITswap의 예치금을 가능한 한 오랫동안 사용하기 위해 SPIRIT을 즉시 잠그고, 그 자금을 SPIRIT에 투입하여 SpiritSwap의 프로토콜 수익을 수취하고 금고를 활성화하며 인센티브 배출에 투표합니다.&#x20;

이 프로토콜 수익은 SPIRIT로 지급되며, 이를 Nesto가 자동으로 청구한 다음 neSPIRIT 보관소(Vault)로 반환합니다. neSPIRIT이 peg를 초과하는 경우, 추가적인 neSPIRIT을 분산 거래소에서 구입하고 두 경우 모두 neSPIRIT를 보관소(Vault)로 다시 보관합니다. 비피는 neSPIRIT 계약을 통해 강화된 SpiritSwap 금고의 모든 예치금, 인출 및 수확 보상을 inSPIRIT 보유자가 강화 혜택을 받을 수 있도록 전달합니다.&#x20;

neSPIRIT은 SPIRIT에 누적된 모든 보관소(Vault)를 하나의 계약에 보관하며, 해당 계약을 통해 모든 보관소(Vault)를 실행함으로써 각 보관소(Vault)가 최대한 부스트를 받을 수 있도록 보장합니다. 모든 강화된 보상은 개별 Nesto 보관소(Vault)에 의해 유지됩니다.&#x20;

neSPIRIT 계약은 SpiritSwap 거버넌스 제안 및 보관소(Vault) 인센티브 게이지에 대해 Nesto를 대신하여 투표를 제출하며, neSPIRIT 보유자는 게이지에 대해 투표할 수 있습니다. 자세한 내용은 [GaugeStaker 스마트 컨트렉트](../../nesto-1/undefined-1.md)의 설명을 참조하세요.

## neSPIRIT으로 어떻게 수익을 얻나요?

neSPIRIT으로 수익을 벌 수 있는 방법입니다.

1. inSPIRIT을 보유한 사용자들은 Nesto neSPIRIT 금고에 예치하여 더 많은 neSPIRIT을 자동으로 받을 수 있습니다.&#x20;
2. 분산 거래소(예: SpiritSwap, SushiSwap)에서 neSPIRIT을 유동성 풀에 예치하여 거래 수수료와 잠재적 보상을 받을 수 있습니다.&#x20;
3. 스피릿스왑의 SPIRIT-neSPIRIT LP에 입금하고, [비피 스피릿-neSPIRIT LP 금고](https://app.beefy.finance/vault/spirit-binspirit-spirit)에 지분을 보유하면 자동 복리 수수료와 보상을 받을 수 있습니다.

## 하지만 수수료는 어떤가요?

Nesto는 수익률 최적화를 위해 노력하고 있으며, neSPIRIT 금고에 대한 수수료는 표준 수수료로 부과합니다. 또한, 사용자가 SPIRIT을 neSPIRIT으로 변환할 때는 Nesto 수수료가 부과되지 않습니다.

## neSPIRIT은 어떻게 페깅을 유지하나요?

사용자가 자신의 포지션을 청산하고 싶을 경우, 교환을 통해 neSPIRIT를 거래할 수 있습니다. inSPIRIT와는 달리 사용자는 절대로 잠기지 않으며 언제든지 자신의 위치를 벗어날 수 있습니다. 이는 또한 neSPIRIT이 항상 SPIRIT에 고정되지 않고, 느슨한 고정을 유지하도록 설계되었다는 것을 의미합니다.

neSPIRIT이 페깅 가격 이하가 되면, 계약은 SpiritSwap 프로토콜 수익을 청구하여 더 많은 neSPIRIT을 다시 구입하는 데 사용합니다. 이렇게 하면 각 neSPIRIT 보유자의 축적된 SPIRIT 비율이 증가하게 되어, 동일한 스피릿을 SpiritSwap에 직접 잠그면 얻을 수 있는 것보다 더 많은 것을 얻을 수 있습니다.

반면에, neSPIRIT이 페깅가격을 초과하면, 계약은 프로토콜 수익을 사용하여 더 많은 neSPIRIT을 수익으로 발행할 것입니다.

## neSPIRIT로 투표할 수 있나요?

아니요, Nesto는 이전에[ neSPIRIT 투표 시스템과 전용 스냅샷 페이지](https://snapshot.org/#/binspirit.eth)를 구축했지만, Spirit Swap V2로 전환함으로써 보유자에게 최고의 수익을 제공할 수 있는 프로세스를 자동화했습니다. 이제 Beefy는 모든 수익을 neSPIRIT Vault로 직접 반환하여 최소한의 노력으로 높은 수익률을 달성합니다.

Nesto가 절차를 자동화했지만, 여전히 neSPIRIT 대한 직접적인 제안을 받을 수 있습니다. 자세한 내용은 코어 팀에게 디스코드, 텔레그램 또는 트위터를 통해 연락해 보세요.
