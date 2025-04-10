## `📌 메모리 관리 기법 (페이징, 세그멘테이션)`

> 운영체제에서 여러 프로세스가 메모리를 효율적으로 사용할 수 있도록 도와주는 기법들을 **메모리 관리 기법**이라고 한다. 주요 기법으로는 페이징(Paging)과 세그멘테이션(Segmentation)이 있다.



📂 **[개발자가 알아야하는 이유]**

- **효율적인 코드 작성을 위해**
    - 내가 만든 데이터가 **Stack에 있는지, Heap에 있는지** 알면 **메모리 오버플로우나 낭비를 피할 수 있다.**
    - ex) 너무 큰 배열을 Stack에 올리면 → StackOverflow 에러 발생
- **메모리 관리를 위해**
    - “**한정된 메모리를 어떻게 쪼개어 쓰지?”**
- **CPU에 원활하게 실행되기 위한 프로그램 용량을 알아야 함**

### 1.  프로세스의 주소 공간

<img width="716" alt="스크린샷 2025-04-01 오후 4 04 19" src="https://github.com/user-attachments/assets/9431fd1c-4fac-4278-9104-51bcbf9f20c0" />

- 운영체제에서 하나의 프로그램이 실행되면 프로세스가 된다.
- 프로세스가 메모리를 할당 받으면, 자신만의 방법으로 메모리를 관리하기 위해 이 공간들을 어떤 구조로 관리하는데, 우리는 이를 **프로세스 주소 공간**이라고 부른다.
- 이 프로세스는 자신만의 가상 주소 공간(Virtual Address Space)을 갖게 된다
- 한정된 메모리를 절약하려고 프로세스는 다양한 메모리 절약 시도를 한다.

| **영역 이름** | **설명** |
| --- | --- |
| Text 영역 | 코드 (실행 명령어) |
| Data 영역 | 전역 변수, static 변수 |
| Heap 영역 | 동적 할당 (malloc, new), 런타임 크기가 결정 |
| Stack 영역 | 함수 호출 시 생기는 지역 변수, 매개변수, return 주소 등 |

### 2. Virtual Address(가상 주소)와 Physical Address(물리 주소)

<img width="703" alt="스크린샷 2025-04-01 오후 4 04 25" src="https://github.com/user-attachments/assets/a648f7c4-9524-499a-8108-6f6ae2c179c4" />

- `Virtual Address(=Logical address)`
    - 프로세스마다 독립적으로 가지는 주소 공간
    - 프로그램의 시작점으로부터 떨어진 거리
    - **운영체제 영역 제외**
    - 각 프로세스마다 0번지부터 시작
    - CPU가 보는 주소는 logical address임
- `Physical address` : 메모리에 실제 올라가는 위치
    - **운영체제 영역 포함**
- `주소 바인딩` : 주소를 결정하는 것
    - 과정 : Symbolic Address → Logical Address → Physical address
    - `Compile time binding`
        - 물리적 메모리 주소(physical address)가 컴파일 시 알려짐
        - 시작 위치 변경시 재컴파일
        - 컴파일러는 절대 코드(absoulte code) 생성
    - `Load time binding`
        - Loader의 책임하에 물리적 메모리 주소 부여
        - 컴파일러가 재배치가능 코드(relocatable code)를 생성한 경우 가능
    - `Execution time binding` (=  Run time binding)
        - 수행이 시작된 이후에도 프로세스의 메모리 상 위치를 옮길 수 있음
        - CPU가 주소를 참조할 때마다 binding을 점검(address mapping table)
        - 하드웨어적인 지원이 필요(eg. base and limit register, MMU)
            - `MMU(Memory-Management Unit, 메모리 관리 장치)`
                - `Logical address`→ `physical address`로 매핑해주는 Hardware device
            - MMU scheme
                - 사용 프로세스가 CPU에서 수행되며 생성해내는 모든 주소값에 대해  `Logical address` 와 `base register`(=relocation register, 프로그램의 기준 주소)의 값을 더한다
                    - `base register` : 프로그램의 가장 작은 물리 주소(프로그램의 첫 물리 주소)를 저장
            - user program
                - logical address만을 다룬다
                - 실제 physical address를 볼 수 없으며 알 필요가 없다


