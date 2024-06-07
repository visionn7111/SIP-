# 1. SIP 호 설립 절차(No.1 ~ No.5)

![image](https://github.com/visionn7111/SIP-SDP-protocol-example/assets/169283479/c22504a8-5cfd-495d-a0cc-01fc72b8f481)

Wireshark에서 위 패킷을 보면 No.1~No.5 의 패킷이 세션을 설립하는 과정인 것을 확인할 수 있다. 위 상단바를 순서대로 설명한다면

- No : 패킷을 수집한 순서
- Time : 패킷이 수집된 시간
- Source : 패킷이 보낸 주소
- Destination : 패킷 도착 주소
- Protocol : 프로토콜 정보
- Length : 패킷의 길이
- Info : 패킷 정보

와 같다. 이번 분석은 Info의 정보를 통해 간략히 No.1 ~ No.5의 과정이 INVITE / 200 OK / ACK 과정을 갖는 실제 사용되는 SIP 호 설립 절차인 것을 알 수 있다.

## 1-1 No.1(INVITE 요청) 패킷

패킷 정보에 INVITE sip:test@10.0.2.15:5060 의 정보를 통해 INVITE 메시지라는 것을 알 수 있다. 또한 Wireshark의 기능을 통해 메시지의 header와, body를 확인 할 수 있다.

- **INVITE 요청 메시지 header**
    
  
![image](https://github.com/visionn7111/SIP-SDP-protocol-example/assets/169283479/933b80ac-16b7-41d1-a69c-460e99dbc608)

    SIP Header를 보면 편지 봉투와 유사한 형태의 정보를 알 수 있다.
    
    **1) sip:test@10.0.2.15:5060 SIP/2.0** 
    
    메시지 첫줄에는 Method와 메시지를 수신하는 최종 단말의 주소와 버전이 명기된다. 따라서 SIP 클라이언트는 'test' 사용자에게 통화요청을 보낸다. 이 요청은 '10.0.2.15'의 호스트에서 수신되며, 포트 번호는 '5060'을 통해 처리 된다. SIP 프로토콜의 버전은 2.0이다. 또한 추가 적인 정보를 확인하면
    
    **2) Via : SIP/2.0/UDP 10.0.2.20:5060;branch=z9hG4bk-2187-1-0**
    
    Via 헤더는 INVITE 요청에 대한 응답을 위한 경로를 나타낸다. branch는 시공간에서 유일한 값을 가지는 트랜잭션 식빌자이다. SIP/2.0은 프로토콜의 버전을 나탄낸다. UDP 프로토콜을 사용하고 송신자의 IP 주의와 포트 번호를 알 수 있다.
    
    **3) Max-Forwards** 
    
    시그널링 경로 상에 SIP 서버의 최대 홉 수로 IP 네트워크 TTL (Time to Live)와 같다.
    
    **4) To: test sip:test@10.0.2.15:5060**
    
    SIP 트랜잭션의 출발지를 나타내지만, 실제 SIP 메시지 라우팅에 사용되지 않으며 Display Named의 의미를 가진다.
    
    **5) From: "DVI4/8000" sip:sipp@10.0.2.20:5060;tag=1**
    
    SIP 트랜잭션의 목적지를 나타낸다. 실제 SIP 메시지의 라우팅에 사용되지 않으며 Display Name의 의미를 가 진다.
    
- INVITE 요청 메시지 body
    
    SDP는 Capability를 협상하기 위해 SIP 호 처리 절차에서 활용 된다. SDP 협상 내용은 SIP 메시지 바디 포함되어 전달되기 때문에 바디에서 확인 할 수 있다.
    
    **1) Session Description Protocol Version (v): 0**
    
    SDP 프로토콜 버전을 표시한다. SDP 버전은 0이다.
    
    **2) Owner/Creator, Session Id (o): - 42 42 IN IP4 10.0.2.20**
    
    SDP 메시지를 생성한 Owner/creator를 표시한다. 순서대로 Owner Username, Session-ID, Session Version, Network Type, Address Type, Unicast Address를 나타내며 아래 사진과 같이 정보를 확인할 수 있다.
    
