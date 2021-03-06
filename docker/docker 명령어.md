## 도커란?

도커(Docker)는 리눅스 컨테이너에 여러 기능을 추가해서 어플리케이션을 컨테이너로 쉽게 사용할 수 있게 해주는 프로젝트로 Go 언어로 만들어졌습니다.

## 가상 머신(Virtual Machine) VS 도커 컨테이너(Docker Container)
기존의 가상화 기술은 가상 머신(VM)이라하여 하이퍼바이저(Hypervisor)를 이용해 여러 개의 운영체제를 하나의 호스트에서 생성해 사용하는 방식이었습니다. 
이 때 생성되고 관리되는 운영체제는 게스트 운영체제(Guest OS)라고 하며, 각 게스트 운영체제는 독립된 공간과 시스템 자원을 할당받아 사용합니다. 
대표적인 툴로는 VirtualBox, VM Ware가 있습니다.
하지만 가상 머신은 게스트 운영체제(Guest OS)를 사용하기 위해 커널과 라이브러리 등이 포함이 되기 때문에 무겁고 용량이 큽니다. 
대신에 도커 컨테이는 가상화 공간을 만들기 위해 리눅스의 자체 기능인 chroot, namespace, cgroup을 사용하기 때문에 성능 손실이 거의 없으며,
컨테이너에 필요한 커널은 호스트 운영체제의 커널을 공유해 사용 하기 때문에, 가볍습니다.


## 도커 명령어 옵션

![image](https://user-images.githubusercontent.com/22395934/89712235-85d26200-d9ca-11ea-80d5-5e7d64ac9d74.png)


ex)
- docker run -d -p 1234:6379 redis
- -p 옵션을 이용: 호스트의 1234포트를 컨테이너의 6379포트로 연결, localhost의 1234포트로 접속하면 하면 redis를 사용 가능



#### 참조: http://pyrasis.com/Docker/Docker-HOWTO#search