---

### 3.  페이징(Paging)

> 가상 주소 공간과 물리 메모리를 **동일한 크기**의 블록으로 나누어 매핑하는 방식
>
<img width="707" alt="스크린샷 2025-04-01 오후 4 04 44" src="https://github.com/user-attachments/assets/55c85c64-7313-4b74-9d07-a314cc6a861f" />


**📌 용어 정리**

- `가상 주소 공간` : 프로세스가 사용하는 논리적 메모리 주소 영역
- `물리 주소 공간` : 실제 RAM에 존재하는 메모리 주소
- `프레임(Frame)` : 물리 메모리도 같은 크기 블록으로 나눔
- `페이지(Page)` : 가상 주소 공간을 고정 크기 블록으로 나누는 것
    - Window 기준 일반적으로, 4KB)
- `단편화` : 메모리를 쓰지 못하고 낭비되는 공간이 생기는 현상
- `External fragmentation(외부 단편화)` : 메모리에 빈 공간은 많은데, **원하는 크기만큼 연속된 공간**이 없어서 메모리를 못 쓰는 상황
    - 메모리 : 10KB, 15KB, 5KB 빈 공간이 흩어져 있는 상황에서 **30KB 연속된 공간**을 원할 경우
    - 전체로 보면 **30KB 이상 남았지만**, 연속된 공간이 없으니 할당하지 못한다.
- `Internal fragmentation(내부 단편화)` : 할당 받은 메모리 내부에서 낭비되는 공간이 생기는 현상

| **요청 크기** | **할당된 크기** | **낭비된 공간** |
| --- | --- | --- |
| 18KB | 20KB | **2KB (내부 단편화)** |
- **설명**
    - Process의 Virtual memory를 동일한 사이즈의 page 단위로 나눔
    - Virtual memor의 내용이 page 단위로 noncontiguous하게 저장됨
    - 일부는 backing storage에, 일부는 physical memory에 저장
- **동작 방식**
    - Physical memory를 동일한 크기의 frame으로 나눔
    - logical memory를 동일한 크기의 page로 나눔(frame과 같은 크기)
    - 모든 가용 frame들을 관리
    - page table을 사용하여 logical address를 physical address로 변환
    - 페이지와 프레임을 **1:1로 매핑**해서 사용하는 방식
- **장점** : `External fragmentation(외부 단편화)` 발생 X
    - 프로세스 간 주소 공간 격리
- **단점** : `Internal fragmentation(내부 단편화)` 발생 가능 (마지막 페이지에 남는 빈 공간)
    - 페이지 테이블 접근 비용(TLB로 해결)
- 주소 변환 과정
    - 가상 주소 = [ page number | page offset ]
        - page number을 이용해 Page Table에서 Frame number을 찾아 물리 주소로 변환

<img width="360" alt="스크린샷 2025-04-01 오후 4 04 49" src="https://github.com/user-attachments/assets/20b12986-be51-4555-bd61-20464285e836" />

---

### 4. 세그멘테이션(Segmentation)
<img width="489" alt="스크린샷 2025-04-01 오후 4 04 54" src="https://github.com/user-attachments/assets/15102baa-29d5-429e-961f-1c313503ddd3" />

> **논리 적인 의미 단위**(코드, 데이터, 슽개 등)로 메모리를 나누는 방식
>
- 가상 주소 공간을 의미 단위(코드, 데이터, 스택 등)으로 나눔
- 각 세그먼트는 크기와 시작 위치가 다름
- `address` = `[segment number + offset]`
- `Segment Table`을 이용해 물리 메모리 주소로 변환
- **장점** : 논리적인 구조 반영 가능(코드, 데이터 분리 등), 사용자 입장에서 직관적
- **단점** : `External fragmentation(외부 단편화)` 발생 가능, Segment Table 크기와 관리 복잡성

