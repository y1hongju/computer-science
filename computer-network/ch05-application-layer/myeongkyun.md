# 5. 응용 계층
- DNS
    - DNS란
        - 전화번호부와 유사
        - 사람이 기억하기 쉬운 도메인 이름과 호스트를 특정지을 주소를 매핑
    - 서브 도메인 (하위 도메인)
        - 도메인의 일부인 도메인
            - [naver.com](http://naver.com) → `maps`.naver.com
    - 도메인 서버
        - ROOT 네임 서버
        - TLD 서버
        - Authoritative DNS 서버 : 찾고자 하는 도메인의 IP주소를 저장하는 최종 서버
        - local DNS 서버 : 클라이언트가 가장 먼저 찾는 DNS 서버 (DNS Resolver)
            - 명시적 설정 → Public DNS
            - 자동 설정 → ISP
    - DNS 서버
        - 데이터
            - DNS 레코드 (자원 레코드)
                - A 레코드 : 도메인에 대한 IPv4 주소
                - AAAA 레코드 : 도메인에 대한 IPv6 주소
                - CNAME 레코드 : 도메인에 대한 별칭
                - NS 레코드 : 네잌 서버 주소
                - SOA 레코드 : 도메인에 대한 관리자 정보
    - DNS 캐시
        - TTL 기간동안 DNS 저장
- URI/URL/URN
    - 네트워크 상에서의 `자원`
        - 네트워크로 주고받을 수 있는 모든 정보
        - 파일, 이미지, 동영상, HTML, XML, JSON …
        - 자원을 요청하고 응답하려면 자원을 식별(identify)할 수 있어야함
    - URI
        - 자원의 식별자, 자원을 식별할 수 있는 문자열
        - 분류
            - URL : 위치 기반 자원 식별 (Locator)
            - URN : 이름 기반 자원 식별 (Name)
    - URL 구성 요소
        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/07758da2-5148-4baa-b95f-ccb08c3a2587/8f4e14e0-9967-482f-87c7-b275fd3138d7/Untitled.png)
        - scheme : https, http 등
        - authority : [userinfo “@”] host [”:”port ] 보통은 생략
        - path : [https://www.example.com/`_img/2015.1/…`](https://www.example.com/_img/2015.1/…) 자원이 있는 경로
        - query : key:value 형태로 서버에 전달할 문자 형태의 파라미터
        - fragment : [https://~~`#socket-families`](https://~~#socket-families) 북마크 기능
- 웹 서버, 웹 어플리케이션 서버
    - 서버 - 클라이언트의 개념
        - 서버 : 대답하는 대상 (response)
            - 웹 서버
            - 웹 어플리케이션 서버
        - 클라이언트 : 요청하는 대상 (request)
        - 서버가 응답해야 하는 자원
            - 정적인 자원 : 언제/어디서/누가 봐도 변하지 않는 정보 (e.g HTML, 이미지, 동영상 …)
                - 웹 서버
            - 동적인 자원 : 언제/어디서/누가 보는지에 따라 변할 수 있는 정보
                - 웹 어플리케이션 서버
- HTTP의 특성
    - 요청-응답 기반 클라이언트-서버 구조 프로토콜
        - HTTP 클라이언트 (HTTP 요청 메시지)
        - HTTP 서버 (HTTP 응답 메시지)
        - 오해 방지 : 서버 간에도 HTTP 메시지를 주고받을 수 있음
    - 미디어-독립적 프로토콜
        - 어떤 형태의 데이터도 HTTP 메시지로 보낼 수 있음
    - 비연결성 프로토콜
        - HTTP 1.0, HTTP 1.1, HTTP 2.0은 TCP 기반
        - TCP는 연결성 프로토콜
        - But, HTTP는 비연결성 프로토콜
            - 다수의 클라이언트가 연결을 시도할 경우
            - 연결을 유지하는 동안 서버의 자원 소모가 너무 크다
    - 스테이트리스 프로토콜
        - 서버는 클라이언트의 상태를 기억하지 않는다
        - 스테이트리스가 아닐 경우
            - 클라이언트가 한 서버에 종속됨
            - 여러 요청을 보내야 할 경우 한 서버에만 요청해야함
    - 지속 연결 프로토콜
        - 연결할 때마다 3-way handshake를 할 순 없음
            - 혼잡 증가
            - 시간 지연 증가
        - 지속 연결 : 하나의 연결을 사용해 여러 개의 HTTP 요청/응답 주고받기
- HTTP 버전별 특성
    - HTTP 0.9 : 단일한 요청 방법 (GET 메서드), 비지속 연결, 별다른 기능 X
    - HTTP 1.0 : 다양한 요청 방법과 헤더 추가
    - HTTP 1.1 : 지속 연결 기능 추가
    - HTTP 2.0 : 요청 순서대로 응답을 반환할 필요 없음, 헤더 압축
    - HTTP 3.0 : UDP 기반 프로토콜인 QUIC로 변경
- HTTP 메시지 개관
    - HTTP 요청 헤더 - Start line
        - HTTP 메서드 요청 대상 HTTP 버전 (e.g GET /restapi/v1.0 HTTP/1.1)
    - HTTP 메서드 : 서버에게 요청할 동작 (해당 자원으로 어떤 동작을 요청할 지)
        - GET : 자원 조회
            - 일반적으로 쿼리 문자열 사용, 본문은 없음
        - POST : 요청할 데이터 처리
            - 본문으로 처리할 데이터 전송
            - 본문에 해당하는 데이터 처리하도록 요청
        - PUT : 자원 덮어쓰기
            - 본문으로 보낸 데이터로 대체 혹은 생성
        - PATCH : 자원 부분 변경
        - DELETE : 자원 삭제
        - 이외 → HEAD, OPTIONS, TRACE, CONNECT 등
        - 멱등성 : 여러 번 동일한 요청을 보내도 첫 요청 결과와 같은가
        - 캐시 가능성 : 응답 결과를 캐시해서 사용할 수 있는가
    - 응답 코드
        - 2XX : 성공
            - 200 OK : 요청 성공 (GET)
            - 201 Created : 요청 성공, 새로운 자원 생성됨 (POST)
            - 202 Accepted : 요청 성공, 처리는 아직 미완료
            - 204 No Content : 요청 성공, 응답할 데이터 없음
        - 3XX : 리다이렉션 (이 요청을 처리하려면 추가적인 처리가 필요)
        - 4XX : 클라이언트 오류
            - 401 Unauthorized : 미인증
            - 403 Forbidden : 금지된 자원에 접근 (권한 부족)
            - 404 Not Found : 요청한 자원 없음 (공개한 자원 아님)
        - 5XX : 서버 오류
            - 500 Internal Server Error : 서버 오류
            - 503 Service Unavallable : 현재 이용 가능하지 않음
    - 헤더 정보
        - Host : 요청 호스트에 대한 호스트명 + 포트 정보
        - Date : 메시지 생성 시간
        - Referer : 직전에 머물렀던 URL
        - User-Agent : 클라이언트 소프트웨어, 브라우저 명칭과 정보
        - Server : 서버 소프트웨어 명칭과 정보
        - Connection : keep-alive 일 경우
        - Location :  리다이렉트시 이동할 경로
        - Content-Type
            - HTTP 요청 및 응답에서 사용될 컨텐츠의 유형
            - MIME 타입으로 명시
        - Content-Encoding : 데이터의 인코딩/압축 방식
        - Content-Language : 데이터의 언어
    - 캐시
        - 캐시란
            - 서버의 지연을 줄이기 위해 웹페이지, 이미지 등의 자원 사본을 임시 저장하는 웹 기술
            - 캐시된 자원이 저장되는 공간 : 클라이언트(브라우저) 혹은 특별한 서버(캐시, 프록시 서버)
        - cache-control 헤더
            - 캐시 기능을 알리는 헤더
            - cache-control : max-age=숫자(초) 캐시한 자원의 지속 시간
            - cache-control : no-cache
                - 캐시 가능한 자원이나, 항상 origin 서버에 검증하기
            - cache-control : no-store
                - 캐시하면 안될 자원
            - 캐시된 자원에는 유효 기간이 있음
                - 마지막으로 변경되었는지 알리는 Last-Modified 헤더
        - 캐시된 자원의 변경
            - 클라이언트는 요청시 if-modified-since 헤더로 특정 시점 이후 자원 변경 여부 확인
                - 변경 되었다면 다시 다운로드
                - 변경 되지 않았다면 304 Not Modified 응답
            - Etag를 통해 자원의 변경 여부 감지 가능
                - 서버는 캐시된 자원이 Etag라는 식별 문자를 붙이고
                - 클라이언트는 If-None-Match 헤더를 통해 해당 Etag가 변경되었는지 확인
    - 쿠키
        - 쿠키란
            - 서버로부터 받은 정보를 클라이언트 측에 임시 저장되는 이름=값 형태의 데이터
            - 유효기간 있음
            - 쿠키를 전송할 도메인과 경로가 정해져 있음
            - 서버가 Set-Cookie 헤더로 쿠키를 전달하면, 클라이언트는 쿠키를 저장하여 다음 HTTP 요청의 Cookie 헤더로 활용한다
            - 쿠키는 보안에 민감하다
            - 쿠키의 저장/관리 주체는 클라이언트
        - Set-Cookie
            - Set-Cookie : domain=example.com
                - example.com에 접글할 때 쿠키 사용
            - Set-Cookie : path=
                - path에 명시된 경로 하위 경로에서 쿠키 활용
            - 쿠키의 유효기간
                - Set-Cookie : expires=Wed, 10 Aug 2023 12:00:00 GMT
                - Set-Cookie : max-age=1000
- 세션
    - 세션이란
        - 세션의 저장/관리 주체는 서버
- 컨텐츠 협상 (Content negotiation)
    - 클라이언트가 원하는 컨텐츠를 받을 수 있도록 서버에게 부탁하는 기능
    - 컨텐츠 타입, 언어, 인코딩 방법 등 클라이언트 맞춤 컨텐츠 제공 기능
    - Accept(-X) 헤더 이용
    - 여러 맞춤 요청을 보낼 수 있음
