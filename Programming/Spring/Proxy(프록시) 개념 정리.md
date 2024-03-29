
## 프록시란?

프록시(Proxy)라는 뜻으로 자바에서 정말 많이 나오는 개념입니다. 물론 이전에도 Spring AOP 개념에 대해서 살펴볼때 설명하였지만, 그때는 대충 개념에 대해서만 알았지 내부적으로 어떤식으로 동작하는지는 이해할 수 없었습니다.

프록시는 실제로 액션을 취하는 객체를 대신해서 대리자 역할을 합니다. 한마디로 실제 호출해야하는 메서드를 가지고 있는 실제 대상을 감싸고 있는 Wrapping 클래스라고 생각하시면 됩니다. 

프록시 패턴을 사용하게 되면 프록시 단계에서 권한을 부여할 수 있는 이점이 생기고 필요에 따라 객체를 생성시키거나 사용하기 때문에 메모리를 절약할 수 있는 이점도 생깁니다. 프록시 패턴이 하는 일은 한마디로 자신이 보호하고 있는 객체에 대한 엑세스 권한을 제어하는 것입니다.

스프링 AOP에서 공통의 기능을 작성할 때 프록시를 이용해서 구현을 하고, 주로 클라이언트에서 타겟의 메소드를 호출할 때 선처리를 하느냐... 후처리를 하느냐에 중점을 두었습니다. 이번에 포스팅 할 내용에서는 간단하게 타겟의 메소드를 호출할 때 프록시를 이용해서 기존의 타겟의 기능에 추가적으로 살을 덧붙이는 예제 코드를 작성해보았습니다.

일단 프록시 예제 코드는 세 부분의 클래스로 나누어져 있습니다. 첫 번째는 프록시를 관리하는 클래스인 `ProxyField 객체`, 두 번째는 프록시가 구현하고 있는 `인터페이스`, 세 번째로는 클라이언트에서 타겟의 메소드를 호출할 때 프록시에서 내부적으로 호출하는 `InvocationHandler의 구현체` 입니다.

가장 먼저, 타겟이 구현하고 있는 인터페이스를 작성하였습니다. 이 인터페이스는 간단하게 name이라는 String 타입의 파라미터를 던져주면 "Hello" + name이라는 문자열을 리턴하는 메소드를 가지고 있습니다. 하나만 있으면 재미없으니 "Hi" + name이라는 문자열도 리턴하는 메소드도 선언하였습니다. 여기서 정말 말도 안돼는 억지이지만... 문자열에 추가적으로 + 씨라고 살을 덧 붙여주도록 프록시 객체를 호출하는 코드를 작성하였습니다.

#### 타겟이 구현하는 인터페이스

```java
public interface Hello {

    public String sayHello(String name);
    public String sayHi(String name);
    
}
```

#### 클라이언트가 호출하는 실제 타겟

```java
public class HelloTarget implements Hello {

    @Override
    public String sayHello(String name) {
        return "hello!" + name;
    }

    @Override
    public String sayHi(String name) {
        return "hi!" + name;
    }
}
```

이제 가장 중요한 프록시 객체를 가지고 있는 **ProxyField** 클래스를 작성하였습니다.

```java
import java.lang.reflect.Proxy;

public class ProxyField {

    private Hello proxyHello = (Hello) Proxy.newProxyInstance(
            getClass().getClassLoader(),
            new Class[] { Hello.class },
            new TargetHandler(new HelloTarget())
    );

    public Hello getProxyHello(){
        return proxyHello;
    }

}
```

여기서 proxyHello는 Hello라는 타겟의 인터페이스를 구현하는 Proxy입니다. 자바에서는 객체에 대한 정확한 정보를 자세히 알지 못하여도 해당 객체의 메소드나 필드에 대한 정확한 정보를 제공하여 우회적으로 객체를 핸들링 할 수 있도록 reflection이라는 API를 제공하고 있습니다. 

프록시를 생성할 때 reflect 패키지에서 제공하는 Proxy 클래스의 newProxyInstance() 메소드를 호출하여 프록시를 생성합니다. 이 때 파라미터로 3개를 메소드에 넘겨주는데 첫 번째 인자로는 ProxyField 클래스를 로드하는 클래스 로더 객체를 넘겨줍니다. 

