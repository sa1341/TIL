# @Transactional 롤백 규칙 (Checked vs Unchecked)

스프링 개발자라면 한 번쯤 겪는 함정이 있습니다. **분명히 예외를 던졌는데 데이터가 그대로 들어가 있습니다.** 예외가 위로 올라갔는데도 `repository.save`로 INSERT한 주문은 DB에 그대로 남아 있죠. **트랜잭션이 롤백을 안 한** 것입니다.

![@Transactional 롤백 규칙 — Checked vs Unchecked Exception|697](../../Attached%20file/spring_transactional_rollback.svg)

## 왜 어떤 예외는 롤백되고, 어떤 예외는 안 될까

답을 보려면 **자바의 예외 철학**으로 돌아가야 합니다. 자바에는 두 종류의 예외가 있습니다.

- **Checked Exception** — 예측 가능한 **외부 실패** (복구 가능)
- **Unchecked Exception (RuntimeException)** — **코드가 잘못된 결과** (복구 불가능)

단순히 `throws`에 적느냐 마느냐의 차이가 아니라, 자바 설계자들이 **둘에 다른 의미**를 부여한 것입니다. 컴파일러가 Checked에만 try-catch를 강제하는 것도 같은 맥락이죠.

스프링은 이 철학을 그대로 받아들였습니다. **복구할 수 있는 예외(Checked)에는 트랜잭션을 살려 두고, 복구 불가능한 예외(Unchecked)에만 롤백을 자동으로** 거는 것입니다.

## 그런데 실무에선 이 가정이 안 맞는다

대부분의 코드에서 `IOException`, `SQLException`은 그 자리에서 복구하지 않습니다. **그냥 위로 던지면서 트랜잭션도 같이 롤백되길 기대**합니다. 그래서 백엔드 코드에는 이런 한 줄이 거의 관습처럼 박혀 있습니다.

```java
@Transactional(rollbackFor = Exception.class)
```

**Checked든 Unchecked든 다 롤백해 달라**는 요청 — 자바 예외 철학과 스프링의 일관성을 깨고 **현실에 맞춰 다시 정의**하는 것입니다.

## 이건 개발자의 무지가 아니다

자바 자체가 **Checked Exception에 회의적인 시대**로 가고 있습니다.

- **코틀린은 Checked Exception을 아예 없앴고**,
- 모던 자바 코드도 **RuntimeException으로 감싸 던지는 패턴**이 일반적입니다.

## 정리

> `@Transactional`의 기본 동작(RuntimeException만 롤백)은 **1990년대 자바 설계자들의 예외 철학을 정확히 반영**하고 있습니다. 우리는 **30년 전의 결정을 어노테이션 한 줄로 매일 만지고** 있는 셈입니다.

관련 노트: [[스프링 핵심 원리 고급편 정리]]

> 출처: 2분코딩 — "Why does @Transactional only roll back on RuntimeException? (Transaction Design)" · https://www.youtube.com/shorts/L3IFezsV5VI