![image](https://github.com/visionn7111/SIP-SDP-protocol-example/assets/169283479/a1f9508b-5ed3-4153-bda1-7460bd40b2ef)

    
    **3) Session Name (s): -**
    
    세션 이름을 표시한다.
    
    **4) Connection Information (c): IN IP4 10.0.2.20**
    
    순서대로 Network Type, Address type, Connection-Address이며, RTP 프로토콜이 사용할 주소를 정의 한다.
    
    ![image](https://github.com/visionn7111/SIP-SDP-protocol-example/assets/169283479/7358fa1c-9962-46d4-8df1-f8770bab17be)

    
    **5) Time Description, active time (t): 0 0**
    
    Timing으로 start-time과 stop-time을 표시한다. 0 0 은 고정 세션을 의미한다.
    
    **6) Media Description, name and address (m): audio 6000 RTP/AVP 5**
    
    SDP Capability Exchage를 위한 핵심 중 하나이다. Media Description으로 Media, Port, Protocol, Format을 정의한다.
    
    ![스크린샷 2024-06-06 오후 7.05.59.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/6f3c069e-3fa7-4bc9-8021-ff524a9bbfbd/a5c4734c-807b-46e5-a73c-940dc12d8bf8/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7_2024-06-06_%EC%98%A4%ED%9B%84_7.05.59.png)
    
    위 사진을 보면 RTP 프로토콜의 페이로드가 무엇인지 선언 한 것을 알 수 있다. audio를 표시하였고 포트는 6000번이 표시되었다. Protocol은 RTP/AVP 이고 Format은 5이며 a줄에서 추가로 설명된다. 하지만 사진위에도 설명이 되어 있는 것을 볼 수 있다.
    
    추가로 포트 번호는 16384에서 32767에서 설정된다고 배웠으나 6000번이 선택 되었다. 이는 Test이기 때문에 임의적으로 포트를 선택했기 때문이라고 생각된다.
    
    **7) Media Attribute (a): rtpmap:5 DVI4/8000, Media Attribute (a): recvonly**
    
    이 필드는 RTP 페이로드 타입 번호, 코덱, 그리고 샘플링 속도를 정의 한다. a= 는 속성 필드를 나타낸다. rtpmap은 RTP 매핑을 나타낸다. RTP 페이로드 타입 번호와 이를 해석하기 위한 코덱 및 샘플링 속도를 정의 한다. 5는 RTP 페이로드 타입 번호이고 DVI4는 코덱의 이름이다. 마지막으로 8000은 샘플링 속도(Hz)이다. 여기서는 8000Hz, 즉 8kHz 샘플링 속도를 사용한다.
    

## 1-2 No.2 (100 Trying) 패킷

![image](https://github.com/visionn7111/SIP-SDP-protocol-example/assets/169283479/4d1c657b-7c99-4cdc-8e11-de12f01db07a)


No.2 의 패킷인 이 패킷은 100 Trying의 SIP Response이다. 수신된 요청을 다음 서버로 전송하거나 처리중이라는 뜻이다. 일반적으로 100Trying은 INVITE 요청을 받자마자자 발행된다. 최종 응답인 200 OK를 발행하기 까지지 처리시간이 오래 걸리기 때문이다. 추가로 100 Trying 헤더의 branch 값과 INVITE 헤더의 branch 값이 동일하기 때문에 같은 트랜잭션이라는 것을 알 수 있다.

## 1-3 No.3 (RTP) 패킷

기본적인 호 설립 절차는 INVITE / 200 OK / ACK 이고, 실제 호 설립 절차는 INVITE / 100 Trying / 180 Ringing / 200 OK / ACK 이다 하지만 이 RTP 패킷이 발생한 원인을 3가지로 추측할 수 있다. 네트워크 오류, 소프트웨어 버그, 잘못된 트래픽이다.

## 1-4 No.4 (200 OK) 패킷

200 OK 응답은 요청이 정상적으로 처리되었음을 아리는 패킷이다. 메시지 header와 body를 통해 더 자세한 내용을 분석 할 수 있다.

- **200 OK 메시지 header**
    
    ![image](https://github.com/visionn7111/SIP-SDP-protocol-example/assets/169283479/87c8eed8-6281-4d07-82b2-bfa1db060f6e)

    
    From 헤더와 To 헤더 값이 바뀌지 않고 SIP INVITE 메시지의 헤더의 값이 동일하다 또한 CSeq 헤더는 INVITE 헤더의 값과 동일한 1 INVITE이다. CSeq는 SIP 패킷 캡처 시에 여러 호가 동시에 진행되더라도 어떤 요청에 대한 200 OK 응답인지를 분석할 수 있다.
    
- **200 OK 메시지 body**
    
    ![image](https://github.com/visionn7111/SIP-SDP-protocol-example/assets/169283479/f2fe921f-e744-45b6-92bd-372c65dc9f27)

    
    **1) Media Description, name and address (m): audio 30490 RTP/AVP 5 101**
    
    오디오 스트림이며, 포트 30490에서 RTP/AVP 프로토콜을 사용할 것을 정했습니다. 6000번 포트가 UDP 사용할 수 있는 프로토콜이 아니기 때문에 변경한 것을 볼 수 있다.
    
    **2) Media Attribute (a): rtpmap:5 DVI4/8000**
    
    패킷 형식을 정의하는 rtpmap : 5가 있다. 여기서는 DVI4 코덱을 사용하고, 샘플링 주파수는 8000Hz이다.
    
    **3) Media Attribute (a): rtpmap:101 telephone-event/8000**
    
    rtpmap : 101 은 telephone-event 코덱을 사용하고, 샘플링 주파수는 8000Hz이다.
    
    **4) Media Attribute (a): fmtp:101 0-16**
    
    fmtp:101은telephone-event 코덱의 파라미터를 정의한다. 여기서는 0부터 16까지의 이벤트를 전달할 수 있다
    
    **5) Media Attribute (a): sendonly**
    
    송신 전용 스트림을 뜻한다.
    
    **6) Media Attribute (a): ptime:20**
    
    패킷 길이를 2ms로 설정한다.
    