클래스 로더는 클래스 패스에 존재하는 클래스들을 JVM이 관리하는 RuntimeDataArea 중 하나인 class 영역에 클래스 파일을 적재하는 역할을 합니다. 즉 프록시 클래스를 로드 할 클래스 로더라고 생각하시면 됩니다. 두 번째로는 프록시가 구현할 인터페이스 대상인데 여기서 Class 배열로 [ 인터페이스.class ]로 넘겨주면 됩니다. 

마지막으로 실제 클라이언트에서 타겟을 호출할 때 프록시 내부적으로 호출되는 InvocationHandler의 구현체인 TargetHandler를 넘겨주는데 이 TargetHandler 객체는 `HelloTarget(실제 호출 대상)`을 간접적으로 호출하면서 추가적으로 살을 덧 붙여 주는 역할을 하는 객체라고 생각하시면 됩니다. 

## newProxyInstance() 메소드 파라미터 목록

- 클래스 로더 (프록시 클래스를 로드하는 클래스 로더 객체)
- 구현할 인터페이스 (Class[])
- InvocationHandler 구현체

#### InvocationHandler 구현체

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

public class TargetHandler implements InvocationHandler {

    // 실제 클라이언트에서 호출하는 타겟 객체
    private Hello target;

    public TargetHandler(Hello target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

        // 프록시 객체를 통해 들어온 메소드 정보를 넘겨줍니다.
        if(method.getName().equals("sayHello")){
            String result = (String) method.invoke(target, args);
            // 타겟이 리턴한 결과 값에 씨를 추가하여 리턴하도록 하였습니다.
            return result + "씨";
        }


        if(method.getName().equals("sayHi")){
            String result = (String) method.invoke(target, args);
            return result + "씨";
        }

        // 위의 두 메소드가 아닐 경우 리턴 값 정의
        return "Hello! 이름없는 사나이씨";
    }

}
```

위에서 프록시를 생성할 때 세 번째 파라미터로 InvocationHandler의 구현체를 넘겨주었는데 이때 생성자로 타겟을 받습니다. 이 타겟을 받는 이유는 InvocationHandler의 역할은 프록시 객체가 클라이언트로부터 타겟 호출에 대한 요청을 가로채어(intercept) 간접적으로 타겟을 호출하게 되는데 이 역할을 InvocationHandler 구현체가 수행하게 됩니다. 여기서 추가적으로 타겟이 리턴한 결과 값을 목적에 맞게 살을 덧 붙일 수도 있는 것입니다.


```java
 @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        
    // 타겟 호출 결과 후 추가 작업 수행
}
```

이 메소드에서 받는 첫 번째 파라미터는 프록시 자기 자신을 매개 변수로 넘겨줍니다. 두 번째 매개변수 method는 프록시 객체를 통해 들어온 메소드 정보를 넘겨주며, 마지막 매개변수인 args는 메소드를 호출하는데 필요한 매개변수들을 의미합니다. 여기서는 생성자로 받은 인스턴스 맴버 변수인 target 변수를 invoke() 메소드의 파라미터로 넣어주어서 실제 타겟의 메소드를 호출하도록 구현했습니다.

메소드는 문자열을 기준으로 어떤 메소드를 호출하느냐에 따라서 분기처리가 가능합니다.


>참고로 스프링에서 AOP에 대해서 공부하신분들은 알시겠지만 Proxy를 생성하는 방식에는 대표적으로 두 가지 방식이 있는데, 이전 포스팅에서 설명했었지만 인터페이스를 구현하여 생성하는 JDK Dynamic Proxy와 클래스를 상속하여 생성하는 CGLIB Proxy 방식이 존재합니다. 스프링에서는 기본적으로 CGLIB 방식으로 프록시를 생성합니다.


#### 테스트 수행 결과


![image](https://user-images.githubusercontent.com/22395934/74549847-ba251280-4f93-11ea-98fc-cfc40789b2b2.png)


#### 참고: https://meaownworld.tistory.com/92