---

### 5.  Paging VS Segmentation

| 항목 | 페이징 (Paging) | 세그멘테이션 (Segmentation) |
| --- | --- | --- |
| 분할 단위 | 고정 크기 블록 | 가변 크기 논리 단위 |
| 단편화 | 내부 단편화 | 외부 단편화 |
| 주소 구조 | 페이지 번호 + 오프셋 | 세그먼트 번호 + 오프셋 |
| 장점 | 외부 단편화 없음 | 논리적 구조 반영 용이 |
| 단점 | 내부 단편화 발생 | 외부 단편화 발생 |

---

## `📌 가상 메모리 개념 (Demand Paging, Page Fault)`

> 우리가 사용하는 컴퓨터에는 물리적인 메모리(RAM) 용량이 제한 되어있고, 하지만 실제로 실행되는 프로그램은 큰 메모리를 요구하는 경우가 많다. 이를 해결하기 위해 운영체제는 가상 메모리(Virtual Memory) 개념을 도입하였다.
>

📂 **[개발자가 알아야하는 이유]**

- 성능 최적화에 도움되기 때문
    - 프로그램이 너무 많은 데이터를 한 번에 다루면, 메모리에 없는 데이터를 불러오느라 **Page Fault**가 잦아짐 → 속도 저하
- 메모리 에러 디버깅에 도움 되기 때문
    - Segmentation Fault, OutOfMemoryError, Page Fault
- 멀티태스킹

### 1. Virtual Memory(가상 메모리)

> 운영체제가 실제 메모리보다 더 큰 메모리를 사용하는 것처럼 작동하게 하는 메모리 관리 기법
>
- 물리 메모리 크기 한계 극복하기 위해 생겨남
- 가상 메모리는 Logical Memory와 Physical Memory를 분리하는 기술
- 전체 프로그램을 메모리에 올릴 필요 없이, 실행에 필요한 일부 페이지만 메모리에 올려 실행할 수 있도록 도와준다
- Logical Address Space(논리 주소 공간)은 물리 주소보다 훨씬 클 수 있다
- 하나의 논리 주소 공간을 여러 프로세스가 공유 가능
- 전체 프로그램이 아니라 일부만 메모리에 적재 → **메모리 절약 + 다중 실행 가능**

- **장점**
    - 더 많은 프로그램을 동시에 실행 가능 (멀티태스킹 향상)
    - I/O 횟수 줄어듦 → **성능 개선**
    - 프로세스 생성 속도 향상 (fork 시 페이지 공유 가능)
    - 프로세스 간 라이브러리나 페이지 공유 가능 (공유 메모리 구현 등)

### **✅** 가상 주소 공간 구조

- 일반적으로 **주소 0부터 시작하여 연속된 논리 주소**를 가짐
- 실제 물리 메모리는 페이지 프레임 단위로 구성 → `MMU(Memory Management Unit)`가 `Logical Address` → `Physical Address`로 변환
- Stack은 **가상 주소 공간의 최댓값에서 아래로**, Heap은 **아래에서 위로** → **공간 활용 극대화**
- 중간의 사용되지 않은 공간은 **Hole**(빈 공간)이라 부름
    - → 동적 라이브러리, Heap/Stack 성장 공간 확보 가능

---

### 2. Demand Paging(요구 페이지)

> 실제로 필요한 페이지가 참조될때에만 메모리에 적재하는 방식
>
<img width="422" alt="스크린샷 2025-04-01 오후 4 05 09" src="https://github.com/user-attachments/assets/cc2967dd-cfcd-41fc-8d37-0da30921beef" />


- 가상 메모리를 어떻게 효율적으로 사용할지에 대한 방법 중 하나
- 프로그램 실행 시, 모든 페이지를 한 번에 메모리에 올려 낭비 하지 않고, 필요한 페이지만 그때그때 꺼내오는 전략
- 처음부터 메모리 전체를 load하지 않고, 페이지 단위로 필요할 때(Demand) 메모리에 적재
- 페이지가 실제로 load time 이후 접근될 때 메모리에 load
    - 이를 `Lazy Loading` or `Lazy Swapping` 이라고 한다
