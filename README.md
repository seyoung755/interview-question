# interview-question
기술면접 대비용 질문 정리 레포지토리입니다 

## 답변할 때 생각할 점
### 최대한 짧고 간결하게 답변한다.
- 너무 디테일하게 말하면 불리해질 수 있다. 핵심을 중심으로 간결하게 답변하고 꼬리질문에 대응하는 게 유리하다.

### 모르는 내용은 솔직하게 모른다고 답변한다.


## 운영체제
<details>
<summary>프로세스와 스레드의 차이에 대해서 설명해주세요.</summary>
<div markdown="1">       


프로세스는 메모리에 올라가서 실행되고 있는 프로그램입니다. 모든 프로세스는 code/data/heap/stack으로 이루어진 독자적인 주소공간을 가지고 있습니다.
반면 스레드는 프로세스 내에서 가지는 실행의 흐름입니다. 하나의 프로세스에서 여러 스레드를 통해 동시에 여러 작업을 처리할 수 있습니다.
스레드들은 프로세스의 스택을 제외한 주소 공간을 공유할 수 있습니다.
그렇기 때문에 공유 영역의 자원을 사용할 때는 동기화 문제를 염두에 두어야 합니다.

</div>
</details>

<details>
<summary>스레드에 대해서 설명해주세요</summary>
<div markdown="1">


스레드는 프로세스 내에서 실행되는 흐름의 단위입니다. 프로세스의 주소 영역 중 code, data, heap을 공유합니다.
독자적인 실행을 위해 PC와 stack 등의 영역은 독자적으로 가집니다. 이런 특성 때문에 스레드 간 context switch는 프로세스의 경우보다 가볍습니다.
그렇지만 한 프로세스 내에서 메모리 영역을 공유하고 있으니, heap 영역의 객체를 사용할 때 동기화 문제가 발생할 수 있습니다.
때문에 싱글 스레드에서는 고려하지 않던 동기화 문제 해결을 위해 lock, semaphore, monitor 등의 방법을 고민해야합니다.

그리고 무조건 스레드를 만드는 것이 유리하냐? 라고 생각할 수 있습니다.
하지만 스레드를 만드는 것 또한 비용이 많이 드는 작업이므로 스레드를 무조건 많이 만든다고 해서 좋지 않습니다.
이론 상 코어의 개수만큼만 스레드가 동시에 작업이 가능하므로 코어의 개수에 맞게 스레드를 설계하는 것이 좋습니다.
이와 관련하여 하이퍼쓰레딩 기술은 한 코어당 두 개의 스레드를 작업할 수 있도록 하여 멀티태스킹 능력을 향상시켰습니다.

</div>
</details>

<details>
<summary>* 동기와 비동기, 블로킹과 논블로킹의 차이에 대해 설명해주세요.</summary>
<div markdown="1">


먼저 동기와 비동기에 대해 설명해보겠습니다. 동기는 Synchronous이므로 시간을 맞춘다는 의미입니다.
메서드를 리턴하는 시간에 결과를 전달받는 시간과 일치하면 동기, 일치하지 않으면 비동기입니다.
리턴하는 시점과 결과를 전달받는 시점이 다르다는 것은 무엇일까요?

이 때 등장하는 개념이 Future입니다. 비동기적으로 실행한 함수의 결과는 일반적인 타입으로 받아올 수 없습니다.
Java에서는 Future라는 형태로 비동기적으로 실행한 함수의 결과를 받아올 수 있습니다.
리턴하는 시점이 아닌, 그 결과를 필요해서 꺼낼 때 전달받으므로 비동기적으로 실행한다고 볼 수 있습니다.

이 때 비동기 작업의 결과가 출력될 때 직접 꺼내는 것이 아닌 콜백을 통해 어떤 작업을 실행하도록 할 수도 있습니다.
자바의 `ListenableFuture` 라는 인터페이스를 활용하여 콜백을 등록하거나, `CompletableFuture`의 `then~()`를 사용할 수도 있습니다.

