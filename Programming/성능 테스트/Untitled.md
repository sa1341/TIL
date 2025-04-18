Java 21+ 가상 스레드를 이용한 성능 테스트 

JDK 24 Release 이후 플랫폼 스레드 vs 가상 스레드의 성능 테스트를 위해서 오랜만에 스프링 부트에서 간단하게 API를 구현하여 테스트를 해봤습니다.

실제 성능 테스트는 코드 기반으로 작성하였고, gatling이라는 gradle 플러그인을 사용하여 task로 실행하였습니다.

스프링 부트 3.2 이후부터는 application.yml 파일에서 spring.threads.virtual.enabled 설정을 지원하고 있어서 플랫폼 스레드와 가상 스레드로 스위칭 변경이 용이해서 좋았습니다.


