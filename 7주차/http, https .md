# 6주차 - Http / Https #

### **HTTP (Hyper Text Transfer Protocol)**

- 웹에서 데이터를 주고받기 위한 **기본적인 통신 프로토콜**이다.
- **클라이언트-서버 모델**을 기반으로 작동하며, 보통 클라이언트는 웹 브라우저, 서버는 웹 서버를 의미한다.
- **TCP/IP 위에서 동작**하며, 기본적으로 **80번 포트**를 사용한다.
- **Stateless 프로토콜**
    - 한 번의 요청/응답 이후에는 **연결 정보와 상태 정보를 유지하지 않는다 ( Stateless , Connectioneless) .**
    - 예: 로그인 후 페이지를 이동해도 서버는 사용자가 로그인한 상태인지 기억하지 못한다. (이 문제를 해결하기 위해 쿠키, 세션, 토큰 등이 사용됨)
- **비암호화 통신**
    - 모든 데이터가 평문(plain text) 으로 전송되므로, 네트워크 중간에 있는 제 3자가 데이터를 **쉽게 가로채거나 조작**할 수 있다.
    - 민감한 정보(로그인 정보, 개인정보 등)를 다룰 경우 보안에 매우 취약하다.

---

### **HTTPS (Hyper Text Transfer Protocol Secure)**

- HTTP에 **데이터 암호화**가 추가된 **보안 프로토콜**이다. 443번 포트를 사용한다.
- **SSL(Secure Sockets Layer)** 또는 **TLS(Transport Layer Security)**를 사용해 데이터를 암호화하여, 중간에 누군가가 데이터를 가로채더라도 내용을 알 수 없게 한다.
- **대칭키/비대칭키 암호화** 모두 사용:
    - **비대칭키 암호화** (공개키/개인키)
        - 초기에 서버와 클라이언트가 보안 연결을 설정할 때 사용된다.
        - 서버는 자신의 **공개키**를 클라이언트에게 제공하고, 클라이언트는 이 공개키를 사용해 데이터를 암호화하여 서버로 전송한다.
        - 서버는 이 데이터를 **개인키**로 복호화할 수 있다.
        - 이 과정은 안전하게 **대칭키를 공유**하기 위한 수단으로 사용된다.
    - **대칭키 암호화**
        - 연결이 설정된 후에는 이 대칭키를 사용해 실제 데이터를 암호화하고 복호화한다.
        - 대칭키는 **속도가 빠르고** 연산이 효율적이기 때문에, 본격적인 데이터 송수신에는 대칭키 방식이 사용된다.

![image (21)](https://github.com/user-attachments/assets/4b402156-705a-492f-b998-6b3a40d24e56)


---

### HTTP 동작 메커니즘

1. 브라우저 주소창에 URL 입력
    
    ```
    [https://www.example.co](https://www.example.com/)
    ```
    
2. **DNS 조회**. 어떤 IP주소인지 알아야 하므로
    1. HTTPS일 때, TLS. SSL 핸드셰이크 실행
3. **TCP 연결** :  **3way handshake**를 통하여 서버 ↔ 클라이언트 사이 연결 확보
4. 클라이언트가 서버에게 요청을 보낸다
    1. HTTP Request Message 구조
        1. 요청 라인
            
            ```
            GET /background.png HTTP/1.0
            POST /login HTTP/1.1
            ```
            
        2. Header 정보
            
            ```
            Host: [www.example.com](http://www.example.com/)
            Content-Type: application/json
            User-Agent: Mozilla/5.0
            ```
            
        3. 빈 줄 . ex)  \r\n . 헤더의 끝을 알긴 해야하니까
        4. RequestBody
        
5. 서버가 클라이언트에게 데이터를 응답한다
    1. Response Message 구조
        1. 상태 라인
            
            ```
            HTTP/1.1 200 OK
            HTTP/1.1 404 Not Found
            ```
            
        2. Header 정보
            
            ```
            Content-Type: application/json
            Content-Length: 123
            Date: Thu, 10 Apr 2025 12:00:00 GMT
            Server: Apache/2.4.1
            ```
            
        3. 빈줄
        4. ResponseBody → JSON으로
6. 서버 클라이언트 간에 연결 종료 (4-way-handshaking)
7. 웹 브라우저가 웹 문서 출력
    1. 단 keep-alive상태면 4-way-handshaking 없이 TCP 연결 계속 유지

---

### **쿠키 & 세션 (+토큰)**

- **쿠키**는 **클라이언트(Local)에 저장되는 key-value 형식의 작은 데이터**이다.
    - 서버가 클라이언트에게 응답할 때 쿠키를 함께 보내고, 클라이언트는 이를 저장한 후 이후 같은 도메인에 요청할 때 마다 쿠키와 함께 전송
    - 보통 **세션 ID**나 사용자의 간단한 상태 정보 등을 담는다.
    - 기준 path가 /login 이면 /login/user 라는 path에 request를 할 때도 쿠키를 포함해서 전송할 수 있다.
- **세션**은 **서버에 저장되는 사용자 상태 정보**다.
    - 보통 로그인 등 인증이 필요한 기능에서 사용되며, 클라이언트는 **세션 ID**만 가지고 있고, 실제 정보는 서버가 보관한다.
    - **세션 저장소 (DB or Memory)** 에서 사용자 정보를 조회한다.
    - 보완성은 높지만 서버 자원이 소모된다.