블로킹과 논블로킹은 함수 호출 후 제어권을 돌려받는 지 아닌지에 대한 영역입니다.
https://www.youtube.com/watch?v=HKlUvCv9hvA

위 링크에 따르면, 내가 직접 제어할 수 없는 대상을 상대하는 방법입니다. I/O를 한다든지, 멀티 스레드 동기화에 해당합니다.
쉽게 말하면 어떤 작업을 시킨 다음 작업이 모두 끝날 때까지 기다렸다가 실행하는 방식입니다.
논블로킹 방식은 어떤 작업을 시킨 다음 곧장 빠져나와서 자신의 작업을 진행합니다.

즉, 다른 주체가 작업할 때 자신의 제어권이 있는지 없는지를 기준으로 나뉩니다.

한 마디로 요약하면 동기/비동기는 결과의 반환 시점, 블록/논블록은 제어권에 대한 기준입니다.

</div>
</details>

<details>
<summary>멀티스레드와 논블로킹 I/O</summary>
<div markdown="1">

한 프로세스에 스레드가 3개 존재한다고 합시다. T1은 파일을 읽어오는 역할을 담당합니다.
T1의 작업 소요 시간은 읽어오는 파일의 크기와 직결됩니다. 크기가 크다면, 이 파일을 일부 단위로 쪼개서 읽어와야할 수도 있습니다.
T2는 읽어온 파일이 영상이라면, 영상 처리를 하는 작업을 합니다.
보통은 영상을 읽는 것보다 처리를 하는 것이 오래 걸립니다.
그러므로 영상 처리와 영상 읽기 작업을 별도의 스레드에서 처리하는 것이 좋습니다.
그 다음 처리한 영상을 전송하는 작업을 한다고 합니다.
T3는 소켓을 열어서 통신하는 역할을 담당합니다.

이 경우에 T1 -> T2 -> T3의 작업은 순차적으로 이루어져야 합니다. 이 때 등장하는 개념이 동기화입니다.
이 작업이 모두 순서에 맞게 동기화가 되어야 정상적인 작업의 흐름대로 진행되기 때문입니다.

동기화를 위해서 먼저 `Queue`를 사용합니다.
T1은 파일을 읽어서 `Queue 1`에 등록합니다. T2는 루프를 돌면서 `Queue 1`을 감시합니다.
`Queue 1`에 처리할 데이터가 생기면, T2는 영상을 처리해서 `Queue 2`에 등록합니다.
이 때, T2처럼 `Queue 2`를 보고 있던 T3는 그제서야 소켓을 열어 해당 file을 전송합니다.

이 때 주의할 점은 T1, T2 간에, T2, T3 간에 동시에 `Queue`를 접근할 수 있습니다.
그렇기 때문에 항상 `Queue`에서 뭔가를 꺼내거나 삽입할 때에는 lock을 거는 형태로 관리하게 됩니다.

</div>
</details>

## 네트워크
<details>
<summary>UDP와 TCP의 차이점에 대해 설명해주세요</summary>
<div markdown="1">       

UDP는 비연결형 프로토콜로, 신뢰성 있는 통신을 지원하지 않습니다. 송신 측에서는 단지 데이터를 전송할 뿐 수신자가 잘 받았는지 확인하지 않습니다.
(듣고있든 말든 갑자기 전화가 걸려와서 말하고 끊는다)
그렇기 때문에 성능에 큰 이점이 있습니다. 연결이 이루어지지 않기 때문에 1:1, 1:N, N:M 등으로 통신할 수 있습니다. 
지원하는 기능이 없으므로 TCP에 비해 Header 크기가 작고, checksum 필드를 통해 오류 체크정도는 할 수 있습니다.

반면 TCP는 연결지향 프로토콜로 송신측과 수신측이 미리 연결된 상태에서 신뢰성 있는 통신을 하게 됩니다.
(전화를 받으면, 안부를 묻고(3-way handshaking) 용건이 끝나면 인사를 하고(4-way handshaking) 대화를 종료한다.)
TCP는 흐름 제어, 혼잡 제어, 순서 보장 등의 기능을 지원하여 송신 측의 데이터가 잘 전달될 수 있도록 보장해줍니다. 
서버와 클라이언트는 1:1로 연결되는 전 이중 방식이며, 점대점 방식입니다.

