
# 웹소켓 vs HTTP 통신


### **1. HTTP와 WebSocket의 개념**

웹소켓 vs HTTP 통신


- **HTTP (Hypertext Transfer Protocol):**
    - 클라이언트(브라우저)와 서버 간의 **요청-응답 기반** 통신.
    - 요청이 발생할 때마다 서버와 클라이언트가 새로운 **TCP 연결**을 열고 닫음.
    - **Stateless(비연결 지향적)**: 각 요청은 독립적이고 이전의 요청과 상태를 공유하지 않음.

- TCP
    
    ### **TCP (Transmission Control Protocol)**
    
    - **TCP**는 네트워크에서 **신뢰성 있는 데이터 전송**을 제공하는 **연결 지향 프로토콜**입니다.
    - 송신 측과 수신 측 사이에서 **패킷 손실 없이 데이터 순서를 보장**합니다.
    - 데이터를 보내기 전 **3-way 핸드셰이크** 과정을 통해 연결을 설정합니다.
    
    ### **TCP 3-way Handshake 과정**
    
    1. **SYN**: 클라이언트가 서버에 연결 요청(SYN)을 보냄.
    2. **SYN-ACK**: 서버가 연결 요청을 수락하고 응답(SYN-ACK)을 보냄.
    3. **ACK**: 클라이언트가 확인 응답(ACK)을 보내고 연결이 확립됨.
    
    TCP는 HTTP와 WebSocket 모두의 **기초 프로토콜**로 사용됩니다. 그러나 연결을 관리하는 방식에서 차이가 있습니다.
    

- **WebSocket:**
    - **양방향 통신**을 지원하는 프로토콜로, 클라이언트와 서버가 **한 번의 연결로 지속적인 통신** 가능.
    - HTTP 핸드셰이크로 연결을 시작한 뒤 **WebSocket 프로토콜로 업그레이드**하여 **항상 열린 연결** 유지.
    - **Full-duplex(전이중 통신)**: 클라이언트와 서버 모두 자유롭게 메시지를 주고받을 수 있음.

---

### **2. HTTP와 WebSocket의 구현 방식 차이**

### **HTTP 통신 흐름**

1. 클라이언트가 서버로 HTTP 요청을 보냄.
2. 서버가 요청을 처리하고 응답을 반환.
3. 응답이 완료되면 연결이 종료됨.
4. 실시간 정보가 필요할 경우 **Polling**(주기적인 재요청) 또는 **Long Polling**(요청을 열어두었다가 응답) 방식 사용.

### **WebSocket 통신 흐름**

1. 클라이언트가 HTTP로 서버에 **WebSocket 핸드셰이크** 요청을 보냄.
    
    ```vbnet
    
    GET /chat HTTP/1.1
    Host: example.com
    Upgrade: websocket
    Connection: Upgrade
    
    ```
    
2. 서버가 WebSocket 핸드셰이크를 승인하며 연결 업그레이드.
3. 연결 후 클라이언트와 서버는 **동시에 데이터를 송수신** 가능.
4. 연결이 필요 없을 때 명시적으로 닫음.

---

### **3. HTTP vs WebSocket: 장단점 비교**

| **특성** | **HTTP** | **WebSocket** |
| --- | --- | --- |
| **통신 방식** | 요청-응답 (Request-Response) | 양방향 (Full-duplex) 통신 |
| **연결 유지** | 요청마다 새 연결 생성 | 지속적인 연결 유지 |
| **실시간 처리** | Polling, Long Polling 등 추가적인 기법 필요 | 실시간 데이터 전송에 최적화 |
| **데이터 오버헤드** | 요청 및 응답마다 헤더 포함 | 연결 후에는 헤더가 거의 없이 메시지만 전송 |
| **반응성** | 클라이언트 요청이 있어야만 서버가 응답 | 서버와 클라이언트 모두 언제든 데이터 전송 가능 |
| **스케일링** | 간단한 요청에 적합하지만, 많은 실시간 요청은 비효율 | 고빈도의 실시간 메시지 처리에 유리 |
| **보안** | HTTPS로 암호화된 통신 | **WSS(WebSocket Secure)**로 암호화 가능 |
| **장점** | 간단한 통신에 적합, 인프라가 잘 구축됨 | 실시간 알림, 채팅, 주식 시세 등 이벤트 기반 처리에 적합 |
| **단점** | 실시간 이벤트 처리에 비효율적 | 초기 설정이 복잡하고 더 많은 서버 자원 필요 |

---

### **4. WebSocket 사용이 적합한 경우**

1. **실시간 데이터 송수신이 중요한 경우**
    - 채팅 애플리케이션 (예: 메시지를 실시간으로 주고받음).
    - 주식/코인 시세 업데이트 (초 단위로 변동되는 데이터).
    - 온라인 게임(게임 상태를 서버와 실시간 동기화).
2. **서버가 클라이언트에 자주 데이터를 푸시해야 하는 경우**
    - 알림 시스템 (서버가 직접 클라이언트에 이벤트를 전송).
3. **빈번한 요청과 응답이 필요한 경우**
    - IoT 기기 통신(온도, 위치 정보 등을 실시간으로 전송).
    - 미터링 시스템(데이터를 지속적으로 업데이트하여 보여줌).

### **5. WebSocket과 HTTPS 통신의 선택 기준**

