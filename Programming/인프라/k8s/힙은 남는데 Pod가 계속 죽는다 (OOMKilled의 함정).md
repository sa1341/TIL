# 힙은 남는데 Pod가 계속 죽는다 (OOMKilled의 함정)

운영 중인 Pod가 하루에 열몇 번씩 재시작됩니다. `kubectl`로 보면 이유가 **OOMKilled** — 메모리 초과로 죽은 것입니다. 그런데 JVM **힙** 모니터링을 보면 512MB 중 380MB가 여유가 있습니다. **힙은 남는데 왜 메모리 초과로 죽을까요?**

## JVM이 쓰는 메모리는 힙만이 아니다

힙 외에도 **off-heap 메모리**가 있습니다.

- 메타스페이스(Metaspace)
- 스레드 스택(Thread Stack)
- 다이렉트 버퍼(Direct Buffer)
- 코드 캐시(Code Cache)

**이것들은 힙 모니터링에 안 잡힙니다.**

## 쿠버네티스는 힙이 아니라 RSS 전체를 본다

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
