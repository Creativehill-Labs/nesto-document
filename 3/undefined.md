# 레지스트리 컨트랙트 위임

DelegateRegistry [컨트렉트는](https://github.com/gnosis/delegate-registry/blob/main/contracts/DelegateRegistry.sol) Gnosis에서 개발하고 Snapshot Labs에서 사용하여 Snapshot 기반 거버넌스 공간에서 투표 위임을 용이하게 하는 거버넌스 스마트 컨트렉트입니다. 사용자는 BNB 체인에서 다른 사용자에게 투표 권한을 위임하여 투표권을 사용하여 다른 사용자에게 권한을 부여할 수 있습니다. 사용자는 언제든지 위임을 제거할 수도 있습니다.

이 메커니즘을 통해 커뮤니티의 신뢰할 수 있는 목소리는 지지자의 적은 노력으로 지지를 활용할 수 있습니다. 또한 시간이 부족한 사람들이 발생하는 모든 제안에 참여할 필요 없이 투표권이 거버넌스에 참여하도록 할 수 있습니다.

### 컨트렉트 매핑

DelegateRegistry 컨트렉트는 무엇보다도 컨트렉트의 "위임" 매핑에 저장되는 기존 위임에 대한 정보 저장소입니다.

```
// The first key is the delegator and the second key a id. 
// The value is the address of the delegate 

mapping (address => mapping (bytes32 => address)) public delegation;
```

효과적으로 각 위임은 위임자의 주소(즉, 투표 권한을 위임받은 사용자)를 참조하여 저장되며, 이는 관련 스냅샷 공간 ID(bytes32 값으로 저장됨) 및 위임자의 주소(즉, 투표 권한을 위임받은 사용자)에 매핑됩니다. 의결권이 위임됨).