- **HTTP/HTTPS**:
    - 정적 콘텐츠 제공(예: HTML, CSS, 이미지)
    - 간헐적인 요청/응답 기반의 통신이 충분한 경우(예: 블로그, 상품 페이지 로딩)
- **WebSocket**:
    - 실시간 통신이 핵심인 경우(예: 주문 처리 중 실시간 상태 업데이트)
    - 서버와 클라이언트 간의 빈번한 메시지 송수신이 필요한 경우(예: 미터링 데이터 처리)

---

### **6. WebSocket 사용 시 고려해야 할 사항**

- **자원 관리**: 연결을 계속 유지하기 때문에 **서버의 부하**를 잘 관리해야 함.
- **보안**: WSS를 사용해 암호화된 통신을 해야 함.
- **네트워크 장애 처리**: 연결이 끊어졌을 때 자동으로 재연결하는 로직 필요.
- **스케일링**: 여러 클라이언트가 동시에 접속하는 경우 **로드 밸런싱과 세션 관리**가 중요함.

### **1) HTTPS API 요청 방식**

- **`fetch()` 또는 `axios` 라이브러리**를 사용해 API 호출.
- 예시:
    
    ```jsx
    javascript
    코드 복사
    fetch('https://api.example.com/products')
      .then(response => response.json())
      .then(data => console.log(data));
    
    ```
    
- **특징**:
    - 요청 시마다 **새 연결**이 생성되고 응답 후 **연결이 종료**됩니다.
    - 주로 **단발성 요청**(예: 로그인, 상품 조회)에 사용.

### **2) WebSocket API 연결 방식**

- **WebSocket 객체**를 사용해 지속적인 연결 유지.
- 예시:
    
    ```jsx
    javascript
    코드 복사
    const socket = new WebSocket('wss://api.example.com/updates');
    
    socket.onopen = () => console.log('Connected');
    socket.onmessage = event => console.log('Data:', event.data);
    socket.onerror = error => console.error('Error:', error);
    
    ```
    
- **특징**:
    - 한 번 연결하면 **지속적인 데이터 송수신**이 가능.
    - 실시간 데이터 스트림(예: 알림, 주문 상태) 처리에 적합.

---

만약 두가지 통신을 모두 사용해야 하는 경우는 어떻게 할까? 

### **모든 API를 통합해서 설계할 수 있는 방법**

### **방법 1: 대부분의 API는 HTTPS로, WebSocket은 실시간 업데이트에만 사용**

- **기본적으로 HTTPS**를 사용하고, **특정 상황**에서만 WebSocket을 활용하는 것이 일반적입니다.
- 예시:
    - **로그인/회원 정보/상품 목록**: HTTPS API 사용
    - **주문 상태/실시간 알림**: WebSocket 사용

---

### **방법 2: WebSocket과 HTTPS 혼합 사용을 위한 설계 패턴**

1. **HTTPS로 초기 데이터 요청, WebSocket으로 실시간 업데이트**
    - 예시:
        - 상품 목록을 **HTTPS API**로 처음 불러오고,
        - 이후 **WebSocket**을 통해 상품 가격이나 재고 변동 사항을 실시간으로 업데이트.
        
        ```jsx
        javascript
        코드 복사
        // HTTPS로 초기 데이터 가져오기
        fetch('https://api.example.com/products')
          .then(response => response.json())
          .then(data => console.log(data));
        
        // WebSocket으로 실시간 업데이트 받기
        const socket = new WebSocket('wss://api.example.com/updates');
        socket.onmessage = event => console.log('Update:', event.data);
        
        ```
        
2. **API 게이트웨이**를 사용해 통합
    - **API 게이트웨이**를 통해 **HTTPS와 WebSocket을 통합**할 수 있습니다.
    - 클라이언트는 **하나의 API 엔드포인트**를 호출하지만, 게이트웨이에서 각각의 요청을 HTTPS나 WebSocket으로 적절히 처리합니다.

### **HTTPS와 WebSocket 통합 사용 시 고려 사항**

1. **인증 및 보안**
    - **HTTPS** API와 **WebSocket** 모두 **JWT 토큰**이나 세션 쿠키를 통해 인증을 공유할 수 있습니다.
        
        ```jsx
        javascript
        코드 복사
        const token = 'your-jwt-token';
        
        // HTTPS 요청에 토큰 포함
        fetch('https://api.example.com/orders', {
          headers: { Authorization: `Bearer ${token}` }
        });
        
        // WebSocket 연결 시 토큰 전달
        const socket = new WebSocket(`wss://api.example.com/updates?token=${token}`);
        
        ```
        
2. **연결 유지 및 재연결 처리**
    - WebSocket은 **네트워크 문제로 연결이 끊길 수 있으므로 재연결 로직**이 필요합니다.
        
        ```jsx
        javascript
        코드 복사
        let socket;
        function connectWebSocket() {
          socket = new WebSocket('wss://api.example.com/updates');
          socket.onclose = () => setTimeout(connectWebSocket, 1000); // 자동 재연결
        }
        connectWebSocket();
        
        ```
        
3. **서버 부하와 비용 관리**
    - WebSocket 연결은 서버에 **지속적인 자원 사용**을 요구하므로 **사용량이 많은 경우 부하 관리**가 필요합니다.
    - HTTPS API는 **캐싱과 로드 밸런싱**이 쉬워서 대규모 트래픽을 처리하기에 유리합니다.