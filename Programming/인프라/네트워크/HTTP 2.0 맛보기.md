
HTTP 1.1 버전이 나온지 벌써 20년이 넘었습니다.

현재의 웹은 멀티미디어 리소스를 처리해야하고 웹 페이지 하나를 구성하기 위해 다수의 비동기 요청이 발생되고 있는데, 이를 처리하기엔 HTTP 1.1 스펙은 너무 느리고 비효율적입니다.

## HTTP/1.1 동작방식

HTTP/1.1은 기본적으로 Connection당 하나의 요청을 처리하도록 설계되었습니다. 그래서 동시전송이 불가능하고 요청과 응답이 순차적으로 이루어지게 됩니다. 

그렇다보니 HTTP 문서안에 포함된 다수의 리소스를 처리하려면 요청할 `리소스(html, image, css, js등)`의 개수에 비례해서 Latency(대기시간)이 길어질수 밖에 없습니다.

## HOL(Head Of Line) Blocking 

Web환경에서 HOLB는 실제로 두 종류가 있지만 그중에서 HTTP의 HOLB를 알아봤습니다.

HTTP/1.1의 connection당 하나의 요청처리를 개선할 수 있는 기법 중 pipelining이 존재하는데 이것은 하나의 connection을 통해서 다수개의 파일을 요청/응답 받을 수 있는 기법입니다.

이 기법을 통해서 어느정도의 성능 향상을 꾀 할 수 있으나 큰 문제점이 있습니다. 

만약 하나의 TCP 통신에서 3개의 이미지(a.png, b.png, c.png)를 순차적으로 얻으려고 하는 경우 첫번째 이미지인 a.png 요청이 지연되면 두번째, 세번째 이미지의 응답처리가 첫번째 응답처리가 완료되기 전까지 지연되며 이와 같은 현상을 HTTP의 `Header of Line Blocking`이라 부르며 파이프 라이닝의 큰 문제점 중 하나입니다.


a[href='red'] {
    color: red;
    pointer-events: none;
    cursor: default;
    text-decoration: none;
}

<a href="https://www.google.com/" style="color: red;">custom link</a>



<a href="red">Look, ma! Red!</a>

[Look, ma! Red!](red)

[주소에 대한 설명](http://www.google.co.kr)

<span style="color:red">[주소에 대한 설명]'(http://www.google.co.kr)'</span>

<span style="color:red">빨간색</span>

<span style="color:blue">파란 색</span>