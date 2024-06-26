
현재 실무에서 모니터링을 `프로메테우스 + 그라파나`를 연동해서 인사이트를 얻고 있는데, 처음부터 제가 스스로 구축한게 아니여서 이번 기회에 각 매트릭 정보에 대해서 공부할 겸 스프링 부트 프로젝트를 만들어서 연동하여 어떻게 그래프로 매트릭 정보를 보여주는지 확인해 봤습니다.

## 프로메테우스 설치

아래 URL을 통해서 현재 OS에 맞는 설치버전을 다운로드 받고, 압축을 풀면 됩니다.

[Prometheus 설치](https://prometheus.io/download/)

## Spring Boot Prometheus dependecy 추가

아래에 spring boot actuator랑 매트릭을 프로메테우스가 수집할 수 있는 포맷으로 변환해주는 마이크로미터 구현체인 `micrometer-registry-prometheus`도 함께 dependency에 선언해 줍니다.

```kotlin
    implementation("org.springframework.boot:spring-boot-starter-actuator")
    implementation("io.micrometer:micrometer-registry-prometheus")
```

실제 실무에서는 `actuator`를 외부에 노출하지 않고 스프링 시큐리티를 통해서 basic auth 설정을 하지만, 예제이기 때문에 시큐리티 없이 설정했습니다.

### application.yml 설정

spring boot acutator에 prometheus가 수집할 수 있는 엔드포인트를 활성화 시킵니다.

```kotlin
management:
  endpoints:
    web:
      exposure:
        include: prometheus
```



### prometheus.yml 설정

```
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:9090"]
  
  - job_name: "spring-actuator"
    metrics_path: '/actuator/prometheus'
    scrape_interval: 1s
    static_configs:
      - targets: ['localhost:8083']
```

맨 막줄 라인에 로컬 스프링 부트 서비스랑 연동한 설정 값을 추가 했습니다. 크게 아래 설정 값 4가지가 있습니다.

- job_name: 수집하는 이름
- metrics_path: 수집할 경로
- scrape_interval: 수집할 주기
- targets: 수집할 서버의 IP, PORT를 지정

`scrape_interval`을 1s로 설정했지만, 실제 실무에서는 서비스에 많은 부하를 줄 수 있기 때문에 운영에서는 `10s ~ 1m`정도를 권장합니다.

이제 설정이 끝났으면 프로메테우스 압축헤제한 디렉토리 경로로 접속 후 `./prometheus`를 실행하면 default로 9090 포트로 접속 후에 

상단의 Status -> Targets으로 들어가서 실제 로컬에서 띄운 스프링부트 서비스를 프로메테우스가 정상적으로 수집하는지 확인할 수 있습니다.


![image](https://user-images.githubusercontent.com/22395934/226826618-9b202e7d-538f-416e-af7d-f74ec8a9aa85.png)

`UP`으로 보여지면 잘 붙은걸로 확인 할 수 있습니다.


## 프로메테우스 검색 기능

프로메테우스가 제공하는 기본 검색기능을 사용하여 초 단위로 서비스 요청 수를 확인할 수 있습니다.

![image](https://user-images.githubusercontent.com/22395934/226828192-95a9d142-3a88-413f-83d0-fe97344f33dd.png)

또한 매트릭 정보를 구분하기 위해서 태그를 사용할 수 있는데 마이크로미터에서는 태그라고 부르지만, 프로메테우스에서는 레이블(Label)이라고 부르고 있습니다.

```
ex) 

http_server_requests_seconds_count{uri!="/actuator/prometheus"}
```

### 레이블 일치 연산자

- =: 제공된 문자열과 정확히 동일한 레이블 선택
- !=: 제공된 문자열과 같지 않은 레이블 선택
- =~: 제공된 문자열과 정규식 일치하는 레이블 선택
- !~: 제공된 문자열과 정규식 일치하지 않는 레이블 선택

예를 들어서 method가 `GET, POST`인 경우를 포함해서 필터를 걸고 싶은 경우에는 아래와 같이 레이블 일치 연산자를 사용할 수 있습니다.

```
http_server_requests_seconds_count{method=~"GET|POST"}
```

또한 `/actuator`로 시작하는 uri는 제외한 조건으로 필터를 걸 수 있습니다.

```
http_server_requests_seconds_count{uri!~"/actuator.*"}
```

### 연산자 쿼리와 함수

1. sum
- 값의 합계를 구합니다.

ex) sum(http_server_requests_seconds_count)

2. sum by
- SQL의 group by 기능과 유사합니다.

ex) sum by(method, status)(http_server_requests_seconds_count)

```
{method="GET", status="404"} 3
{method="GET", status="200"} 120
```

3. count
- 메트릭 자체의 수 카운트

ex) count(http_server_requests_seconds_count)