## 1-5 No.5 (ACK) 패킷

![image](https://github.com/visionn7111/SIP-SDP-protocol-example/assets/169283479/4645d2e4-eb17-459e-ab1c-8d0b47060822)


수신자의 전화기가 200 OK를 수신하였음을 확인하는 ACK를 전송한다.

CSeq 헤더 값이 1 이므로 앞의 200 OK에 대한 ACK임을 확인할 수 있다.

# 2. RTP로 전송된 오디오 스트림(No.6 ~ No.431)

![image](https://github.com/visionn7111/SIP-SDP-protocol-example/assets/169283479/b354a1de-2c5a-40a9-9789-b835c500734b)


이 RTP 패킷은 DVI4 코덱을 사용하고, 샘플링 주파수는 8000Hz이다. SSRC는 0x43DAB09로, 패킷 시퀀스 번호와 타임스탬프가 각각 시간에 따라 분류되었음을 알 수 있다. 이러한 패킷들은 오디오 데이터를 네트워크를 통해 전송하는데 사용될 수 있다. 즉, 앞에서의 호 설립 절차가 정상적으로 작동하여 통화중을 나타낸다.

# 3. SIP 호 종료 절차(No. 432 ~ N0. 433)

## 3-1 No.432 (BYE) 패킷

![image](https://github.com/visionn7111/SIP-SDP-protocol-example/assets/169283479/e048530f-6080-4690-be15-8cafebee3318)


BYE 세션은 발신자와 수신자 누구나 생성 할 수 있다. 전화기의 훅 스위치에 수화기를 올려 놓는 쪽에서 BYE가 전송된다. 세션 종료 절차는 새롭게 시작하는 다이얼로그이자 트랜잭션이므로 CSeq의 헤더 값이 다르다. 위 헤더의 정보로 From 헤더와 To 헤더의 값에서 알 수 있듯이 세션 종료 절차의 진행 방향이 반대로 바뀐 것을 알 수 있다 따라서 “Test”사용자가 호 종료를 진행 한 것을 볼 수 있다.

## 3-2 No.433 (200 OK) 패킷

![image](https://github.com/visionn7111/SIP-SDP-protocol-example/assets/169283479/fb12a6d9-f0ac-44b8-b7a8-694bd190a115)


200 OK 패킷은 요청이 정상적으로 성립된 것을 알리는 패킷이다. 위 CSeq를 확인하면 BYE에 대한 요청이 정상적으로 성립되었다. 즉, 전화가 종료 된것을 알 수 있다.

# 4. SIP 호 설립 절차 -2 (No. 434 ~ No. 438)

## 4-1 No.434 (INVITE) 패킷

앞에서 SIP 호 설립을 정상적으로 진행 한 후 BYE를 통해서 호를 종료 하였다. 그 후 두번째 호를 설립하는 과정이다.

- **INVITE 요청 메시지 header**
    
    ![image](https://github.com/visionn7111/SIP-SDP-protocol-example/assets/169283479/34f1f319-b4d1-4ba9-b061-d0f28f117bd0)

    **1) Request-Line: INVITE sip:test@10.0.2.15:5060 SIP/2.0**
    
    메시지 첫줄에는 Method와 메시지를 수신하는 최종 단말의 주소와 버전이 명기된다. 따라서 SIP 클라이언트는 'test' 사용자에게 통화요청을 보낸다. 이 요청은 '10.0.2.15'의 호스트에서 수신되며, 포트 번호는 '5060'을 통해 처리 된다. SIP 프로토콜의 버전은 2.0이다. 
    
    **2) Via: SIP/2.0/UDP 10.0.2.20:5060;branch=z9hG4bK-2189-1-0**
    
    Via 헤더는 INVITE 요청에 대한 응답을 위한 경로를 나타낸다. branch는 시공간에서 유일한 값을 가지는 트랜잭션 식빌자이다. SIP/2.0은 프로토콜의 버전을 나탄낸다. UDP 프로토콜을 사용하고 송신자의 IP 주의와 포트 번호를 알 수 있다.
    
    **3) Max-Forwards: 70**
    
    시그널링 경로 상에 SIP 서버의 최대 홉 수로 IP 네트워크 TTL (Time to Live)와 같다.
    
    **4) To: test sip:test@10.0.2.15:5060**
    
    SIP 트랜잭션의 출발지를 나타내지만, 실제 SIP 메시지 라우팅에 사용되지 않으며 Display Named의 의미를 가진다.
    
    **5) From: "DVI4/16000" sip:sipp@10.0.2.20:5060;tag=1**
    
    SIP 트랜잭션의 목적지를 나타낸다. 실제 SIP 메시지의 라우팅에 사용되지 않으며 Display Name의 의미를 가 진다.
    