- 가상 메모리와 페이지 개념을 활용해 관리

- **동작 방식**
    1. CPU가 어떤 페이지를 참조하려고 시도
    2. 해당 페이지에 메모리 없으면 `Page Fault` 발생
    3. 운영체제가 디스크에서 해당 페이지를 `load`
    4. `Page table`을 갱신하지 않고 명령어 `재시작`

- **장점** : 메모리 효율성, 불필요한 I/O 감소, 다중 프로세스 지원에 효과적

---

### 3. Page Fault(페이지 부재)

> CPU가 참조하려는 페이지가 메모리에 없을 때 발생하는 예외 상황
>
<img width="460" alt="스크린샷 2025-04-01 오후 4 05 14" src="https://github.com/user-attachments/assets/0a53f30f-68f2-40f9-9163-e950901bea4e" />


- 페이지 폴트(Page Fault)는 프로세스가 접근하려는 메모리 페이지가 **물리적 메모리(RAM)에 존재하지 않을 때** 발생하는 이벤트
- 이는 가상 메모리를 사용하는 시스템에서 **정상적으로 발생할 수 있는 인터럽트**이다.
- CPU가 접근하려는 페이지가 메모리에 없을 때 발생
- 디스크에 해당 페이지를 가져와 메모리에 올림
- **동작 과정**
    1. **CPU가 가상 주소를 참조**하려고 시도
    2. **페이지 테이블을 확인**했을 때, 해당 페이지가 메모리에 없으면,  **`Page Fault` 발생** (OS로 trap 발생)
    3. 운영체제는 `Page Fault` 인터럽트를 감지 → 현재 CPU 상태를 저장
    4. 운영체제는 **해당 페이지가 유효한지 검증**
        - Invalid Reference(잘못된 주소)일 경우 → 프로세스 강제 종료 (Abort)
        - 올바른 주소지만 아직 메모리에 없다면, 다음 단계 진행
    5. 운영체제는 `Free Frame`(빈프레임) 을 찾음
    6. 디스크에서 해당 페이지를 `Free Frame`에 load (스케줄링된 디스크 I/O 수행)
    7. **`Page Table`과 관련 Table을 업데이트**
        - 페이지가 메모리에 있음을 표시 (`Validation Bit = v`)
    8. **CPU가 중단된 명령어를 다시 실행**하며 프로세스를 재개

💬 Q. **Page Fault는 무조건 오류인가요?**

A. 아니요, **가상 메모리 기반 시스템에서는 자연스럽게 발생하는 현상**입니다.단, 너무 자주 발생하면 시스템 성능 저하 (→ Thrashing 유발 가능)

---

### 4. Thrashing

> Page Fault가 너무 자주 발생해서, 시스템이 실제 작업을 거의 못하게 되는 현상
>
<img width="455" alt="스크린샷 2025-04-01 오후 4 05 20" src="https://github.com/user-attachments/assets/b7799233-a8de-4a08-8170-7799448269de" />

- 페이지 교체가 너무 빈번하여 디스크와 메모리 간 이동만 반복됨
- 프로세스의 **locality 크기 > 메모리 크기**일 때 발생
    - 즉, 필요한 데이터를 모두 담을 수 없어서 계속 Page Fault 발생
- CPU는 계속 대기 상태 → **시스템 성능 급격히 저하**
- 프로세스가 필요한 페이지를 메모리에 충분히 올리지 못하면 Page Fault 비율이 매우 높아짐
- 운영체제는 CPU 사용률이 낮다고 판단하여 멀티프로그래밍 정도를 증가시킬 수 있음
    - 더 많은 프로세스를 메모리에 올리면 → 상황 악화
- 결과적으로 프로세스들은 계속해서 페이지를 swap in/out만 하게 되고, 실제로 일은 거의 하지 못하는 상태가 된다