- **토큰**
    - 인증 성공 시 서버가 사용자 정보를 담은 **암호화된 토큰**을 클라이언트에게 발급
    - 클라이언트는 이 토큰을 요청 헤더에 담아 전송. 이 토큰은 **Signature(사용자 인증)** 를 포함하므로 별도 세션 조회가 필요없다.
    - 서버는 토큰을 복호화하여 사용자를 식별 할 수 있다. **Stateless**한 성질을 가진다.
    - 확장성이 세션에 비해서 높다. 보안상 반드시 HTTPS에서 사용

---

### Keep Alive

**Keep Alive란? ( Http Keep Alive)**

- HTTP 연결을 끊지 않고  **일정 시간 동안 계속 유지.**
- 클라이언트가 여러 리소스를 요청할 때, 새 연결을 만들지 않고 **기존 연결 재사용**
    - TCP 연결 생성 비용 (3 way handshake) 발생 단축
    - 속도 효율 증가
- 헤더
    
    ```
    HTTP/1.1 200 OK
    Connection: Keep-Alive // 이 값을 포함시킨다
    Content-Encoding: gzip
    Content-Type: text/html; charset=utf-8
    Date: Thu, 11 Aug 2016 15:23:13 GMT
    Keep-Alive: timeout=5, max=1000 // 5초 유지, 최대 1000개의 요청 처리
    Last-Modified: Mon, 25 Jul 2016 04:32:39 GMT
    Server: Apache
    ```
    
- connection 점유율 때문에 socket이 부족할 수도 있다.

---

### HTTP의 역사

- Http/1.1 (1990년대~)
    - **keep-alive** 도입
    - Host 헤더 도입
- Http/2 (2015)
    - 바이너리 프로토콜 → 기존 텍스트 기반에서 순수 바이너리 코드로 이루어져 파싱 능력이 빨라
    - **멀티플랙싱** : 하나의 TCP연결에서 여러 요청/응답 동시 처리.  (Stream ID)로 구
- Http/3 (2022)
    - TCP기반에서 UDP기반으로 전환
    - **Head-of-Line Blocking (**하나의 요청이 끝나야 다음 요청 가능 ) 제거

---

### RestfulAPI

- **REST** ( Representational State Transfer)
- 웹의 자원을 URI로 표현하고 **HTTP Method ( get, post ,put ,delete)**를 통하여 해당 자원을 조작하는 아키텍쳐 스타일
- RESTfulAPI는 이러한 원칙을 따르는 API

**특징**

| 요소 | 설명 |
| --- | --- |
| **자원(Resource)** | URI로 표현 (예: `/users/1`) |
| **동작(Method)** | HTTP 메서드로 구분 (GET, POST 등) |
| **무상태성(Stateless)** | 서버는 클라이언트 상태를 저장하지 않음 |
| **계층 구조** | 중간 서버(프록시, 게이트웨이 등) 허용 |
| **일관된 URI 설계** | 명확하고 예측 가능한 경로 사용 |

**Http 메서드 예시**

| 메서드 | 의미 | 예시 |
| --- | --- | --- |
| GET | 자원 조회 | `GET /users` (전체 사용자 조회) |
| POST | 자원 생성 | `POST /users` (새 사용자 추가) |
| PUT | 자원 전체 수정 | `PUT /users/1` (ID 1인 사용자 전체 수정) |
| PATCH | 자원 일부 수정 | `PATCH /users/1` (일부 속성만 수정) |
| DELETE | 자원 삭제 | `DELETE /users/1` |
- Get은 데이터 조회할 때 사용되며, URL에 요청 데이터를 포함
- Post는 데이터 생성(추가) 시 사용되며 Body에 요청 데이터 포함
- Put은 데이터 전체 수정 , Patch는 일부 데이터 수정

```
PUT /users/1
Content-Type: application/json

{
"name": "홍길동",
"email": "[gilsun@example.com](mailto:gilsun@example.com)",
"age": 26
}
```

- 여기서 name만 수정해도 객체를 전체 덮어쓰기로 처리

```
PATCH /users/1
Content-Type: application/json

{
"age": 30
}
```

- patch는 딱 age만 수정

**HTTP Request 구성**

```
[요청 라인]
GET /users HTTP/1.1

[헤더]
Host: example.com
Authorization: Bearer token123
Content-Type: application/json

[바디]
{
  "name": "Alice",
  "email": "alice@example.com"
}
```

| 구성 요소 | 설명 |
| --- | --- |
| 요청 라인 | 메서드, 경로, 프로토콜 버전 |
| 헤더(Header) | 요청에 대한 부가 정보 (인증, 타입 등) |
| 바디(Body) | POST, PUT 요청 시 전송할 실제 데이터 |

**HTTP Response 구성**

```
[응답 라인]
HTTP/1.1 200 OK

[헤더]
Content-Type: application/json
Content-Length: 75

[바디]
{
	"id": 1,
	"name": "Alice",
	"email": "[alice@example.com](mailto:alice@example.com)"
}
```

| 구성 요소 | 설명 |
| --- | --- |
| 상태 라인 | HTTP 버전, 상태 코드, 메시지 |
| 헤더(Header) | 응답에 대한 정보 (콘텐츠 타입 등) |
| 바디(Body) | 실제 반환된 데이터 (JSON, HTML 등) |

**response 상태 코드**

| 코드 | 의미 |
| --- | --- |
| 200 | OK (성공) |
| 201 | Created (생성됨) |
| 204 | No Content (성공, 하지만 바디 없음) |
| 400 | Bad Request (요청 오류) |
| 401 | Unauthorized (인증 실패) |
| 404 | Not Found (자원 없음) |
| 500 | Internal Server Error (서버 에러) |
