# 로드 밸런싱, 웹 서버 & WAS 차이

## 로드 밸런싱

### 로드 밸런서란? + L4 vs L7

#### 로드 밸런서(Load Balancer)

![로드 밸런서](https://static.wixstatic.com/media/3aac70_069fdffae211437688d66863b767ccf9~mv2.png/v1/fill/w_568,h_248,al_c,q_85,usm_0.66_1.00_0.01,enc_avif,quality_auto/3aac70_069fdffae211437688d66863b767ccf9~mv2.png)

로드밸런서(Load Balancer)는 클라이언트와 서버 그룹 사이에 위치해 서버에 가해지는 트래픽을 여러 대의 서버에
고르게 분배하여 특정 서버의 부하를 덜어준다. 서버가 하나인데 많은 트래픽이 몰릴 경우 부하를 감당하지 못하고,
서버가 다운되어 서비스가 작동을 멈출 수 있다.
이런 문제를 해결하기 위해서 Scale up(스케일업)과 Scale out(스케일아웃) 방식 중 한 가지를 사용해 해결한다.

<hr />

#### 스케일 업과 스케일 아웃

![스케일 업과 스케일 아웃](https://velog.velcdn.com/images/rladmlduq47/post/3149abff-2eb6-4a8e-a6ca-791f8935b6b5/image.png)

- 스케일 업(Scale Up): 기존 서버의 성능을 향상시키는 방법으로 이는 CPU나 메모리를 업그레이드하는 것과 같은 작업을 포함
- 스케일 아웃(Scale Out): 트래픽이나 작업을 여러 대의 컴퓨터나 서버에 분산시켜 처리하는 방법

|        | 스케일 업                                 | 스케일 다운                                  |
|--------|---------------------------------------|-----------------------------------------|
| 확장성    | 성능 확장에 한계 존재                          | 지속적 확장이 가능                              |
| 서버 비용  | 성능 증가에 따른 비용 증가 폭 큼. 일반적으로 비용 부담이 큰 편 | 비교적 저렴한 서버를 사용하므로, 일반적으로 비용 부담이 적음      |
| 관리 편의성 | 스케일 업에 따른 큰 변화 없음                     | 서버 대수가 늘어날수록 관리 편의성 떨어짐                 |
| 운영 비용  | 스케일 업에 따른 큰 변화 없음                     | 서버 대수가 늘어날수록 운영 비용 증가                   |
| 장애 영향  | 한 대의 서버에 부하가 집중되므로 장애 시 다운 타임 발생      | 부하가 여러 서버에 분산되어 처리됨으로 장애 시 전면 장애 가능성 적음 |

스케일 아웃을 하게 되면 서버가 여러 대가 되기 때문에 각 서버에 걸리는 부하를 고르게 나누기 위해서는
로드밸런싱이 필수적으로 동반되어야 한다. 로드 밸런싱은 여러 서버나 컴퓨터 자원에 작업을 고르게 분산하는 기술이다.
예를 들어 웹사이트에 들어오는 트래픽을 여러 서버에 분산시켜 하나의 서버에 부담이 가지 않도록 하는 것이 로드밸런싱의 주요 목적이다.
이로써 서버의 성능과 안정성을 높일 수 있다.

<hr />

#### L4 LB vs L7 LB

- L4 Load Balancer
    - IP, Port를 기준으로 스케줄링 알고리즘을 통해 부하 분산
    - 클라이언트에서 로드밸런서(DNS)로 요청을 보냈을 때 최적의 서버로 요청을 전송하고 결과를 클라이언트에게 준다.
- L7 Load Balancer
    - L7 위에서 동작하기 때문에 IP, Port 이외에도 URI, Payload, Http Header, Cookie 등의 내용을 기준으로 부하 분산
    - 콘텐츠 기반 스위칭
    - 요청의 세부적인 사항을 두고 결제만 담당하는 서버, 회원가입만을 담당하는 서버 등으로 분리해서 가볍고 작은 단위로 여러 개의 서비스를 운영하고 요청을 각각의 서버에 분산
    - 데이터를 분석해서 처리가 가능하기 때문에 악의적이거나 비정상적인 콘텐츠를 감지해 보안 지점을 구축할 수 있다는 장점
    - 자원 소모가 크다는 단점

![L4 vs L7](https://blog.kakaocdn.net/dn/ck0dNZ/btrEKUM6v3V/timcEvbT0Cq1iDeL45m650/img.png)

<hr />

### 로드 밸런싱 알고리즘

#### 정적 로드 밸런싱

1. 라운드 로빈 방식(Round Robin Method)
    - 클라이언트의 요청을 여러 대의 서버에 순차적으로 분배하는 방식
    - 클라이언트의 요청을 순서대로 분배하기 때문에 서버들이 동일 스펙을 가지고 있고, 서버와의 연결(세션)이 오래 지속되지 않는 경우 활용하기 적합
    - A, B, C의 서버를 가지고 있을 경우 A → B → C → A 순서대로 순회
2. 가중치 기반 라운드 로빈 방식(Weighted Round Robin Method)
    - 각각의 서버마다 가중치(Weight)를 매기고 가중치가 높은 서버에 클라이언트의 요청을 먼저 배분
    - 여러 서버가 같은 사양이 아니고, 특정 서버의 스펙이 더 좋은 경우 해당 서버의 가중치를 높게 매겨 트래픽 처리량을 늘릴 수 있다.
    - 서버 A의 가중치=8, 서버 B의 가중치=2, 서버 C의 가중치=3 → 서버 A에 8개, 서버 B에 2개, 서버 C에 3개의 Request를 할당
3. IP 해시 방식(IP Hash Method)
    - IP 해시 방식에서 로드밸런서는 클라이언트 IP 주소에 대해 해싱이라고 하는 수학적인 계산을 수행
    - 클라이언트 IP 주소를 숫자로 변환한 다음 개별 서버에 매핑
    - 사용자 IP를 해싱하여 부하를 분산하기 때문에 사용자가 항상 동일한 서버로 연결되는 것을 보장

#### 동적 로드 밸런싱

1. 최소 연결 방법(Least Connection Method)
   - 최소 연결 방법에서 로드 밸런서는 활성 연결이 가장 적은 서버를 확인하고 해당 서버로 트래픽을 전송
   - 이 방법에서는 모든 연결에 모든 서버에 대해 동일한 처리 능력이 필요하다고 가정
2. 최소 응답 시간 방법(Least Response Time Method)
   - 서버의 현재 연결 상태와 응답 시간을 모두 고려하여, 가장 짧은 응답 시간을 보내는 서버로 트래픽을 할당
   - 각 서버의 가용 가능한 리소스와 성능, 처리 중인 데이터양 등이 상이할 경우 적합
   - 조건에 잘 들어맞는 서버가 있을 시 여유 있는 서버보다 먼저 할당
   - 로드 밸런서는 이 알고리즘을 사용하여 모든 사용자에게 더 빠른 서비스를 보장

<hr />

### 헬스 체크 + 장애 조치

#### 헬스 체크

- 서버의 상태를 주기적으로 확인하여 서버의 정상 작동 여부를 판단하는 과정
  - Passive Health Checks
    - 서버에서 수신한 요청에 대해 응답을 제대로 보낼 수 있는지를 검사하는 방식
    - 주로 로드 밸런서와 같은 프록시 서버에서 사용
    - 서버에서 문제가 발생하면 요청에 대한 응답이 지연되거나 실패할 가능성이 있으므로 이를 감지하기 위해 사용됨
  - Active Health Checks
    - 주기적으로 서버에 대한 요청을 보내고 응답을 받아 서버가 정상적으로 작동하는지 여부를 판단하는 방식
    - 대표적으로 ICMP, TCP, HTTP, HTTPS 프로토콜을 이용한 체크 방식을 사용함
    - 서버의 로드 밸런싱, 데이터베이스 연결, API 서비스 등에서 사용됨
  - Agent-based Health Checks
    - 서버에 에이전트(agent)를 설치하고, 에이전트가 서버 내부의 상태 정보를 수집하여 체크하는 방식
    - 서버의 리소스 사용량, 디스크 용량 등 세밀한 정보를 수집할 수 있어 정확한 서버 상태 파악이 가능함
    - 대규모 서버 환경에서 사용됨

#### 서버 장애 조치

- 기본 서버가 오프라인 상태가 될 경우 자동으로 서버를 인수할 수 있도록 백업 서버를 준비하는 것
- 구성에 따라 장애 조치는 약간 다르게 작동. 서버 장애 조치 구성은 활성-활성 또는 활성-대기 중 하나
  - 활성-대기
    - 활성-대기 상태에서는 기본 서버와 하나 이상의 보조 서버가 있다.
    - 두 개의 서버 설정에서 보조 서버는 기본 서버를 모니터링하지만, 그외에는 비활성 상태로 유지된다.
    - 보조 서버가 주 서버의 변경 사항을 감지하면 주 서버를 대신하여 데이터 센터에 주 서버 복원이 필요하다는 사실을 알린다.
    - 기본 서버가 복원되면 다시 한 번 인계받고 보조 서버는 대기 상태가 된다.
    - 기본 서버가 작업을 재개하는 행위를 장애 복구라고 한다.
  - 활성-활성
    - 2개의 서버 활성-활성 구성에서는 두 서버 모두 활성 상태를 유지해야 한다.
    - 활성-활성 구성은 서버가 동일한 방식으로 구성되고 작업 부하를 공유하므로 일반적으로 부하 분산과 관련있다.
    - 활성-활성 구성에서 서버에 장애가 발생하면 트래픽은 운영 중인 서버로 라우팅된다.

<hr />

### DNS 로드밸런싱

- 별도의 소프트웨어나 하드웨어 로드밸런싱 장비를 이용하지 않고 DNS를 이용하여 도메인 정보를 조회하는 시점에서 다른 IP정보를 통해 트래픽을 분산하는 기법

![DNS 로드밸런싱](https://hoing.io/storage/2020/12/dns_lb_3.png)

- 사용 할 수 있는 환경은 Web 이외 도메인을 사용하는 모든 서비스가 가능
- FTP/SFTP, SMTP , 등등...

#### DNS 로드 밸런싱과 기존 로드 밸런싱의 차이점

- DNS 부하 부산
  - 애플리케이션 계층 7의 DNS 수준에서 작동하며 TCP/IP 모델의 전송 계층에서 UDP(사용자 데이터그램 프로토콜)를 사용하여 데이터를 전송
    - UDP는 속도와 가벼운 패킷으로 인해 DNS의 경우 TCP보다 선호
  - DNS 부하 분산을 구현하는 것은 다른 부하 분산 솔루션에 비해 비교적 간단하며 특히 예산이 부족한 중소기업에 유용
    - 복잡한 구성이나 전용 로드 밸런서 하드웨어 또는 소프트웨어가 필요하지 않으므로 IT 리소스가 제한적인 조직에서도 사용하기 쉽다.
  - 사용자를 다른 지리적 지역에 있는 서버로 연결하도록 구성할 수도 있다.
    - 이 기능은 GDPR 준수를 위해 가장 가까운 애플리케이션 인스턴스로 트래픽을 유도하거나 트래픽을 라우팅할 수 있으므로 전 세계에 지사를 둔 조직에 특히 유용
- 기존 부하 부산
  - 전송 계층 4나 애플리케이션 계층 7에서 작동
    - 4계층 로드 밸런서는 네트워크 및 전송 계층 프로토콜(IP, TCP, UDP)에서 발견된 데이터에 따라 작동
    - 7계층 로드 밸런서는 HTTP와 같은 애플리케이션 계층 프로토콜에서 찾은 데이터를 기반으로 요청을 분산
  - 일반적으로 하드웨어나 소프트웨어를 사용하여 클라이언트 요청을 여러 서버 또는 서버 인스턴스에 분산하는 전용 장치
  - 더욱 고급 기능을 제공하고 트래픽 분산을 더욱 세부적으로 제어

<hr />

## 웹 서버 & WAS 관련

### 정적 vs 동적 페이지 차이

#### 정적 웹 페이지(Static Web Page)

- 서버(웹 서버, Web Server)에 미리 저장된 파일(HTML 파일, 이미지, JavaScript 파일 등)이 그대로 전달되는 웹 페이지
- 서버는 사용자가 요청(Request)에 해당하는 저장된 웹 페이지를 보냄
- 사용자는 서버에 저장된 데이터가 변경되지 않는 한 고정된 웹 페이지를 보게 됨

![정적 웹 페이지](https://velog.velcdn.com/images%2Fdbfudgudals%2Fpost%2F6882758b-f4b7-4c19-a007-70a4193ac34b%2Fimage.png)

- 장점
    - 빠르다 : 요청에 대한 파일만 전송하면 되기 때문에 추가적인 작업이 없음
    - 비용이 적다 : 마찬가지로 웹 서버만 구축하면 됨
- 단점
    - 서비스가 한정적이다 : 저장된 정보만 보여줄 수 있음
    - 관리가 힘들다 : 추가/수정/삭제의 작업 모두 수동

#### 동적 웹 페이지(Dynamic Web Page)

- 서버(웹 서버, Web Server)에 있는 데이터들을 스크립트에 의해 가공처리한 후 생성되어 전달되는 웹 페이지
- 서버는 사용자의 요청(Request)을 해석하여 데이터를 가공한 후 생성되는 웹 페이지를 보냄
- 사용자는 상황, 시간, 요청 등에 따라 달라지는 웹 페이지를 보게 됨

![동적 웹 페이지](https://velog.velcdn.com/images%2Fdbfudgudals%2Fpost%2F7c2b381d-eca9-4d0b-a87d-eca6a1a51d0c%2Fimage.png)

- 장점
  - 서비스가 다양하다 : 다양한 정보를 조합하여 동적으로 생성하여 제공 가능
  - 관리가 쉽다 : 웹 사이트 구조에 따라서 추가/수정/삭제 등의 작업이 용이
- 단점
  - 상대적으로 느리다 : 사용자에게 웹 페이지를 전달하기 전에 처리하는 작업이 필요
  - 추가 비용이 든다 : 웹 서버외에 추가적으로 처리를 위한 어플리케이션 서버(Web Application Server)가 필요

<hr />

### 웹 서버와 WAS의 차이점

![웹 서버와 WAS](https://velog.velcdn.com/images/moonblue/post/c3a1c361-1a6c-4c47-962f-0056a598e17c/image.jpeg)

|항목|웹 서버|WAS|
|---|---|---|
|정의|정적인 콘텐츠(HTML, CSS, 이미지 등)를 제공하는 서버|동적인 콘텐츠(웹 애플리케이션)를 처리하고 제공하는 서버|
|기능|HTTP 프로토콜을 이용해 클라이언트에게 웹 페이지 제공|웹 애플리케이션 실행 및 데이터 처리, 웹 서버와 클라이언트 간의 중계 역할|

#### 웹 서버의 역할

- 클라이언트가 웹 브라우저를 통해 요청한 정적 콘텐츠를 제공하는 역할
- 주로 HTTP 프로토콜을 사용하여 작동
- 클라이언트가 URL을 통해 요청한 웹 페이지를 찾아 전송

#### WAS의 역할

- 웹 애플리케이션을 실행하여 동적 콘텐츠를 생성하고, 웹 서버와 클라이언트 간의 데이터 처리를 담당하는 역할
- 클라이언트의 요청에 따라 데이터베이스에서 정보를 가져오거나, 웹 애플리케이션을 실행하여 동적인 웹 페이지를 생성한 후 결과를 웹 서버에 전달
- 웹 서버는 이를 받아 클라이언트에게 전달

#### 웹 서버와 WAS의 활용 사례

- 실제 웹 서비스에서는 웹 서버와 WAS가 함꼐 사용되는 경우가 많다.
- 활용 사례
  - 온라인 쇼핑몰 : 사용자가 상품을 검색하거나 장바구니에 담는 등의 동적인 작업을 WAS 서버에서 처리하고, 상품 이미지나 스타일 정보 등 정적 콘텐츠를 웹 서버에서 제공
  - 온라인 커뮤니티 : 게시판 글 작성, 댓글 달기 등 동적인 기능은 WAS 서버에서 처리하고, 게시글 내용이나 이미지 등 정적 콘텐츠는 웹 서버에서 제공

<hr />

### 리버스 프록시

- 개념
  - 클라이언트와 웹 서버 간의 중개자 역할을 하는 서버
  - 클라이언트로부터의 요청을 대신 받아 웹 서버에 전달하고, 웹 서버의 응답을 클라이언트에게 전달하는 역할을 한다.
- 필요성
  - 서버 부하 분산 (Load balancing)
    - 웹 서비스에 동시에 많은 사용자가 접속할 경우, 서버에 부하가 집중되어 성능 저하 및 서비스 중단이 발생할 수 있다.
    - 리버스 프록시는 들어오는 요청을 여러 대의 서버로 분산시켜 각 서버의 부하를 줄이고, 서버의 가용성을 높여 안정적인 서비스 제공이 가능하도록 한다.
  - 보안 강화
    - 외부에서 직접 서버에 접근하지 못하도록 하여 웹 서비스의 보안을 강화
    - 클라이언트 요청은 먼저 리버스 프록시를 거쳐 서버로 전달되며, 이 과정에서 리버스 프록시는 악성 요청 필터링, 접근 제한 등의 역할을 수행하여 서버를 보호
  - 캐싱 및 가속화
    - 자주 사용되는 정적 파일들(이미지, CSS, JavaScript 등)을 캐시에 저장하여 빠르게 제공
    - 이로 인해 서버의 부하를 줄이고 응답 시간을 단축시켜 웹 서비스의 성능을 향상시킬 수 있다.
- 단점
  - 추가적인 서버 설정과 관리
  - 네트워크 지연
  - 복잡성 증가

<hr />

### 웹서버와 WAS를 분리하는 이유

#### 성능 및 확장성

- 정적인 파일을 처리하는 웹 서버와 동적인 요청을 처리하는 WAS를 각각의 역할에 특화된 서버로 구성함으로써 성능을 향상시킬 수 있다.
  - 일반적으로 웹 서버는 정적인 파일을 처리하는 데 특화되어 있다.
    - ∴ CPU나 메모리 같은 자원을 크게 요구하지 않으며, 처리 속도가 매우 빠르다.
    - (∵ 대부분의 파일이 이미 디스크에 저장되어 있음)
  - WAS는 동적인 요청(비즈니스 로직과 데이터베이스 조회 등의 작업)을 처리하는 데 특화되어 있다.
    - ∴ CPU나 메모리 같은 자원을 많이 요구하며, 처리 속도가 느릴 수 있다.
- WAS를 필요에 따라 수평적으로 확장시키기 쉬움 ⇒ 부하 분산을 효과적으로 처리할 수 있다.
  - (ex. 동적인 요청이 많아지면 WAS를 추가로 구성하여 부하를 분산시킬 수 있음)

#### 안정성

- 웹 서버와 WAS를 분리하지 않고 하나의 서버에 함께 구성하게 되면,
  - WAS에서 발생한 문제가 웹 서버까지 영향을 끼칠 수 있다.
    - (ex. WAS에서 발생한 오류로 인해 WAS가 다운되거나 과부하 상태가 되면,
    - 이에 응답하지 못하는 클라이언트 요청들이 쌓이게 되어, 웹 서버 역시 다운될 수 있다.)
- 웹 서버와 WAS를 분리하면,
  - 각각의 서버가 독립적으로 동작하므로, WAS에서 발생한 문제가 웹 서버에 영향을 주지 않는다.
  - WAS에서 문제가 발생해도 웹 서버는 정적인 파일을 처리하는 역할에만 집중할 수 있어, 전체적인 시스템 안정성이 높아진다.
  - 각각의 서버를 더욱 효율적으로 관리할 수 있다.
    - (ex. 서버 유지보수나 업그레이드 작업을 수행할 때, 웹 서버와 WAS를 분리하면 더욱 안정적으로 작업을 수행할 수 있다.)
 
#### 유지보수 및 관리

- 유지보수 비용 절감
  - 웹 서버와 WAS가 분리되어 있으면 각각의 역할이 분명해지기 때문에 서버 구성 및 구성 변경이 쉬워진다.
  - 웹 서버와 WAS를 분리하면 서버 자원을 더욱 효율적으로 사용할 수 있다.
    - 웹 서버는 정적인 콘텐츠(HTML, CSS, 이미지 등)를 처리하는 데 더욱 특화되어 있고,
    - WAS는 동적인 콘텐츠(JSP, Servlet, PHP, ASP 등)를 처리하는 데 더욱 특화되어 있다.
    - 웹서버에는 정적인 콘텐츠를 처리하는 데에 CPU와 메모리를 많이 사용하지 않기 때문에, CPU와 메모리가 적은 서버를 할당하여 비용을 절감할 수 있다.
    - WAS에서는 동적인 콘텐츠를 처리하는 데에 CPU와 메모리를 많이 사용하기 때문에, CPU와 메모리가 많은 서버를 할당하여 성능을 향상시킬 수 있다.
    - 서버를 분리하여 각각의 서버에 맞는 운영체제, 웹 서버 소프트웨어, WAS 소프트웨어 등을 설치하고 구성할 수 있다.
    - 서버를 분리하면 각각의 서버에 맞는 하드웨어와 자원을 할당할 수 있기 때문에 서버 자원을 더욱 효율적으로 사용할 수 있다.
      - ⇒ 서버 확장을 고려할 때 유리하다.

#### 보안

- 웹 서버와 WAS를 분리하면 각각의 서버에 대해 보안에 대한 전문적인 대응이 가능해진다.
- 웹 서버는 정적인 컨텐츠를 제공하기 때문에, 파일 시스템 접근과 같은 기본적인 보안 기능만 필요하지만,
- WAS는 애플리케이션 코드를 실행하고 데이터베이스와 같은 백엔드 리소스에 접근하기 때문에 보다 고급 보안 기능이 필요하다.
- 웹 서버에는 인증, 인가와 같은 보안 기능을 처리하고, WAS에서는 비즈니스 로직을 처리하는 등의 역할을 분리할 수 있다.
- 웹 서버와 WAS를 분리해서 전문적으로 보안 대응을 하면 보안 위험에 대한 대응이 신속하게 이뤄질 수 있다.
- 웹 서버와 WAS 각각에 대한 보안 설정을 분리하여 관리하면, 보안 설정에 대한 실수나 누락 등이 줄어들어 전반적인 보안 수준을 높일 수 있다.