- **해결 방법**
    - **`Local Replacement`**: 해당 프로세스의 메모리 프레임 내에서만 교체 수행
    - **`Priority Replacement`**: 우선순위 낮은 프로세스 페이지부터 교체
    - **`Working Set` 알고리즘** 활용하여 각 프로세스의 locality 추적

---

### 5. Working Set

> 프로세스가 일정 시간 동안 자주 참조하는 페이지들의 집합
>
- 최근에 자주 사용하는 페이지 묶음
- Working Set 개념을 통해 메모리에 올려야 할 페이지들을 예측 가능
- 프로그램은 한동안 비슷한 코드, 데이터만 계속 쓰기 때문에 메모리에 미리 올려두면 **Page Fault를 줄이고 Thrashing을 방지**할 수 있다 → 성능 개선

---

## `📌 캐시 메모리, TLB(Translation Lookaside Buffer)`

📂 **[개발자가 알아야하는 이유]**

- 성능 최적화와 직접 연결 되기 때문
    - 프로그램이 느려지는 이유 중 하나인 메모리 접근 시간을 줄이기 위해 `Cache hit rate`가 중요
    - `Locality`(지역성) 개념을 이해하고 코드를 작성하면, `Cache hit rate`를 높여 성능이 좋아짐
- CPU 구조, 병목 구간 파악 가능하기 때문
    - 시스템이 느릴 때 Cache Miss때문인지 고민 할 수 있어야 함
- Java, Pytho 등 가상 메모리 기반 프로그램 돌아감 → 주소 변환이 느리면 성능 저하 → TLB 모르면 원인 못찾음

| **이름** | 설명 |
| --- | --- |
| 캐시(Cache) | 자주 쓰는 걸 미리 꺼내놓는 저장소 |
| 캐시 메모리 | CPU가 데이터를 더 빨리 쓰려고 사용하는 임시 메모리 |
| TLB | 자주 쓰는 주소 변환 정보를 저장하는 캐시 (주소 계산용) |

### 1. 캐시 메모리

<img width="699" alt="스크린샷 2025-04-01 오후 4 05 31" src="https://github.com/user-attachments/assets/c08af8d2-763a-40e7-8060-557b4ee9ad99" />

> 자주 쓰는 데이터를 Cache에 둬서 CPU가 빠르게 접근 가능할 수 있게 도와주는 고속 메모리
>
- CPU와 메모리 사이 속도 차이를 줄이기 위해 사용하는 고속 저장소
- CPU와 RAM(Main Memmory) 사이 속도 차이를 줄이기 위한 고속 SRAM 기반 저장소
- CPU의 연산 속도와 메모리 접근 속도의 차이를 조금이나마 줄이기 위해 탄생
- “CPU가 매번 메모리에 왔다 갔다 하는건 시간이 오래 걸리니, 메모리에서 CPU가 사용할 일부 데이터를 미리 캐시 메모리로 가지고 와서 쓰자”
- 계층적 구조: L1 (가장 빠름), L2, L3 (속도↓, 용량↑)
- 지역성(Locality) 원칙 기반 (시간/공간 지역성)
- 캐시 미스 종류: `Cold Miss`, `Capacity Miss`, `Conflict Miss`

💬 **Q. 왜 캐시메모리는 L1, L2, L3처럼 계층이 나뉘어 있나요?**

A. L1, L2, L3 캐시는 각각 속도와 크기, 비용이 다르기 때문에, 가장 빠른 L1에 자주 쓰는 데이터를 두고, L2, L3는 점점 큰 저장소로 덜 자주 쓰는 데이터를 저장합니다. CPU가 RAM까지 가지 않고도 빠르게 데이터를 처리할 수 있도록 돕습니다