- **INVITE 요청 메시지 body**
    
    ![image](https://github.com/visionn7111/SIP-SDP-protocol-example/assets/169283479/00dd3a80-65e9-4760-8892-bdc83dce7c18)

    
    1) Session Description Protocol Version (v): 0
    
    SDP 프로토콜 버전을 표시한다. SDP 버전은 0이다.
    
    2) Owner/Creator, Session Id (o): - 42 42 IN IP4 10.0.2.20
    
    ![image](https://github.com/visionn7111/SIP-SDP-protocol-example/assets/169283479/a6ece14b-337e-47dd-9b34-6c54ffa21dfa)

    
    SDP 메시지를 생성한 Owner/creator를 표시한다. 순서대로 Owner Username, Session-ID, Session Version, Network Type, Address Type, Unicast Address를 나타내며 아래 사진과 같이 정보를 확인할 수 있다.
    
    3) Session Name (s): -
    
    세션의 이름을 나타낸다. 여기에서는 비어있다.
    
    4) Connection Information (c): IN IP4 10.0.2.20
    
    순서대로 Network Type, Address type, Connection-Address이며, RTP 프로토콜이 사용할 주소를 정의 한다.
    
    5) Time Description, active time (t): 0 0
    
    Timing으로 start-time과 stop-time을 표시한다. 0 0 은 고정 세션을 의미한다.
    
    6) Media Description, name and address (m): audio 6000 RTP/AVP 6
    
    미디어 스트림의 설명을 제공한다. 오디오 스트림 6000번 포트를 통해 RTP/AVP 프로토콜을 사용하여 전송한다. 페이로드 유형 6번이다.
    
    7) **Media Attribute (a): rtpmap:6 DVI4/16000**
    
    미디어의 속성을 정의한다. DVI4 코덱을 사용하며, 샘플링 주파수는 16000Hz이다.
    
    8) Media Attribute (a): recvonly
    
    미디어 속성을 정의하고, 이 세션에서는 미디어를 수신만 하고 송신하지 않음을 나타낸다.
    

## 4-2 No.435 (100 Trying) 패킷

