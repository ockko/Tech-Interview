## 📑 목차 (Table of Contents)

- [🍀 IP(Internet Protocol)](#-ipinternet-protocol)
  - [📌 IPv4와 IPv6의 차이점](#-ipv4와-ipv6의-차이점)
  - [📌 IP주소와 MAC 주소의 차이](#-ip주소와-mac-주소의-차이)
  - [📌 ICMP / ARP / DHCP](#-icmp-arp-dhcp)
  - [📌 TTL(Time To Live)](#-ttltime-to-live)
  - [📌 IPv4 Checksum vs TCP Checksum](#-ipv4-checksum-vs-tcp-checksum)

- [🍀 HTTP & HTTPS](#-http--https)
  - [📌 HTTP의 Stateless 특성](#-http의-stateless-특성)
  - [📌 HTTPS 공개키와 대칭키의 차이](#-https-공개키와-대칭키의-차이)
  - [📌 HTTPS Handshake 과정에서는 인증서를 사용하는 이유](#-https-handshake-과정에서는-인증서를-사용하는-이유)
  - [📌 HTTP 응답 코드](#-http-응답-코드)
  - [📌 GET과 POST의 차이](#-get과-post의-차이)
  - [📌 POST vs PUT vs PATCH](#-post-vs-put-vs-patch)

- [🍀 TLS & SSL](#-tls--ssl)

- [🍀 DNS](#-dns)
  - [📌 DNS 과정](#dns-과정)

- [🍀 쿠키 & 세션](#-쿠키--세션)
  - [🍪 쿠키](#-쿠키)
  - [🗨️ 세션](#-세션)
  - [🗨️ 규모가 커져 서버가 여러 개가 될 경우 세션 관리 방법](#-규모가-커져-서버가-여러-개가-될-경우-세션-관리-방법)

- [🤍 Stateless와 Connectionless 차이점](#-stateless와-connectionless-차이점)

- [🌎 웹 브라우저에서 구글을 검색하면 어떤 경로로 서비스되는가?](#-웹-브라우저에서-구글을-검색하면-어떤-경로로-서비스되는가)

<br>

# IP / HTTP&HTTPS / TLS&SSL / 쿠키&세션

Created: 2025년 5월 12일 오후 3:43
Status: Not started
과목: 네트워크

## 🍀 IP(Internet Protocol)

- 네트워크상에서 기기(호스트)를 식별하기 위한 주소
- 패킷을 목적지까지 라우팅하기 위한 주소 지정 기능 담당
- EX) 편지를 보낼 때 작성하는 집주소

### **📌 IPv4와 IPv6의 차이점**

|  | IPv4 | IPv6 |
| --- | --- | --- |
| 주소 길이 | 32비트(4바이트) | 128비트(16바이트) |
| 표기 방식 | 10진수, 점으로 구분
192.13.2.1 | 16진수, 콜론으로 구분
2001:0db8::0001 |
| 가능한 개수 | 약 42억 개 | 사실상 무한대 |
| 보안 | 기본 없음 | 기본 지원 |

### **📌** IP주소와 MAC 주소의 차이

|  | IP 주소 | MAC 주소 |
| --- | --- | --- |
| 정의 | 네트워크상에서 기기(호스트) 식별이 가능한 주소 | 물리적 하드웨어 주소 |
| 변경 가능성 | 변경 가능(동적 할당) | 고정 |
| 위치 | 네트워크 계층(3 계층) | 데이터링크 계층(2 계층) |

📌 ICMP**(Internet Control Message Protocol)** : IP 프로토콜의 동작을 보조하는

**제어용 메시지 프로토콜**로, **오류 메시지 전송**, **진단**, **통신 상태 확인** 등에 사용됨.

📌 ARP(Address Resolution Protocol) : **IP 주소를 MAC 주소로 변환**해주는 프로토콜

DHCP : 네트워크에 연결된 기기에 **IP 주소, 서브넷 마스크, 게이트웨이, DNS 서버 주소 등**을 **자동으로 할당**해주는 프로토콜

### **📌** TTL(Time To Live)

- IP패킷이 네트워크에 얼마나 오래 전파될 수 있는지를 나타내는 수치
- 라우터를 거칠 때 마다 1씩 감소, 0이 되면 폐기되어 무한 루프 방지

![image](https://github.com/user-attachments/assets/0bd67d0d-f476-4f0b-a03b-3c97f1e55279)


### **📌 IPv4 Checksum vs TCP Checksum**

체크섬 : 데이터가 전송 중에 손상되었는지를 검사하는 방법

|  | IPv4 Checksum | TCP Checksum |
| --- | --- | --- |
| 위치 | IP헤더 | TCP 헤더 + 데이터 전체 |
| 목적 | 헤더 오류 검출 | 전체 오류 검출(신뢰성) |
| 필드 범위 | IP헤더만 검출 | 데이터 포함 전체 검증 |
| 의무 여부 | 필수 | 필수 |

---
<br>


## 🍀 HTTP & HTTPS

- **HTTP**(HyperText Transfer Protocol) : 웹 브라우저와 웹 서버 간에 데이터를 주고받는 프로토콜
- **HTTPS**(HTTP Secure or HTTP over SSL/TLS) : HTTP에 TLS(또는 SSL) 암호화를 더한 버전


### **📌** HTTP의 Stateless 특성

- HTTP는 무상태 프로토콜
    - 클라이언트와 서버가 통신할 때 요청 간의 상태를 저장하지 않음 → 요청과 응답 독립적
    - 장점 : 확장성, 단순성
    - 단점 : 로그인 유지 등 상태 저장 어려움 → 세션 쿠키로 극복

### **📌** HTTPS 공개키와 대칭키의 차이

- 공개키(비대칭키) : 암호화와 복호화 키가 다름, 인증서로 전달, 초기 통신에 사용
- 대칭키 : 암복호화에 같은 키 사용, 속도 빠름, 실제 데이터 통신에 사용
- ⇒ 공개키로 대칭키를 안전하게 전달 → 이후 대칭키로 통신

### **📌** HTTPS Handshake 과정에서는 인증서를 사용하는 이유

**[HTTPS Handshake 작동 방식]**

1. **클라이언트 → 서버로 접속 시도** (https URL 입력)
2. 서버가 **인증서(공개키 포함)** 전송
3. 클라이언트는 인증서가 **신뢰할 수 있는 기관(CA)**에서 발급되었는지 확인
4. 클라이언트가 **대칭키를 생성**하고, 서버의 공개키로 암호화하여 서버에 전송
5. 서버는 **자신의 개인키로 복호화**해 대칭키 획득
6. 이후부터는 **대칭키로 암호화된 통신**

![image](https://github.com/user-attachments/assets/a5dc1206-1ef5-4140-8b35-1d32ef03080d)

<br>

## 🍀 HTTP 응답 코드

| 코드 | 의미 |
| --- | --- |
| 200 | OK (정상 응답) |
| 301 | 영구 이동 (Redirect) |
| 400 | 잘못된 요청 (Bad Request) |
| 401 | 인증 필요 (Unauthorized) |
| 403 | 금지됨 (Forbidden) |
| 404 | 요청한 리소스를 찾을 수 없음 (Not Found) |
| 500 | 서버 내부 오류 (Internal Server Error) |

### 📌 GET과 POST의 차이

| 항목 | GET | POST |
| --- | --- | --- |
| 목적 | 데이터 조회 | 데이터 전송/생성 |
| 데이터 위치 | URL 쿼리 스트링 | HTTP Body |
| 보안 | 낮음 (주소 노출) | 상대적으로 높음 |
| 캐싱 | 가능 | 불가능 |
| 길이 제한 | 있음 | 없음 (서버 설정에 따름) |

### 📌 POST vs PUT vs PATCH

| 메서드 | 의미 | 사용 예시 |
| --- | --- | --- |
| **POST** | 리소스 생성 | 회원 가입 |
| **PUT** | 리소스 전체 수정 | 회원정보 전체 수정 |
| **PATCH** | 리소스 일부 수정 | 닉네임만 수정 |

<br>

## 🍀TLS & SSL

- SSL : 오래된 암호화 프로토콜로 현재는 취약점 존재
- TLS : SSL을 대체한 더 안전한 프로토콜 (현재표준)

<br>

## 🍀DNS

- DNS : Domain Name System
- 사람이 기억하기 쉬운 도메인 주소**(www.google.com)**를 ip주소(예: 142.250.206.4)로 변환해주는 시스템
- 애플리케이션 게층 프로토콜

<DNS 과정>

1. 사용자가 웹 브라우저에 `www.google.com` 입력
2. OS는 먼저 **로컬 DNS 캐시**에 IP 주소가 있는지 확인
3. 없다면 **지정된 DNS 서버(보통 인터넷 제공업체의 DNS)**에 질의

<br>

## 🍀 쿠키 & 세션

### 🍪 쿠키

- 클라이언트(브라우저)에 저장되는 작은 데이터 조각
- 서버가 사용자에게 응답할 때 **Set-Cookie** 헤더를 통해 쿠키를 설정하고, 이후 요청마다 **브라우저가 자동으로 서버에 쿠키를 함께 전송**

### 🗨️ 세션

- 사용자 상태를 유지하기 위해 서버에서 생성하는 저장 공간
- 사용자 로그인 → 서버가 고유한 세션id 발급 → 이를 통해 사용자 식별
- **세션ID는 일반적으로 쿠키에 저장되어 브라우저에 전달**

| 항목 | 쿠키 | 세션 |
| --- | --- | --- |
| 저장 위치 | 클라이언트(브라우저) | 서버 |
| 보안성 | 낮음 (변조 가능성) | 높음 (서버 관리) |
| 용량 제한 | 4KB | 서버 설정에 따름 |
| 유지 시간 | 설정된 시간까지 유지 | 브라우저 종료 or 서버 설정에 따름 |

<br>

### 🗨️ 규모가 커져 서버가 여러 개가 될 경우 세션 관리 방법

❗ 문제 상황 : A서버에서 로그인했는데, 다음 요청은 B서버로 간다면 세션 정보가 없음 → 인증 실패 발생

**해결방법**

| **ticky Session (Session Affinity)** | 같은 사용자의 요청을 **항상 같은 서버**로 라우팅  → 로드 밸런서 설정 필요 |
| --- | --- |
| **세션 클러스터링** | 모든 서버가 서로 세션 정보를 **실시간으로 복제**  → 복잡하고 성능 이슈 |
| **공용 저장소 사용** (권장) | 세션 정보를 DB, Redis 같은 중앙 저장소에 보관
→ 서버 간 공유 가능, 확장성 우수 |

<br>

### 🤍 Stateless와 Connectionless 차이점

**Stateless는 서버의 설계 원칙**, **Connectionless는 통신 방식**

| 항목 | Stateless | Connectionless |
| --- | --- | --- |
| 의미 | 서버가 클라이언트의 상태를 저장하지 않음 | 통신 전에 연결을 설정하지 않고 데이터 전송 |
| 예시 프로토콜 | HTTP | UDP |
| 상태 유지 | ❌ 없음 (요청 간 독립) | ❌ 없음 (요청/응답 간 연결 없이 동작) |
| 적용 목적 | 확장성과 단순성 | 속도 우선, 연결 오버헤드 제거 |
| 장점 | 서버 부담 적음, 확장성 우수 | 빠름, 실시간성 좋음 |
| 단점 | 상태 유지 어렵다 → 별도 장치 필요 (세션/토큰 등) | 데이터 유실, 순서 보장 불가 |

<br>

## 🌎 웹 브라우저에서 구글을 검색하면 어떤 경로로 서비스되는가?

**🔷 응용 계층 (Application Layer)**

- 사용자가 **브라우저 주소창에 "구글" 또는 "국민은행" 입력**
- 브라우저는 기본 검색 엔진(예: Google)에 **검색 쿼리 요청** 준비
    - 예: `https://www.google.com/search?q=국민은행`
- DNS 질의: `www.google.com` → IP 주소 요청
    
    → DNS 서버에서 IP 주소 반환
    
- 검색 요청은 **HTTP/HTTPS 프로토콜**을 통해 전송될 준비 완료

📌 DNS : Domain Name System 도메인 이름을 숫자로 된 ip 주소로 변환해주는 인터넷의 전화번호부

🔷 **전송 계층 (Transport Layer)**

- 브라우저는 구글 서버와 **TCP 3-way 핸드셰이크** 수행
    - SYN → SYN-ACK → ACK
- HTTPS 사용 시 **TLS 핸드셰이크** 병행
    - 서버 인증서 확인, 대칭키 생성
- 이후 **검색 요청 데이터 (HTTP GET 요청)** 전송

🔷  **인터넷 계층 (Internet Layer)**

- 브라우저는 구글 서버의 IP 주소를 기준으로 **IP 패킷 생성**
- 패킷에는 출발지 IP, 목적지 IP 포함
- IP 패킷은 여러 라우터와 게이트웨이를 거쳐 구글 데이터센터로 전달됨
    
    (클라이언트 → ISP → 백본망 → 구글)
    

🔷 **4. 네트워크 액세스 계층 (Network Access Layer)**

> 실제 물리적 전송 (프레임, 전기신호, MAC 주소 기반)
> 
- IP 패킷이 **이더넷 프레임**으로 감싸짐 (캡슐화)
- 각 구간마다 **MAC 주소를 기준으로 다음 장치로 전송**
- 와이파이, LAN 케이블 등 물리 매체 통해 신호 전송
- 최종적으로 구글 서버에 도달

✅ 구글 서버 측 

- 구글 서버는 검색어를 분석 → 적절한 검색 결과 생성
- HTML/JSON 형태의 응답 생성
- TCP를 통해 클라이언트로 전송

✅ 브라우저 렌더링

- 응답받은 HTML을 파싱하여 DOM 생성
- 검색 결과를 사용자에게 시각적으로 출력

![image](https://github.com/user-attachments/assets/ab6dcd48-5f55-4dd2-a36d-6e15105d36fa)
