# 전송 계층
- 전송 계층의 역할
    - `응용 계층의 어플리케이션 프로세스 식별 : 포트`
    - 네트워크 계층의 신뢰성/연결성 확립
- 포트
    - 포트 번호는 16비트로 표현 (65536개)
    - 분류
        - 웰노운포트 (0~1023)
        - 등록된 포트 (1024~49151)
        - 동적 포트 (49152~65535)
- TCP / UDP
    - TCP
        - MSS : TCP 세그먼트로 보낼 수 있는 최대 크기
        - TCP 세그먼트 구조
            - 출발지 포트 , 목적지 포트
            - 순서 번호 : 송수신되는 세그먼트 데이터 첫 바이트에 부여되는 번호
            - 확인 응답 번호
            - 제어 비트
                - ACK : 세그먼트 승인을 나타내는 비트
                - SYN : 연결 수립을 위한 비트
                - FIN : 연결을 끝내기 위한 비트
                - RST : 연결을 리셋하기 위한 비트
            - 윈도우 : 수신지 윈도우 크기. 한번에 수신 받고자 하는 양
        - TCP 연결형 프로토콜
            - 순서
                - 연결 설정 `(Three-way handshake)`
                - 데이터 송수신
                - 연결 종료
        - TCP 스테이트풀 프로토콜
            - 스테이트풀 프로토콜이란
                - 현재 연결 상태를 나타내기 위해 다양한 상태 활용
            - 상태 목록
                - CLOSED : 연결 없음
                - LISTEN : SYN  세그먼트로 기다리는 상태 (서버 호스트)
                - SYN-SENT :  SYN 세그먼트를 보낸 뒤 SYN+ACK 세그먼트를 기다리는 상태
                - SYN-RECEIVED : SYN+ACK 세그먼트를 보낸 뒤  ACK 세그먼트를 기다리는 상태
                - ESTABLISHED : 연결되어 있는 상태 (데이터 송수신 가능)
                - FIN-WAIT-1
                - FIN-WAIT-2
                - CLOSE-WAIT
                - CLOSING
                - LAST-ACK
                - TIME-WAIT
        - TCP 재전송
            -  재전송 기반의 오류 제어 : 잘못 전송된 경우 재전송
                - 중복된 ACK 세그먼트를 수신했을 때
                - 타임아웃이 발생했을 때
            - TCP 오류 제어
                - 재전송 기반의 오류 제어를 수행 ARQ (자동 재전송 요구)
                - Stop-and-Wait ARQ
                    - 전송, 확인, 전송, 확인
                - Go-Back-N ARQ (누적 확인 응답)
                    - 올바른 세그먼트에 대해 확인 응답
                    - 올바르지 않으면 n+1번 이후 모두 폐기
                - Selective Repeat ARQ
                    - 올바른 세그먼트에 대해서만 확인 응답 (개별 확인 응답)
                - 빠른 재전송 (fast retransmit)
                    - 재전송 타이머를 기다리지 않고 중복 세그먼트 3번 이후 재전송
            - TCP 흐름/혼잡 제어
                - 송신 버퍼와 수신 버퍼
                    - 송신 버퍼 : 어플리케이션 계층에서 전송할 데이터 임시 저장
                    - 수신 버퍼 : 네트워크 계층에서 수신할 데이터 임시 저장
                    - 버퍼 오버플로우 : 일부 데이터가 처리되지 않을 수 있음
                - TCP 흐름 제어
                    - 송신 호스트가 수신 호스트 처리 속도를 고려하며 송수신 속도를 균일하게 맞추는 것
                    - 오늘날 TCP 흐름 제어 → 슬라이딩 윈도우 (sliding window)
                    - 수신 호스트
                        - 수신 윈도우 = 수신 버퍼 크기 - [마지막으로 수신한 바이트 - 마지막으로 읽어들인 바이트]
                    - 송신 호스트
                        - 수신 윈도우 ≥ 마지막으로 송신한 바이트 - 마지막으로 수신 확인된 바이트
                    - 윈도우 : 파이프라이닝 가능한 순서번호 범위
                    - 윈도우 크기 : 확인 응답 받지 않고도 한번에 보낼 수 있는 최대 양
                - TCP 혼잡 제어
                    - 혼잡이란
                        - 많은 트래픽으로 인해 패킷 처리 속도가 느려지거나 유실될 우려가 있는 상황
                    - 혼잡 제어가 이루어 지지 않는다면
                        - 혼잡 → 유실 → 재전송 → 혼잡 → 유실 → 재전송 → … (혼잡 붕괴 현상)
                    - 혼잡 제어
                        - 혼잡 윈도우 : 혼잡 없이 전송할 수 있을 법한 양
                        - 송신 호스트
                            - 최소값(수신 윈동, 혼잡 윈도우) ≥ 마지막으로 송신한 바이트 - 마지막으로 수신 확인된 바이트
                        - 기본 동작 형태 : AIMD(Additive Increase Multicative Decrease)
                        - 혼잡 제어 알고리즘
                            - 느린 시작
                                - ACK 세그먼트가 수신될 때마다 혼잡 윈도우 1 증가 (RTT마다 2배 증가)
                                - 특정 임계치(sshresh) 값과 같아지면 혼잡 회피 수행
                                - 특정 임계치 : 미리 정한 값
                            - 혼잡 회피
                                - 매 RTT마다 혼잡 윈도우 1씩 증가
                                - 세 번의 중복 세그먼트 발생할 경우 빠른 회복 수행
                            - 빠른 회복
                                - 느린 시작을 건너뛰고 혼잡 회피 수행
                                - TCP Tahoe (빠른 회복 미수행) vs TCP Reno (빠른 회복 수행)
                            - RTT(Round Trip Time)
                                - 메시지를 전송한 뒤 그에 대한 답변을 받는 시간
    - UDP
        - UDP란
            - UDP는 IP 패킷을 감싸는 껍데기일 뿐
                - 비연결성/비신뢰성 프로토콜
                - TCP의 재전송/흐름 제어/혼잡 제어 등의 기능 없음
# 요약
    - TCP : 하나하나 확실히
    - UDP : 빠르게 마구 던지기
