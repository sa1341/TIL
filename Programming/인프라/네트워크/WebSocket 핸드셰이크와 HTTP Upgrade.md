# WebSocket 핸드셰이크와 HTTP Upgrade

WebSocket 연결의 첫 줄을 보면 의외입니다. 양방향 실시간 통신이 목표인데, 왜 굳이 **HTTP 핸드셰이크부터** 거칠까요? TCP 위에 자기만의 프로토콜을 바로 올리면 되잖아요?

![WebSocket — HTTP Upgrade 핸드셰이크 후 양방향 전환|697](../../../Attached%20file/net_websocket_upgrade.svg)

## 두 가지 벽

WebSocket 이전엔 **롱 폴링(long polling)**으로 실시간을 흉내 냈습니다. 그럼 TCP 위에 새 프로토콜을 그냥 만들면 되지 않을까요? 두 가지 벽이 있습니다.

1. **인터넷 인프라가 사실상 HTTP만 통과시킨다.** 사용자 PC에서 서버까지 가는 길에는 수많은 중간 장비(프록시·방화벽)가 있습니다. 새 프로토콜을 만들어도 이 장비들을 못 뚫으면 의미가 없죠. **HTTP로 시작하는 게 가장 확실한 통과 방법**입니다.
2. **HTTP 인프라를 그대로 재사용할 수 있다.** WebSocket이 인증을 새로 정의했다면 모든 게 처음부터였을 겁니다. HTTP로 시작하니 **기존 웹의 보안·라우팅 자산을 그대로** 가져다 씁니다.

핸드셰이크가 끝나면(`Upgrade: websocket`) 그제야 본색을 드러내 양방향 통신으로 전환합니다.

## 그런데 단방향이면 과하다 — SSE

- **단방향 알림** 같은 케이스에선 WebSocket이 과한 선택이 되고 있습니다.
- 채팅처럼 진짜 양방향이 필요한 게 아니라, **서버가 밀어주는 알림 정도면 SSE(Server-Sent Events) 하나로 충분**합니다.
- **HTTP/2의 멀티플렉싱 + SSE** 조합이면, 별도의 풀 듀플렉스 프로토콜 없이 대부분의 푸시 시나리오가 해결됩니다.

## 역설

> 역설적이게도, **HTTP를 통과하려고 HTTP를 빌렸던 WebSocket**이, 그 HTTP가 진화(HTTP/2 + SSE)하면서 **단방향 영역을 SSE에 내주고** 있습니다.

## 정리

> WebSocket이 증명한 건 하나입니다 — **새 프로토콜이 살아남으려면, 인터넷이 이미 통과시켜 주는 모양(HTTP)으로 시작해야 한다.** 그 모양에 너무 잘 맞춘 탓에, HTTP가 발전할수록 자기 영역을 다시 그려야 하는 것이죠.

관련 노트: [[HTTP 버전별 진화와 HOL 블로킹]]

> 출처: 2분코딩 — "Why does WebSocket start with HTTP when its goal is bidirectional communication? (WebSocket design)" · https://www.youtube.com/shorts/0UfTQsHHaQs
