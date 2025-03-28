
# **📌 PCB (Process Control Block)**

## **PCB란?**

- **Process Control Block** (프로세스 제어 블록)
- 운영체제가 **프로세스를 관리**하기 위해 프로세스의 상태 정보를 저장하는 **구조체**
- **프로세스가 생성될 때마다 고유한 PCB가 생성**되며, **프로세스 작업이 끝나면 PCB는 제거**됨

---

## **📌 PCB 구조**

![image](https://github.com/user-attachments/assets/430c0c30-e385-4bd5-ad1e-13f6a10f0828)


### **🔹 각 항목 설명**

1. **Process ID**
    - 프로세스를 고유하게 식별하는 ID (PID)
2. **Process State**
    - 프로세스의 현재 상태
        - `New` (생성됨)
        - `Ready` (준비 상태)
        - `Running` (실행 중)
        - `Waiting` (입출력 대기 등)
        - `Terminated` (종료됨)
3. **Program Counter**
    - 프로세스가 **다음에 실행할 명령어의 주소**
4. **Registers**
    - CPU 레지스터 값 (예: Accumulator, General Registers, Stack Pointer 등)
5. **CPU Scheduling Information**
    - 프로세스의 **우선순위(priority)**, **CPU 점유 시간**, **스케줄링 큐 정보**
6. **Memory Management Information**
    - 해당 프로세스의 **주소 공간** (페이지 테이블, 세그먼트 테이블 포함)
7. **I/O Status**
    - 프로세스가 사용하는 **입출력 장치 목록**, **열린 파일 목록**

---

## **📌 PCB가 필요한 이유**

### **1️⃣ 프로세스의 상태 관리**

- CPU에서 실행 중인 프로세스가 **대기 상태**가 되거나, **다른 프로세스로 전환**될 때 현재 프로세스의 정보를 저장해야 함
- PCB가 없으면 **프로세스의 실행 상태를 추적할 방법이 없음**

### **2️⃣ 문맥 전환 (Context Switch)**

- 운영체제는 여러 프로세스를 동시에 실행해야 하므로, **CPU가 다른 프로세스로 전환될 때 현재 프로세스의 상태를 PCB에 저장**함
- PCB가 없다면 **이전 프로세스의 상태를 기억하지 못해 실행을 재개할 수 없음**

### **3️⃣ 리소스 관리**

- 각 프로세스는 CPU, 메모리, 입출력 장치, 열린 파일 등의 **리소스를 사용함**
- **PCB는 프로세스가 사용하는 리소스 목록을 저장**하며, 이를 통해 OS가 효율적으로 리소스를 할당 및 회수 가능

### **4️⃣ 프로세스 식별 및 계층 구조 유지**

- 운영체제는 **각 프로세스를 고유한 ID(PID)로 구분**해야 함
- PCB 내에 부모-자식 관계 정보를 저장하여 **프로세스 계층 구조 유지** 가능
(예: 부모 프로세스가 종료되면 자식 프로세스도 종료될 수 있도록 관리)

# **📌 Context Switching**

## **✅ Context Switching이란?**

- 현재 실행 중인 **프로세스의 상태를 PCB에 저장**하고,
다음 실행할 **프로세스의 정보를 PCB에서 읽어서 실행하는 과정**
- 운영체제가 **멀티태스킹을 지원하기 위해 필수적인 개념**

---

## **📌 Context Switching의 필요성**

1. **CPU는 한 번에 하나의 프로세스만 실행 가능**
    - 다중 프로세스를 동시에 실행하는 것처럼 보이게 하기 위해,
    여러 프로세스를 **빠르게 번갈아가며 실행**함
2. **인터럽트 또는 시스템 호출(System Call) 처리**
    - 실행 중인 프로세스가 I/O 요청을 하거나, 특정 이벤트가 발생하면
    다른 프로세스로 전환이 필요함
3. **효율적인 CPU 사용**
    - 한 프로세스가 I/O 작업을 기다리는 동안 **CPU를 낭비하지 않고**
    다른 프로세스를 실행할 수 있도록 함

---

## **📌 Context Switching 과정**

![image](https://github.com/user-attachments/assets/60ed8c8b-db11-4fbf-98ca-269420774e40)



### **🔹 과정 설명**

1. **Process P1이 실행되는 도중**
    - 인터럽트(Interrupt) 또는 시스템 콜(System Call) 발생
2. **P1의 현재 상태(레지스터, 프로그램 카운터 등)를 PCB1에 저장**
3. **PCB2에서 P2의 상태를 불러와 Process P2 실행**
4. **P2가 실행되던 중 인터럽트 발생**
5. **PCB2에 P2의 상태 저장, PCB1에서 P1의 상태 불러오기**
6. **Process P1을 다시 실행**

### **⚠️  Context Switching Overhead**

- Context Switching 과정에서 **CPU가 실제 작업을 수행하지 않는 시간**이 발생
- 이 시간을 **"오버헤드(Overhead)"** 라고 하며, 너무 자주 발생하면 시스템 성능이 저하될 수 있음
- 프로세스 수가 많을 때 Context Swtiching을 자주 수행하면, **CPU가 프로세스를 실행하는 것 보다 프로세스 간 전환에 더 많은 시간**을 소비할 수도 있다.

---

## **📌 Context Switching 발생 상황**

### **1️⃣ 타이머 인터럽트 (Time Slice Expired)**

- **CPU 스케줄러**가 특정 프로세스에게 할당된 **시간(Time Slice)이 종료되었을 때**
- 다음 프로세스가 CPU를 사용하도록 전환됨

### **2️⃣ 프로세스 종료 (Process Termination)**

- 현재 실행 중인 프로세스가 **완전히 종료되면**, 다른 프로세스가 CPU를 사용해야 함
- 종료된 프로세스의 PCB는 제거됨

### **3️⃣ 입출력 요청 (I/O Interrupt)**

- 실행 중인 프로세스가 **I/O 작업(예: 디스크, 네트워크 요청 등)을 수행하기 위해 대기 상태(WAIT)로 변경**되었을 때
- **CPU가  유휴 상태가 되지 않도록** 다른 프로세스를 실행함
- 예: 파일을 읽거나 네트워크 데이터를 받을 때 Context Switching 발생