</div>
</details>

<details>
<summary>브라우저에 www.google.com을 쳤을 때 일어나는 일</summary>
<div markdown="1">       

먼저 브라우저는 주소창을 통해 들어온 uri를 파싱하여 호스트를 알아내고 HTTP Request message를 만들어 OS에게 전송을 요청합니다.
이 때, 호스트의 이름을 통해 IP 주소를 알아내기 위해 DNS 서버에 질의합니다.
(여기서 공유기를 쓴다면 공유기에 요청하고, 공유기가 DNS에 질의하는 경우도 있습니다.
또는 ISP가 제공해주는 DNS에 질의하게 됩니다.)
질의하기 전에 호스트 이름이 cache되었는지 브라우저나 운영체제 캐시를 확인합니다. 
그리고 없다면 루트 네임서버부터 서브 도메인 순으로 찾게 됩니다.

이렇게 찾은 IP 주소를 통해 TCP 연결을 하고 소켓을 통해 요청 메세지를 전달합니다.
(소켓 연결 시 http는 80포트, https는 443 포트로 연결합니다. 
이 때, https는 tcp handshake 뒤에 TLS handshake를 진행합니다.)
서버는 요청 메세지를 받고 응답하는 HTTP 메세지를 소켓을 통해 클라이언트에게 전달합니다.

</div>
</details>

<details>
<summary>TLS handshake는 어떻게 일어나는가?</summary>
<div markdown="1">       

TLS handshake는 https 연결을 위해 TCP handshake 이후에 발생합니다.
먼저 클라이언트는 자신이 지원하는 암호화 제품군과 클라이언트 무작위 문자열을 서버에게 보냅니다.

서버는 공개 키와(포함하지 않을 수도 있음) 서버 인증서를 클라이언트에 전달합니다. 
클라이언트는 인증서를 통해 서버가 내가 요청한 서버가 맞는지, 검증된 서버가 맞는지 확인합니다.

그리고 클라이언트는 예비 마스터 암호는 공개 키로 암호화되어 있고, 서버가 개인키로만 해독할 수 있습니다.

클라이언트와 서버는 클라이언트 무작위, 서버 무작위, 예비 마스터 암호를 통해 세션 키를 만듭니다.
이 세션 키를 통해 암호화된 완료 메시지를 전송하고, 클라이언트로 알고있는 정보로 복호화하여 핸드셰이크가 완료됩니다.
그리고 세션 키를 통한 대칭 키 방식으로 통신을 진행합니다.

이 때, 계속 공개 키 방식이 아닌 대칭키 방식을 쓰는 이유는, 공개 키 방식에 들어가는 오버헤드가 크기 때문입니다.
한 번 공개 키 방식으로 대칭 키를 교환한 이후에는 http 방식으로 메세지를 교환하게 됩니다.  

</div>
</details>

<details>
<summary>CORS에 대해서 설명해주세요</summary>
<div markdown="1">       

CORS는 Cross-Origin Resource Sharing으로 서로 다른 도메인 간 자원 공유를 말합니다.
예를 들어 클라이언트 서버의 도메인 A에서 도메인 B인 WAS로 자원을 요청하여 받는 경우에 발생합니다.
브라우저는 요청한 도메인과 받는 도메인이 다른 경우에 안전하지 않은 응답이라고 판단하여 이를 버리게 됩니다.

이를 해결하려면, WAS에 응답을 보내줄 때 올바른 CORS 헤더를 추가하여 보내야 합니다.
응답을 받는 클라이언트의 도메인을 헤더에 추가해줘야 합니다.

</div>
</details>

## 데이터베이스
<details>
<summary>인덱스는 무엇이고 왜 사용하나요?</summary>
<div markdown="1">       

