
코틀린으로 프로젝트를 하는 도중에 class 타입의 객체를 Argument로 넘겨줘야하는 메서드가 있었는데, `String::class.java`로 사용하는 부분이 있었고 왜 이렇게 명시해줘야 되는지 궁금해서 알아보려고 합니다.


## 리플렉션

일단 리플렉션이란 개념을 알야야 됩니다. 리플렉션은 코드를 작성하는 시점에 런타임상 컴파일된 바이트 코드에서 내가 작성한 코드를 JVM이 어디에 위치하는지 알 수 없기 때문에 바이트코드를 이용해 내가 참조하려는 값을 찾기 위해 사용합니다.

## Java와 Kotlin에서 리플렉션 사용 방법

### Java에서 클래스를 참조할 경우

```java
Something.class // 클래스 그 자체를 리플렉션
something.getClass() // 인스턴스에서 클래스를 리플렉션
```


### Kotlin에서 클래스를 참조할 경우

```kotlin
Something::class
something::class
```

하지만 넘길 때는 `Something::class.java`와 같이 .java가 붙는데, 그 이유는 Java에서 쓰는 클래스와 코틀린에서 쓰는 클래스가 다르기 때문입니다.

Java에서의 Something.class는 Class를 리턴합니다. 반면에, Kotlin에서는 Something::class를 하면 KClass를 리턴합니다. 그렇기 때문에 KClass를 Class로 변환해야 하는데 이때 .java를 이용하여 Java 클래스 값을 받습니다.