| **계층** | **속도** | **크기** | **위치** | **특징** |
| --- | --- | --- | --- | --- |
| L1 캐시 | 가장 빠름  | 작음 (수십 KB) | CPU 코어 내부 | 접근 속도 1~2 클럭 / 전용 |
| L2 캐시 | 중간 속도 | 중간 (수백 KB~MB) | CPU 내부 or 공유 | 여러 코어 간 공유 가능 |
| L3 캐시 | 느리지만 RAM보단 빠름 | 큼 (수 MB~수십 MB) | CPU 전체 공유 | 히트율 향상 목적 |

### 2. TLB(Translation Lookaside Buffer)

> 페이지 테이블을 빠르게 검색하기 위한 캐시 역할을 하는 특수한 하드웨어
>
<img width="730" alt="스크린샷 2025-04-01 오후 4 05 42" src="https://github.com/user-attachments/assets/081ca4d6-911d-4d3d-a47e-0fef7118a93d" />


- 자주 쓰는 주소 매핑 정보를 저장하는 캐시
- `Virtual Address` → `Physical Address` 변환 시 **페이지 테이블** 참조가 느림 →  **최근 사용된 주소 변환 정보를 `TLB`에 저장**
- `TLB Miss` 발생 시 → 페이지 테이블에서 찾고 TLB 업데이트
- 주소 변환 성능 향상 + 페이징 시스템 병목 제거

| **항목** | **캐시 메모리** | **TLB** |
| --- | --- | --- |
| 위치 | CPU ↔ RAM 사이 | CPU ↔ 페이지 테이블 사이 |
| 역할 | 자주 쓰는 데이터 캐시 | 자주 쓰는 주소 변환 캐시 |
| 장점 | 메모리 접근 속도 향상 | 주소 변환 속도 향상 |

---

## `📌 메모리 할당 전략 (First Fit, Best Fit, Worst Fit)`

📂 **[개발자가 알아야하는 이유]**

- **효율적인 시스템 설계와 디버깅, 성능 튜닝에 도움 되기 위함**

<img width="407" alt="스크린샷 2025-04-01 오후 4 05 48" src="https://github.com/user-attachments/assets/21b03e48-564a-47d4-aa10-979099d688f4" />


### 1. First Fit(최초 적합)

> 가장 먼저 발견한 충분한 공간에 할당
>
- 메모리 앞에서부터 탐색하며 처음 만나는 충분한 공간에 바로 할당
- 탐색 속도가 빠르고 구현이 간단함
- 할당된 공간 이후에 작은 공간들이 남아 `외부 단편화` 발생 가능

### 2. Best Fit(최적 적합)

> 가장 크기가 딱 맞는 공간(남는 공간이 가장 적은 곳)에 할당
>
- 공간 낭비가 적지만, 탐색 시간이 길 수 있음
- 메모리 낭비를 줄이려는 전략
- 남는 공간이 많이 생기기 때문에 `오히려 외부 단편화`가 많이 발생할 수도 있음

### 3. Worst Fit(최악 적합)

> 남은 공간 중에 가장 큰 공간에 할당
>
- 가장 큰 공간에 할당하여 남은 공간이 충분히 크도록 함
- 큰 공간들이 자꾸 잘게 쪼개져서 **큰 프로세스를 수용하지 못하는 상황** 발생 가능

### 4. Next Fit

> 마지막으로 참조한 메모리 공간에서부터 탐색을 시작해 공간을 찾음
>
- 마지막으로 찾았던 자리 이후부터 다시 찾기 시작하는 메모리 할당 전략
- 탐색 지점을 옮김으로써, 앞 구간 탐색을 줄여 탐색 시간을 줄이고자 함
- 뒤에서 앞으로 돌아가지 않기 때문에 더 빠르게 메모리 할당할 수도 있음

💬 Q. **메모리의 fit의 종류 4가지에 대해 설명해주세요.**

- First fit : 메모리의 처음부터 검사해서 크기가 충분한 첫번째 메모리에 할당
- Best fit : 모든 메모리 공간을 검사해서 내부 단편화를 최소화하는 공간에 할당
- Worst fit : 남은 공간 중에 가장 큰 공간에 할당
- Next fit : 마지막으로 참조한 메모리 공간에서부터 탐색을 시작해 공간을 찾음