인덱스는 관계형 데이터베이스에서 테이블을 효과적으로 저장하기 위한 자료구조입니다. 테이블은 disk에 저장되는데, disk는 random I/O에 매우 지연이 발생하는 구조입니다.
DBMS도 데이터베이스 테이블의 데이터를 일일이 가져오려면 random access가 많이 발생하여 시간이 오래 걸린다.
인덱스의 목적은 random access를 줄여 데이터를 찾아오는 시간을 줄이는 데에 있습니다.

기본적으로 MySQL에서는 기본적으로 B+ Tree 형태로 인덱스를 만듭니다. 칼럼의 값과 해당 레코드가 저장된 주소를 key-value로 삼아 인덱스를 만든다.

장점으로는 목적에서 알 수 있듯이, SELECT 쿼리의 성능이 빨라집니다. 특히 범위 검색 연산을 수행하는 속도를 크게 향상시켜 줍니다.
단점으로는 자료구조를 추가로 만들기 때문에 INSERT, UPDATE, DELETE 발생 시 인덱스가 없을 때보다 시간이 추가로 들게 됩니다.

</div>
</details>

<details>
<summary>인덱스를 사용하면 좋은 경우는 언제일까요?</summary>
<div markdown="1">       

인덱스의 성능은 Selectivity에 좌우됩니다. 인덱스로 조회한 결과가 너무 많으면, 결국은 조회한 결과를 모두 random access하게 된다.
보통은 전체 레코드의 15~30% 이내로 조회될 때만 효과적으로 인덱스를 사용할 수 있다.
조회 결과가 그 이상 넘어갈 때는 MySQL Optimizer가 내부적으로 그냥 full scan을 진행한다.
그리고 인덱스를 실제로 잘 타고 있는지 쿼리 플랜을 활용하면 알 수 있다.

</div>
</details>

<details>
<summary>트랜잭션의 격리 수준에 대해 설명해주세요</summary>
<div markdown="1">       

트랜잭션의 격리 수준이란 서로 다른 트랜잭션 간 얼마나 떨어뜨려놓냐에 대한 레벨입니다.

가장 낮은 단계인 `Read Uncommitted`는 다른 트랜잭션에서 commit하지 않은 내용도 읽어올 수 있습니다.
이렇게 되면, commit되지 않고 rollback 된 내용이라고 읽어와서 사용하여 정합성이 깨어질 수 있습니다.
이런 현상을 `dirty read`라고 합니다.

다음 단계인 `Read Committed`는 다른 트랜잭션에서 commit한 내용만 읽어올 수 있는 격리 수준입니다.
이렇게 되면 dirty read 문제는 발생하지 않습니다. 하지만, 트랜잭션 A가 커밋하기 전에 읽어온 내용과 
커밋 이후에 읽어온 내용이 달라지는 `non-repeatble read` 현상이 발생합니다.

다음 단계인 `Repeatable read`는 자신의 트랜잭션보다 이전에 시작한 트랜잭션의 내용만 읽어올 수 있습니다.
그렇기 때문에 한 트랜잭션 내에서 한번 읽어온 값은 계속 같은 값만 읽어갈 수 있도록 한다. 
UPDATE가 일어나면 UNDO 영역에 백업해두고 실제 레코드를 변경한다. 그리고 UNDO에 있는 레코드를 읽어옵니다.
MySQL의 InnoDB에서는 이 UNDO와 레코드 단위 잠금을 통해 MVCC를 구현합니다.
하지만, INSERT/DELETE에 대해서는 정합성이 깨지게 되는데 이를 `Phantom read`라고 합니다.

마지막 단계인 `Serializable`은 모든 트랜잭션이 직렬적으로 실행되도록 하는 격리수준입니다.
한 마디로 한 트랜잭션이 테이블에 접근하고 있으면, 다른 트랜잭션은 접근하지 못하고 기다려야 합니다.
이 경우 모든 이상현상이 발생하지 않지만 성능에 큰 문제가 생겨서 보통 사용하지 않습니다.

</div>
</details>

<details>
<summary>ACID에 대해서 설명해주세요</summary>
<div markdown="1">       

ACID란 트랜잭션이 안전하게 수행되기 위한 성질입니다.