![image](https://github.com/visionn7111/SIP-SDP-protocol-example/assets/169283479/d5c46c82-64ad-4b4f-8b7c-05a128b77625)


No.2 의 패킷인 이 패킷은 100 Trying의 SIP Response이다. 수신된 요청을 다음 서버로 전송하거나 처리중이라는 뜻이다. 일반적으로 100Trying은 INVITE 요청을 받자마자자 발행된다. 최종 응답인 200 OK를 발행하기 까지지 처리시간이 오래 걸리기 때문이다.

## 4-3 No.436 (RTP) 패킷

일반적으로 SIP을 사용하여 호를 설정할 때 RTP 패킷은 호가 성립되고 오디오 스트림이 교환 되기 전에는 전송되지 않는다. 그러나 여기서 호가 설정되고  SIP/SDP 메시지를 통해 미디어어 협상이 이루어진 후에도 RTP 패킷이 전송 되었다. 따라서 오류 인것으로 생각된다. 오류의 원인을 3가지로 추측할 수 있다. 네트워크 오류, 소프트웨어 버그, 잘못된 트래픽이다.

## 4-4 No.437 (200 OK) 패킷

- 200 OK 메시지 header
    
    ![image](https://github.com/visionn7111/SIP-SDP-protocol-example/assets/169283479/7f0aabc2-49d6-4920-a4f2-32d6a9213524)

    
    요청에 대한 처리가 정상적으로 진행 된 것을 알 수 있다. CSeq 를 확인하면 INVITE의 대한 요청임을 확인 할 수 있다.
    
- 200 OK 메시지 body
    
    ![image](https://github.com/visionn7111/SIP-SDP-protocol-example/assets/169283479/9f616e64-fc86-431b-951e-d69fb8b956ce)

    
    **1) Media Description, name and address (m): audio 25146 RTP/AVP 6 101**
    
    오디오 스트림을 설명한다. 25146포트를 통해 RTP/AVP 프로토콜을 사용하여 전송되고 있으며, 페이로드 유형은 6 및 101이다.
    
    **2) Media Attribute (a): rtpmap:6 DVI4/16000**
    
    페이로드 유형 6인 DVI4 코덱을 사용하며, 샘플링 주파수는 16000Hz이다.
    
    **3)Media Attribute (a): rtpmap:101 telephone-event/8000**
    
    RTP 맵핑을 정의한다. 여기서는 페이로드 유형이 101인 telephone-event 코덱을 사용하며, 샘플링 주파수는 8000Hz이다.
    
    **4)Media Attribute (a): fmtp:101 0-16**
    
    페이로드 유형이 101인 telephone-event 코덱의 포맷 매개변수를 정의한다. 여기서는 0부터 16까지의 이벤트를 전달할 수 있다.
    
    **5)Media Attribute (a): sendonly**
    
    이 세션에서는 미디어를 송신만 하고고 수신하지 않는다.
    
    **6)Media Attribute (a): ptime:20**
    
    패킷의 길이를 정의하고, 여기서는 20ms로 설정되어 있다.
    

## 4-5 No.438 (ACK) 패킷

![image](https://github.com/visionn7111/SIP-SDP-protocol-example/assets/169283479/01a6acc9-cf93-47d2-ae0e-7836a8914985)


수신자의 전화기가 200 OK를 수신하였음을 확인하는 ACK를 전송한다.

CSeq 헤더 값이 1 이므로 앞의 200 OK에 대한 ACK임을 확인할 수 있다.

# 5. RTP로 전송된 오디오 스트림(No. 439 ~ No.864)

![스크린샷 2024-06-06 오후 8.10.18.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/6f3c069e-3fa7-4bc9-8021-ff524a9bbfbd/85e97830-3011-4b02-b33f-5801965d3f31/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7_2024-06-06_%EC%98%A4%ED%9B%84_8.10.18.png)

RTP를 통해 전송된 오디어 데이터를 나타낸다. 이 오디오 데이터는 DVI4 코덱을 사용하였으며, 샘플링 주파수는 전과 다른 16000Hz이다. SSRC 값은 0x43FFBA2로, 이는 동기화 소스 식별자로서 패킷을 보내는 개체를 식별한다. 즉 전화 통화가 시작 되었고 전화중을 의미한다.

# 6. SIP 호 종료 절차(No. 865 ~ N0. 866)

## 6-1 No.865 (BYE) 패킷

![image](https://github.com/visionn7111/SIP-SDP-protocol-example/assets/169283479/06c63a20-5715-4e0f-a34a-9c652d89fc6a)


BYE 세션은 발신자와 수신자 누구나 생성 할 수 있다. 전화기의 훅 스위치에 수화기를 올려 놓는 쪽에서 BYE가 전송된다. 세션 종료 절차는 새롭게 시작하는 다이얼로그이자 트랜잭션이므로 CSeq의 헤더 값이 다르다. 위 헤더의 정보로 From 헤더와 To 헤더의 값에서 알 수 있듯이 세션 종료 절차의 진행 방향이 반대로 바뀐 것을 알 수 있다 따라서 “Test”사용자가 호 종료를 진행 한 것을 볼 수 있다.

## 6-2 No.866 (200 OK) 패킷

![image](https://github.com/visionn7111/SIP-SDP-protocol-example/assets/169283479/b717be10-e827-45f3-8ae3-d690ee56746f)


200 OK 패킷은 요청이 정상적으로 성립된 것을 알리는 패킷이다. 위 CSeq를 확인하면 BYE에 대한 요청이 정상적으로 성립되었다. 즉, 전화가 종료 된것을 알 수 있다.

# 7. 요약

전체적인 Wireshark 내용을 보았을 때 흐름은 전화연결 시도(SIP 호 설립) → 통화 → 전화 종료(SIP 호 종료) → 전화연결 시도(SIP 호 설립) → 통화 → 전화 종료(SIP 호 종료)의 흐름인 것을 알 수 있다.