[Snapshot Labs의 DelegateRegistry 컨트렉트에서 관리하는 모든 위임에 대한 자세한 내용은 여기에서](https://thegraph.com/hosted-service/subgraph/snapshot-labs/snapshot) Snapshot Subgraph를 탐색할 수 있습니다 .

컨트렉트 이벤트

DelegateRegistry 컨트렉트는 일반적인 작업에서 두 ​​가지 가능한 이벤트를 내보냅니다.

### SetDelegate

컨트렉트의 의미[setDelegate()](https://docs.beefy.finance/developer-documentation/third-party-contracts/delegateregistry-contract#setdelegate-1)함수가 성공적으로 호출되었으며 결과적으로 호출자가 새 대리자를 선택했습니다.

```
// Using these events it is possible to process the events to build up reverse 
lookups.
// The indeces allow it to be very partial about how to build this lookup (e.g. only 
for a specific delegate).

event SetDelegate(address indexed delegator, bytes32 indexed id, address indexed 
delegate);

```

### ClearDelegate

아래 중 하나임을 나타냅니다.[컨트렉트 기능](https://docs.beefy.finance/developer-documentation/third-party-contracts/delegateregistry-contract#contract-functions)이(가) 성공적으로 호출되었으며 결과적으로 이전 대리인이 호출자에 대해 승인되었습니다.

<pre><code>// Using these events it is possible to process the events to build up reverse 
lookups.
// The indeces allow it to be very partial about how to build this lookup (e.g. only 
for a specific delegate).

event ClearDelegate(address indexed delegator, bytes32 indexed id, address indexed 
delegate);
<strong>
</strong>
</code></pre>

## 컨트렉트 기능&#x20;

DelegateRegistry 컨트렉트의 기능은 매우 간단하며 대리자를 설정하고 제거하는 두 가지 기능으로 구성됩니다.

### setDelegate()

대리인을 설정하려면 컨트렉트에 두 가지 입력이 필요합니다. 관련 스냅샷 공간 ID(bytes32 값으로 저장됨); 및 대리인의 주소(즉, 투표권이 위임된 사용자). 그런 다음 호출을 실행하기 전에 몇 가지 요구 사항(예: 사용자가 자신, null 주소 또는 현재 대리인에게 위임하지 않음)에 대해 입력을 테스트합니다.

```
/// @dev Sets a delegate for the msg.sender and a specific id.
///      The combination of msg.sender and the id can be seen as a unique key.
/// @param id Id for which the delegate should be set
/// @param delegate Address of the delegate

function setDelegate(bytes32 id, address delegate) public {
        require (delegate != msg.sender, "Can't delegate to self");
        require (delegate != address(0), "Can't delegate to 0x0");
        address currentDelegate = delegation[msg.sender][id];
        require (delegate != currentDelegate, "Already delegated to this address");
        
        // Update delegation mapping
        delegation[msg.sender][id] = delegate;
        
        if (currentDelegate != address(0)) {
            emit ClearDelegate(msg.sender, id, currentDelegate);
        }

        emit SetDelegate(msg.sender, id, delegate);
}

```

호출이 성공하면 함수는 위임 매핑을 새 위임 주소로 업데이트합니다. 그런 다음 사용자가 이전에 다른 사용자에게 위임했는지 확인하고 위임했다면 다음을 내보냅니다.[ClearDelegate](https://docs.beefy.finance/developer-documentation/third-party-contracts/delegateregistry-contract#cleardelegate) 이전 대리인이 제거되었음을 나타내는 이벤트입니다. 마지막으로 그것은 [SetDelegate](https://docs.beefy.finance/developer-documentation/third-party-contracts/delegateregistry-contract#setdelegate) 새 대리인이 추가되었음을 나타내는 이벤트입니다.

이 기능은 또한 컨트렉트를 사용하도록 요구하지 않습니다.[clearDelegate()](https://docs.beefy.finance/developer-documentation/third-party-contracts/delegateregistry-contract#cleardelegate-1)기존 대리자를 제거하는 데만 사용되는 함수입니다.

### clearDelegate()

현재 대리인을 지우려면 컨트렉트에 관련 스냅샷 공간 ID(bytes32 값으로 저장됨)인 하나의 입력이 필요합니다. 그런 다음 호출을 실행하기 전에 대리인이 실제로 설정되었는지 확인하기 위해 테스트합니다.

```
/// @dev Clears a delegate for the msg.sender and a specific id.
///      The combination of msg.sender and the id can be seen as a unique key.
/// @param id Id for which the delegate should be set

function clearDelegate(bytes32 id) public {
        address currentDelegate = delegation[msg.sender][id];
        require (currentDelegate != address(0), "No delegate set");
        
        // update delegation mapping
        delegation[msg.sender][id] = address(0);
        
        emit ClearDelegate(msg.sender, id, currentDelegate);
}
```

호출이 성공하면 함수는 위임 매핑을 null 주소로 업데이트한 다음(즉, 사용자가 투표권을 위임하지 않았음을 나타냄) 다음을 내보냅니다.[ClearDelegate](https://docs.beefy.finance/developer-documentation/third-party-contracts/delegateregistry-contract#cleardelegate)이전 대리자인이이제거되었음을 나타내는 이벤트입니다.

## 위임 연습

사용자가 DelegateRegistry 컨트렉트와 상호 작용하기 위해 채택할 수 있는 두 가지 주요 방법이 있습니다. Snapshot 인터페이스를 통해 상호 작용하거나 컨트렉트와 직접 상호 작용합니다(예: 관련 블록 탐색기를 통해). 다음은 각 방법에 대한 간략한 설명입니다.

### 스냅샷 인터페이스를 통해

1.[스냅샷 인터페이스 위임 페이지](https://vote.beefy.finance/#/delegate/beefydao.eth) 로 이동합니다 .

2.지갑을 사이트에 연결하면 주소를 감지하고 전화를 걸 수 있습니다.[setDelegate()](https://docs.beefy.finance/developer-documentation/third-party-contracts/delegateregistry-contract#setdelegate-1)기능.

3.지갑으로 BSC 네트워크의 BNB 체인에 연결되어 있는지 확인하세요. **다른 체인은 작동하지 않습니다.**

4.투표권을 위임할 사용자의 주소 또는 ENS 이름을 입력합니다.

5.원하는 경우 "특정 공간으로 위임 제한"을 선택하여 위임을 Nesto Snapshot 공간으로만 제한할 수 있습니다. 제한하려면면 다음 공백을 입력하십시오: Nestodao.eth.

6.지갑에서 거래를 시작하려면 "확인"을 클릭하십시오. 이것은 쓰기 트랜잭션(예: 블록체인에 저장하기 위한 정보 제출)이므로 트랜잭션을 용이하게 하기 위해 소량의 가스를 지불해야 합니다.

\-----------이미지영역--------------

> Snapshot 인터페이스의 위임 페이지는 투표권을 위임하는 깔끔하고 간단한 방법을 제공합니다.

7.거래가 완료되면 NEFI 토큰이 배포된 모든 체인에서 제공한 사용자 주소에 성공적으로 위임됩니다.

### 블록 탐색기를 통해

1.[BscScan](https://bscscan.com/address/0x469788fE6E9E9681C6ebF3bF78e7Fd26Fc015446) 의 DelegateRegistry 컨트렉트 주소로 이동합니다 .

2.컨트렉트 페이지의 "컨트렉트" 탭에서 "컨트렉트 작성" 하위 탭으로 이동합니다(아래 참조).

3.지갑을 연결하고 컨트렉트와 상호 작용하려면 "Web3에 연결" 버튼을 선택하십시오.

4.지갑이 BSC 네트워크의 BNB 체인으로 설정되어 있는지 확인하십시오. **다른 체인은 작동하지 않습니다.**

\-----------이미지영역--------------

> BscScan 인터페이스는 모든 위임 방법, 이벤트 및 트랜잭션에 대한 전체 액세스를 제공하지만 대부분의 사용자에게는 분명히 덜 친숙합니다.

5.아래로 스크롤하여 [setDelegate()](https://docs.beefy.finance/developer-documentation/third-party-contracts/delegateregistry-contract#setdelegate-1)기능을 선택하고 다음과 같은 필수 세부 정보를 입력합니다.

&#x20;   1.id(bytes32) - 위임할 스냅샷 공간의 주소(예: Nesto Space ID: 0x626565667964616f2e657468).

&#x20;   2.위임(주소) - 투표권을 위임하려는 사용자의 주소입니다.

6\. 지갑에서 거래를 시작하려면 "쓰기"를 클릭하십시오. 이것은 쓰기 트랜잭션(예: 블록체인에 저장하기 위한 정보 제출)이므로 트랜잭션을 용이하게 하기 위해 소량의 가스를 지불해야 합니다.

\-----------이미지영역--------------

> 각 블록 탐색기의 기능은 [컨트렉트 기능](https://docs.beefy.finance/developer-documentation/third-party-contracts/delegateregistry-contract#contract-functions)위 섹션.

7.거래가 완료되면 NEFI 토큰이 배포된 모든 체인에서 제공한 사용자 주소에 성공적으로 위임됩니다.

### 컨트렉트

DelegateRegistry 컨트렉트 및 Nesto 스냅샷 공간은 다음 주소에 배포됩니다.

* DelegateRegistry - [0x469788fE6E9E9681C6ebF3bF78e7Fd26Fc015446](https://bscscan.com/address/0x469788fE6E9E9681C6ebF3bF78e7Fd26Fc015446) .<mark style="color:red;">\*\*</mark>
* <mark style="color:red;">Nesto Space ID - 0x626565667964616f2e657468 또는 Nestodao.eth.</mark>

### 추가 정보

Nesto의 거버넌스에서 투표 위임이 작동하는 방식에 대한 자세한 내용은 다음을 참조하십시오.[통거버넌스](https://docs.beefy.finance/community-governance/governance)  페이지 (구체적으로[내 투표권을 어떻게 위임합니까?](https://docs.beefy.finance/community-governance/governance#how-do-i-delegate-my-vote) 그리고 [대리인이 되려면 어떻게 해야 합니까?](https://docs.beefy.finance/community-governance/governance#how-do-i-become-a-delegate)). [이 Google 시트](https://docs.google.com/spreadsheets/d/1sJH4jg3eEEJDpbws55qUmzPeLDiDuL\_5OAQobSn7m2Y/edit?usp=sharing) 에서 현재 유지 관리되는 투표 대리인 목록을 찾을 수도 있습니다 .

[Snapshot Labs의 DelegateRegistry 컨트렉트에서 관리하는 모든 위임에 대한 자세한 내용은 여기에서](https://thegraph.com/hosted-service/subgraph/snapshot-labs/snapshot) Snapshot Subgraph를 탐색할 수 있습니다 . 자세한 내용은 Snapshot [문서](https://docs.snapshot.org/guides/delegation) 에서 확인할 수 있습니다 .

