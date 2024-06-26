
## HTTP와 TCP/IP의 차이점

업무를 하면서 HTTP 통신으로 데이터를 주고 받는 Restful API를 자주 작성하는데 문득 TCP/IP와 HTTP의 차이점이 무엇인지 생각이 나서 구글링을 해보았습니다. 역시 웹 상에서 빡고수분들이 너무 잘 정리를 해주어서 이해가 잘 되도록 정리를 해보았습니다.

일단, 대학교 시절에 OSI 7계층에 대해서는 동해물과 백두산이 마르도록 들었고, 시험을 위해 외웠던 기억이 있었습니다. OSI 7계층에서 TCP/IP는 4계층 `(TCP는 4계층, IP는 3계층에 속합니다.)` 요즘은 웹 서비스에 맞게 단순화 시켜서 TCP/IP 4계층이 존재합니다. 

HTTP는 최상위 계층인 application 계층에 동작합니다. 개념적으로만 봤을 때 HTTP는 TCP/IP 계층 위에 동작하는 거라고 볼 수 있습니다. 

> 참고: IP 계층에는 출발지 주소와 목적지 주소가 존재하고, TCP 계층에는 출발지 포트, 목적지 포트가 존재합니다. IP가 아파트 주소라면 TCP는 번지, 동 개념이라고 생각하면 편합니다. 

- 데이터 형태
    - tcp: byte Array로 정보를 통신
    - http: String으로 정보를 통신

예를 들어서 클라이언트로부터 특정 URL로 요청이 들어오면 DNS 서버가 도메인에 매핑되는 IP 주소를 받아옵니다. TCP 계층에서 HTTP 메시지를 패킷으로 분해합니다. 그리고 IP 계층에서 전송 위치를 확인하고 네트워크를 통하여 전송합니다. 그리고 받는쪽은 위의 과정을 역순으로 진행하여 처리합니다.

추가로 소켓도 마찬가지로 TCP 기반으로 나온건데 HTTP와의 차이점은 연결지향 / 동기식 통신이 필요할 때는 소켓 통신을 이용하는게 더 유리하다는 점입니다.