4. topk
- 상위 3개 메트릭 조회

ex) topk(3, http_server_requests_seconds_count)

5. 오프셋 수정자
- 현재를 기준으로 특정 과거 시점의 데이터를 반환합니다.

ex) http_server_requests_seconds_count offset 10m

6. 범위 벡터 선택기
- 마지막에 [1m],[60s]와 같이 표현합니다. 지난 1분간의 모든 기록값을 선택합니다. 

ex) http_server_requests_seconds_count[1m]

> 참고로 범위 벡터 선택기는 차트에 바로 표현할 수 없습니다. 데이터로만 확인이 가능합니다.

## 프로메테우스 게이지와 카운터

메트릭은 크게 보면 게이지와 카운터 2가지로 분류 가능합니다.

- 게이지(Gauge): 임의로 오르내릴 수 있는 값

ex) CPU 사용량, 메모리 사용량, 사용중인 커넥션

- 카운터(Counter): 단순하게 증가하는 단일 누적 값

ex) HTTP 요청 수, 로그 발생 수 

게이지는 오르락 내리락 하는 값이고, 카운터는 특정 이벤트가 발생할 때마다 그 수를 계속 누적하는 값입니다.

![image](https://user-images.githubusercontent.com/22395934/226830954-5113e19d-a9b2-4782-8b02-77646c39fb42.png)

위 그래프는 CPU 사용량을 보여주는 게이지입니다. 과거부터 지금까지의 CPU 사용량을 확인할 수 있고, 오르락 내리락 하는걸 알 수 있습니다.

아래 그래프는 특정 uri에 대한 카운터를 그래프로 보여주고 있습니다. 카운터는 계속 누적해서 증가하는 값입니다. 이렇게 증가만 하는 그래프에서는 특정 시간에 얼마나 고객의 요청이 들어왔는지 인사이트를 얻기가 힘듭니다. 이런 문제를 해결하기 위해서는 프로메테우스에서는 `increase(), rate()` 같은 함수를 지원하고 있습니다.

![image](https://user-images.githubusercontent.com/22395934/226831369-4f2f6f65-c131-4de1-ab45-0d1d09ddbbac.png)


### increase()

increase()를 사용하면 지정한 시간 단위별로 증가를 확인할 수 있습니다. 마지막에 [시간]을 사용해서 범위 벡터를 선택해야 합니다.

```
increase(http_server_requests_seconds_count{uri="/api/v1/date-type"}[1m])
```

![image](https://user-images.githubusercontent.com/22395934/226831956-fc6456cb-aff4-45d1-ab8f-97e0f944f836.png)

increase() 함수를 적용하니 실제로 분당 얼마나 고객의 요청이 어느정도 증가했는지 한눈에 파악할 수 있습니다.

### rate()

범위 벡터에서 초당 평균 증가율을 계산합니다.
increase()가 숫자를 직접 카운트 한다면, rate()는 여기에 초당 평균을 나누어 계산합니다.

ex) rate(data[1m])에서 [1m]이라고 하면 60초가 기준이 되므로 60을 나눈 수입니다.

> 핀트만 말하자면 초당 얼마나 증가하는지 나타내는 지표로 보면 됩니다.

### irate()

rate와 유사하지만, 범위 벡터에서 초당 순간 증가율을 계산합니다. 급격하게 증가한 내용을 확인하기 좋습니다.

