# 로드 밸런싱, 웹 서버 & WAS 차이

Created: 2025년 5월 27일 오후 4:28
Status: Not started
과목: 네트워크

목차

## 로드 밸런싱

### 로드 밸런서란?

> 여러 대의 서버에 요청을 나눠주는 분산 트래픽 장치
> 
> 
> 하나의 서버에 트래픽이 몰려 과부하 되지 않도록 하는 역할
> 

### 🎯 왜 필요할까?

- 서버 1대는 많은 사용자를 감당하기 어려움
- 서버가 여러 대일 때, **각 서버로 요청을 고르게 분배**해줘야 함
- 시스템의 **성능 향상**, **장애 대응(장애 조치)**, **확장성 확보**

### L4 vs L7 로드 밸런서
![image](https://github.com/user-attachments/assets/9ab1b62b-9a69-4a54-a7e4-10845b89c966)


### 로드 밸런싱 알고리즘
![image](https://github.com/user-attachments/assets/4e41843c-3218-478f-b914-1cd8d2be6fa1)



### 🔍 헬스 체크(Health Check)

- 서버가 **정상적으로 동작하는지** 주기적으로 확인하는 기능
- 실패 응답 시, 해당 서버로 요청 분배하지 않음

### 🔄 장애 조치(Failover)

- 헬스 체크를 통해 **문제 있는 서버를 자동으로 제외**하고
- 나머지 정상 서버로 트래픽을 자동 재분배함

> ✅ 사용자가 몰라도 시스템이 자동으로 복구됨 → 고가용성(HA) 유지
> 

### 💡 DNS 기반 로드 밸런싱

> DNS가 서로 다른 IP 주소를 번갈아 응답하여 트래픽 분산
> 

**동작 방식:**

- `www.example.com` 요청 시, DNS가 IP1, IP2, IP3 등을 번갈아 응답
- 클라이언트는 받은 IP에 요청

**한계:**

- DNS는 **캐시**가 존재해, 빠르게 장애 조치가 어렵다
- 로드밸런서(LB)는 서버 상태 실시간 반영하지만, DNS는 반응이 느림

### 정적 페이지 vs 동적 페이지
![image](https://github.com/user-attachments/assets/2ee61848-eebb-4a49-b2b7-f292f23ad2b9)


### 웹서버

- 정적 파일 제공
- 주요 기능 : HTML, 이미지, JS, CSS 전달

### WAS

- 동적 로직 처리
- 주요 기능 : DB 조회, 비즈니스 로직 처리

### 웹서버와 WAS 협업 구조

[Client] → [웹서버 (Nginx)] → [WAS (Tomcat)] → [DB]

**동작 흐름 예시:**

1. 클라이언트가 `index.html` 요청
    
    → 웹서버가 직접 정적 파일 응답
    
2. 클라이언트가 `login.do` 요청
    
    → 웹서버가 요청을 WAS에 전달 (프록시 역할)
    
3. WAS가 Java 코드 실행 + DB조회 후 결과 반환
    
    → 웹서버가 사용자에게 최종 응답
    

✅ **웹 서버는 프론트 게이트웨이**

✅ **WAS는 실제 비즈니스 로직 처리자**

### 웹서버와 WAS를 분리해서 사용하는 이유
![image](https://github.com/user-attachments/assets/9c5b4ec4-173d-433e-abd5-eb49acc03bc3)
