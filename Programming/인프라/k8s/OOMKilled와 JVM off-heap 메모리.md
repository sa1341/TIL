# OOMKilled와 JVM off-heap 메모리

운영 중인 Pod가 하루에 열몇 번씩 재시작됩니다. `kubectl`로 보면 이유가 **OOMKilled** — 메모리 초과로 죽은 것입니다. 그런데 JVM **힙** 모니터링을 보면 512MB 중 380MB나 남아 있습니다. **힙은 남는데 왜 메모리 초과로 죽을까요?**

![OOMKilled — 힙 모니터링 vs 컨테이너 RSS(off-heap 포함)|697](../../../Attached%20file/k8s_oomkilled_memory.svg)

## JVM이 쓰는 메모리는 힙만이 아니다

힙 외에도 **off-heap 메모리**가 있습니다.

- 메타스페이스(Metaspace)
- 스레드 스택(Thread Stack)
- 다이렉트 버퍼(Direct Buffer)
- 코드 캐시(Code Cache)

**이것들은 힙 모니터링에 안 잡힙니다.**

## off-heap 영역별 용도

각 영역이 실제로 무엇에 쓰이고, 무엇이 그 크기를 키우는지 알아야 어디를 제한할지 판단할 수 있습니다.

![컨테이너 RSS 구성 — 힙 + off-heap 영역별 용도|697](../../../Attached%20file/k8s_jvm_offheap_regions.svg)

- **메타스페이스(Metaspace):** 로드된 **클래스의 메타데이터**(구조·메서드·상수 풀)를 담습니다. 스프링·하이버네이트의 동적 프록시, 클래스로더를 반복 생성하는 코드가 늘리며, **클래스로더 누수**의 대표 증상입니다. → `-XX:MaxMetaspaceSize`
- **스레드 스택(Thread Stack):** 스레드마다 하나씩 갖는 **호출 스택**(지역 변수·메서드 프레임). 크기는 **스레드 수 × 스택 크기**라, 스레드 1000개 × 1MB면 그것만 1GB입니다. 톰캣·커넥션 풀·`@Async` 풀이 위험 지점. → `-Xss` + **스레드 수 자체를 통제**
- **코드 캐시(Code Cache):** **JIT 컴파일러가 만든 네이티브 기계어**. 꽉 차면 JIT이 멈춰 성능이 급락합니다. → `-XX:ReservedCodeCacheSize`
- **다이렉트 버퍼(Direct Buffer):** `allocateDirect()`로 잡는 **힙 밖 I/O 버퍼**(zero-copy). **Netty·gRPC·Kafka 클라이언트** 등 NIO 라이브러리가 쓰며, GC로만 회수돼 해제가 늦어 쌓이기 쉽습니다. → `-XX:MaxDirectMemorySize`
- **GC·네이티브(그 외):** 나열엔 없지만 RSS를 차지합니다 — GC 구조체(카드 테이블·마킹 비트맵, **힙 크기에 비례**), JNI/네이티브 라이브러리, glibc **malloc 아레나**(RSS가 스멀스멀 부푸는 고전 문제, `MALLOC_ARENA_MAX`나 **jemalloc**으로 완화).

> 실측은 추측하지 말고 **NMT**로: `-XX:NativeMemoryTracking=summary`를 켜고 `jcmd <pid> VM.native_memory summary`를 보면 영역별(Class/Thread/Code/GC…) 실제 사용량이 바이트 단위로 나옵니다.

## 쿠버네티스는 힙이 아니라 RSS 전체를 본다

> **RSS(Resident Set Size)**란 프로세스가 **실제로 물리 메모리(RAM)에 올려 두고 쓰는 상주 메모리**의 크기입니다. 힙뿐 아니라 off-heap, 코드, 매핑된 라이브러리까지 **RAM에 실제 올라온 것 전부**를 포함하고, 디스크로 스왑된 페이지는 빠집니다. 컨테이너의 메모리 limit은 바로 이 RSS(정확히는 cgroup이 집계하는 working set)를 기준으로 판정합니다.

- 쿠버네티스는 **컨테이너 전체의 RSS 메모리**를 감시합니다. JVM 힙이 아니라 **프로세스가 실제로 쓰는 메모리 전체**입니다.
- 힙은 여유 있어도, **off-heap을 합치면 limit에 걸리는** 것이죠.
- limit을 넘는 순간 **커널이 프로세스를 즉시 강제 종료**합니다. 로그도 없이 그냥 죽습니다.

## 그럼 limit을 넉넉히 잡으면?

2GB, 3GB로 올리면 될까요? **limit을 과하게 잡으면** 노드의 메모리를 과다 점유해서, **한 Pod의 메모리 급증이 같은 노드의 다른 Pod까지 쫓아냅니다**(eviction).

그래서 JVM에서는 **off-heap을 직접 제한하는 것**이 정답입니다. (`-XX:MaxMetaspaceSize`, `-XX:MaxDirectMemorySize`, `-Xss` 등)

## 정리

> OOMKilled는 **메모리가 부족해서 생기는 게 아니라, "보이지 않는 메모리(off-heap)"를 계산하지 않아서** 생깁니다. 힙 모니터링만으로는 컨테이너의 진짜 메모리 사용량을 알 수 없습니다.

관련 노트: [[JVM 성능과 최적화]]

> 출처: 2분코딩 — "Pod Keeps Restarting Despite Plenty of JVM Heap (The Trap of OOMKilled)" · https://www.youtube.com/shorts/35DPn-t_87g
