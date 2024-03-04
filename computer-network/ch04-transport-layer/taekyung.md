# 포트

### 전송 계층의 역할

- 응용 계층의 어플리케이션 프로세스 식별
- 네트워크 계층의 신뢰성/연결성 확립

### 포트

- 포트 번호는 16비트로 표현 가능 (65536개 = 2^16)
- 포트 범위 : 0번부터 65535번까지
- 잘 알려진 포트 (0~1023) 등록된 포트 (1024~49151) 동적 포트(49152~65535)

**잘 알려진 포트(well-known port, 시스템 포트)**

FTP, SSH,SMTP, DNS, DHCP, HTTP , HTTPS

**등록된 포트 (특정 기업 혹은 어플리케이션)**

OpenVPN, Microsoft SQL Server 데이터베이스, MySQL 데이터베이스, Redis, HTTP 대체

# TCP와 UDP

**MSS**(Maximum Segment Size) : TCP 세그먼트로 보낼 수 있는 최대 크기

TCP는 UDP가 제공하지 않는 다양한 기능을 제공

**TCP 세그먼트 구조**

- 출발지 포트
- 목적지 포트
- **순서 번호 :** 송수신되는 세그먼트 데이터 첫 바이트에 부여되는 번호 (포지셔널 인코딩같은 개념)
- **확인 응답 번호 :** 순서 번호에 대한 응답 (다음으로 수신받길 기대하는 바이트 번호)
- 제어 비트
    - ACK : 세그먼트 승인을 나타내는 비트
    - SYN : 연결 수립을 위한 비트
    - FIN : 연결을 끝내기 위한 비트
    - RST : 연결을 리셋하기 위한 비트
- 윈도우 : 수신지 윈도우 크기. 한번에 수신 받고자 하는 양

**UDP 데이터 그램**

UDP는 IP 패킷을 감싸는 껍데기

- 비연결성/비신뢰성 프로토콜
- TCP의 재전송/흐름 제어/혼잡 제어 등의 기능 없음

cf) 체크섬 필드

TCP는 하나 하나 확실해 보내기, UDP는 빠르게 마구 보내기

→ 최근 빠른 성능으로 UDP가 각광 받고 있음

# TCP 연결

TCP는 연결 수립작업과 데이터를 주고 받는 작업, 연결 끊는 작업이 있음

### TCP는 연결형 프로토콜

1. 연결 설정 ← Three-way handshake
2. 데이터 송수신
3. 연결 종료

# TCP 상태

### TCP는 스테이트풀(stateful) 프로토콜

- CLOSED : 아무런 연결이 없는 상태
- LISTEN, SYN-SENT ,SYN-RECIEVED : 연결 수립 도중 사용되는 상태
    - LISTEN : SYN 세그먼트를 기다리는 상태 (서버 호스트)
    - SYN-SENT : SYN 세그먼트를 보낸 뒤 SYN+ACK를 기다리는 상태
    - SYN-RECEIVED : SYN + ACK 세그먼트를 보낸 뒤 그에 대한 ACK 대기
- ESTABLISHED : 연결이 되어있는 상태, Three-way-handshake가 끝난 상태
- FIN-WAIT-1, FIN-WAIT-2, CLOSE-WAIT, CLOSING, LAST-ACK, TIME-WAIT : 연결 해제시 사용되는 상태
    
    

# TCP 재전송 기능

TCP는 신뢰성 프로토콜

- 무엇인가를 확실히 전송했다는 보장이 있으려면?
    
    →재전송 기반의 오류 제어: 잘못 전송된 경우 재전송
    
    →흐름 제어: 받을 수 있을 만큼만 받기
    
    → 혼잡 제어: 보낼 수 있는 상황에서만 보내기
    

- 언제 잘못 되었음을 인지할까?
1. 중복된 ACK 세그먼트를 수신했을 때
2. 타임아웃이 발생 했을때 (TCP 내부의 재전송 타이머, 운영체제가 관리)

TCP 오류 제어

- TCP는 재전송 기반의 오류 제어를 수행
- 재전송을 기반으로 잘못된 전송을 바로 잡는 것 : ARQ (자동 재전송 요구)

### ARQ

1.Stop-and-Wait ARQ

- 비효율의 문제 → 파이프라인

2.Go-Back-N ARQ

3.Selective Repeat ARQ

빠른 재전송(fast retransmit)

# TCP의 혼잡 제어와 흐름 제어

파이프라이닝 전송

→ 송수신 입장에서 무엇을 고려해야 할까?

### TCP 흐름 제어

송신 호스트가 수신호스트 처리 속도를 고려하여 송수신 속도를 균일하게 맞추는 것

오늘날 TCP에서의 흐름 제어 → 슬라이딩 윈도우(sliding window)

cf) 송신 버퍼와 수신 버퍼

송신 버퍼 : 어플리케이션 계층에서 전송할 데이터 임시 저장

수신 버퍼 : 네트워크 계층에서 수신할 데이터 임시 저장

cf) 윈도우 : 파이프라이닝 가능한 순서번호 범위

윈도우 크기 : 확인 응답 받지 않고도 한번에 보낼 수 있는 최대 양

(수신)윈도우 크기 : TCP 헤더를 통해 송신지에게 알려주는 정보

수신 윈도우 = 수신 버퍼 크기 - [마지막으로 수신한 바이트 - 마지막으로 읽어드린 바이트]

수신 윈도우 ≥ 마지막으로 송신한 바이트 - 마지막으로 수신 확인된 바이트

### TCP 혼잡 제어

- 많은 트래픽으로 인해 패킷 처리 속도가 느려지거나 유실될 우려가 있는 상황
- 혼잡이 생기지 않을 정도로만 조금씩 전송하는 방법
- 주체 : 송신지! (흐름제어의 주체: 수신지)
- 혼잡 윈도우 : 혼잡 없이 전송할 수 있을 법한 양 (헤더에 포함X)

최소값(수신 윈도우, 혼잡 윈도우) ≥ 마지막으로 송신한 바이트 - 마지막으로 수신 확인된 바이트

- 기본 동작 형태: AIMD(Additive Increase Multicative Decrease)