Atomicity는 트랜잭션이 원자적으로 실행되어야 한다는 성질입니다. 트랜잭션의 작업은 모두 성공하거나 모두 실패해야 합니다.
Consistency는 트랜잭션이 테이블에 변경 사항을 적용할 때 미리 정의되거나 예측할 수 있는 방식만 취합니다. 트랜잭션 전후로 제약 조건을 모두 만족해야 합니다.
Isolation은 모든 트랜잭션이 다른 트랜잭션으로부터 독립되어야 한다는 뜻이다. 
Durability는 트랜잭션의 결과가 로그로 남아서 영속적으로 기록된다는 성질이다. 도중에 오류가 발생하더라도 로그가 남아 장애를 복구할 수 있도록 해야 한다.

</div>
</details>

## 자바
<details>
<summary>* 자바의 Concurrent 패키지는 어떻게 동기화를 하는가?</summary>
<div markdown="1">       

concurrent 패키지의 자료구조들은 모두 멀티스레드 환경에서 동기화가 되도록 구현되어 있습니다.
그럼 어떻게 동기화가 되는 것일까요?
Java의 `ConcurrentLinkedQueue`에 보면 이 자료구조는 아래 링크에 따라 효율적인 논블로킹 알고리즘을 채용했다고 합니다.
https://www.cs.rochester.edu/~scott/papers/1996_PODC_queues.pdf

대략적인 내용은 논블로킹이 되는 구조가 성능에 유리하다는 것이고, lock-free한 알고리즘인 CAS(Compare-And-Swap)이라는 알고리즘이 소개됩니다.
Compare-And-Swap이란 주어진 값과 메모리에 있는 값이 동일하다면 값을 업데이트하고 그렇지 않으면 하지 않는 것입니다.
이는 `synchronized` 처럼 임계 영역에 도달하면 블로킹 시키는 것이 아니라, 모든 스레드를 논블로킹으로 접근할 수 있도록 하되,
작업 시점의 기준값과 메모리 상의 값을 비교하여 일치하면 작업을 수행하고, 일치하지 않으면 중간에 다른 스레드가 끼어들었다고 판단하여 재시도를 합니다.
(`AtomicInteger`의 `getAndSetInt()`를 보면 do-while 문을 통해, compareAndSet이 true일때까지 반복합니다.)

또한, 메모리 상의 값과 비교하기 위해 각 스레드가 캐시가 아닌 메모리에 직접 기록할 수 있도록 `volatile` 키워드를 사용합니다.
매 번 변경된 데이터는 메모리 상에 반영되므로 모든 스레드가 동일한 메모리 상의 값을 참조할 수 있도록 합니다.

일반적으로 lock을 사용하는 것보다 매우 빠르면서도 스레드 세이프하다는 장점이 있습니다.
그러나 compare-and-swap은 `ABA 문제`를 만날 수 있습니다.
compare하는 순간에 old value와 이미 수정된 값이 같은 현상입니다.
이 때 별도의 카운터를 통해 값이 갱신될 때마다 수정하여, 값이 같더라도 카운터 값이 다르면 수정하지 않는 식으로 해결할 수 있다고 합니다.

</div>
</details>

## Spring
<details>
<summary>* 스프링의 IoC, DI는 왜 할까?</summary>
<div markdown="1">       
결론적으로 OCP, DIP를 준수하여 좋은 객체지향 코드를 짜기 위함입니다.
객체지향에서는 객체간의 의존관계를 통해 하나의 기능을 수행하게 됩니다.
그런데 의존관계의 설정을 사용자 코드에서 하게 되면, 의존관계가 바뀔 때마다 코드를 수정해야 합니다.
이런 것이 OCP를 위반하는 코드이므로, 스프링에서는 IoC 컨테이너를 통해 빈을 등록해놓고 필요한 의존관계를 빈에 주입해주게 됩니다.

개발자가 객체를 생성하거나 의존관계를 설정하지 않고 스프링이 알아서 해주기 때문에 이것을 IoC라고 부르게 됩니다.
</div>
</details>

### `*`가 붙은 내용은 이해가 더 필요한 내용입니다.
