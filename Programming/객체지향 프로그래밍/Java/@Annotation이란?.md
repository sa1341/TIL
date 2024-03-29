# 어노테이션이란?

스프링을 사용하여 코드를 작성하다 보면 @로 시작하는 어노테이션들을 자주 보게 되었습니다. 주로 lombok을 사용하여 @Getter, @Setter를 특정 클래스 위에 기술하여 클래스 파일 생성시 getter, setter를 알아서 자동으로 만들어주는 아주 편리한 녀석으로만 생각했었습니다.

부끄럽지만 Java 1.5부터 제공하는 어노테이션에 대해서 예제를 통해서 정확하게 무엇을 하고 어떻게 Customizing 해서 사용하는지 찾아보았습니다.

## 1. 어노테이션의 개념

어노테이션(Annotation)은 메타데이터(metadata)라고 볼 수 있습니다. 메타데이터는 Application이 처리하는 데이터가 아니고, 컴파일 과정과 런타임 과정에서 코드를 어떻게 컴파일하고 처리할것인지를 알려주는 정보입니다. 어노테이션은 다음과 같은 형태로 작성됩니다.

> @AnnotationName

어노테이션은 다음 세가지 용도로 사용됩니다.

>
- 컴파일러에게 코드 문법 에러를 체크하도록 정보를 제공 함.
- 소프트웨어 개발 툴이 빌드나 배치 시 코드를 자동으로 생성할 수 있도록 정보를 제공 함.
- 실행 시(런타임 시) 특정 기능을 실행하도록 정보를 제공 함.

첫번째로 컴파일러에게 코드 문법 에러를 체크하도록 정보를 제공하는 대표적인 예는 @Override 어노테이션 입니다. 

예를 들어서 부모클래스의 메소드를 재정의하여 사용할때 컴파일 시 상위타입(부모클래스, 인터페이스)에 해당 메소드가 존재하는지 확인하고 만약 존재하지 않는다면 컴파일 에러를 발생시킵니다. 어노테이션은 빌드 시 자동으로 XML 설정 파일을 생성하거나, 배포를 위해 JAR 압축 파일을 생성하는데에도 사용됩니다. 그리고 실행 시 클래스의 역할을 정의하기도 합니다.

## 2. 어노테이션의 정의

어노테이션 타입을 정의하는 방법은 인터페이스를 정의하는 것과 유사합니다.

```java
public @interface AnnotationName {

}
```

이렇게 정의한 어노테이션은 코드에서 다음과 같이 사용합니다.

```java
@AnnotationName
```

어노테이션은 클래스의 필드처럼 엘리먼트라는 녀석을 맴버로 가질 수 있습니다. 각 엘리먼트는 타입과 이름으로 구성되며, 디폴트 값을 가질 수 있습니다. 엘리먼트 타입으로는 int나 double과 같은 원시 타입이나 String, Class 타입, 그리고 이들의 배열 타입을 사용할 수 있습니다.

```java
public @interface AnnotationName{
    // 엘리먼트 선언
    String elementName1();       
    int elementName2() default 5;
}
```

이렇게 정의한 어노테이션을 코드에서 적용할 때에는 다음과 같이 기술합니다.

```java
@Annotation(elementName1 = "값", elementName2 = 3);
또는
@AnnotationName(elementName1 = "값");
```

elementName1은 디폴트 값이 없기 때문에 반드시 값을 기술해야하고, elementName2는 디폴트 값이 있기 때문에 생략 가능합니다. 
어노테이션은 기본 엘리먼트인 value를 가질 수 있습니다.

value 엘리먼트를 가진 어노테이션을 코드에서 적용할 때에는 다음과 같이 값만 기술할 수 있습니다.

이 값은 기본 엘리먼트인 value 값으로 자동 설정됩니다.

```java
@Annotation("값");
```

만약 value 엘리먼트와 다른 엘리먼트의 값을 동시에 주고 싶다면 다음과 같이 정상적인 방법으로 지정하면 됩니다.

```java
@AnnotationName(value = "값",  elementName = 3);
```

## 3. 어노테이션 적용 대상

| <center>ElementType 열거상수</center> | 적용 대상 |
|:--------------------:|:------------------:|
| TYPE | 클래스, 인터페이스, 열거타입 |
| ANNOTATION_TYPE | 어노테이션 |
| FIELD | 필드 |
| CONSTRUCTOR | 생성자 |
| METHOD | 메소드 |
| LOCAL_VARIABLE | 로컬변수 |
| PACKAGE | 패키지 |


### @Target

어노테이션이 적용될 대상을 지정할 때에는 @Target 어노테이션을 사용합니다. @Target의 기본 엘리먼트인 value는 ElementType 배열을 값으로 가진다. 이것은 어노테이션이 적용될 대상을 복수개로 지정하기 위해서 입니다.

```java
@Target({ElementType.TYPE, ElementType.FIELD, ElementType.METHOD})
public @interface AnnotatiionName{

}
```

### 어노테이션 유지 정책

어노테이션 정의할 때에는 한 가지 더 추가해야 할 내용은 사용 용도에 따라 @AnnotationName을 어느 범위까지 유지할 것인지 지정해야 합니다. 쉽게 설명하면 소스상에만 유지할 건지, 컴파일된 클래스까지 유지할 건지, 런타임 시에도 유지할 건지를 지정해야 합니다. 어노테이션 유지 정책은 java.lang.annotation.RetentionPolicy 열거 상수로 다음과 같이 정의되어 있습니다.

| <center>RetentionPolicy 열거상수</center> | 설명 |
|:--------------------:|:------------------|
| SOURCE | 소스상에서만 어노테이션 정보를 유지한다. 소스 코드를 분석할 때만 의미가 있으며, 바이트 코드 파일에는 정보가 남지 않습니다. |
| CLASS | 바이트 코드 파일까지 어노테이션 정보를 유지한다. 하지만 리플렉션을 이용해서 어노테이션 정보를 얻을 수는 없습니다. |
| RUNTIME | 바이트 코드 파일까지 어노테이션 정보를 유지하면서 리플렉션을 이용해서 런타임 시에 어노테이션 정보를 얻을 수 있습니다.
 |

> 리플렉션(Reflection)이란 런타임 시에 클래스의 메타 정보를 얻는 기능을 말합니다. 예를 들어 클래스가 가지고 있는 필드가 무엇인지, 어떤 생성자를 갖고 있는지, 어떤 메소드를 가지고 있는지, 적용된 어노테이션이 무엇인지 알아내는 것이 리플렉션 입니다. 리플렉션을 이용해서 런타임 시에 어노테이션 정보를 얻으려면 어노테이션 유지 정채을 RUNTIME으로 설정해야 합니다. 어노테이션 유지 정책을 지정할 때에는 @Retention 어노테이션을 사용합니다.


## 4. 어노테이션을 적용한 예제코드

어노테이션과 리플렉션을 이용해서 간단한 예제를 만들어 보도록 합시다. 다음은 각 메소드의 실행 내용을 구분선으로 분리해서 콘솔에 출력하도록 하는 PrintAnnotation입니다.

#### @PrintAnnotation 어노테이션

```java
Target({ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface PrintAnnotation {
    String value() default "-";
    int number() default 15;
}
```

위의 코드에서 @Target은 메소드에만 적용하였고, @Retention은 런타임 시까지 어노테이션 정보를 유지하도록 하였습니다. 기본 엘리먼트 value는 구분선에 사용될 문자이고, number는 반복 출력 횟수입니다. 각각 디폴트 값으로 "-"와 15를 주었습니다. 다음은 PrintAnnotation을 적용한 Service 클래스 입니다.

#### Service 클래스

```java
public class Service {

    @PrintAnnotation // 해당 어노테이션은 엘리먼트의 기본값으로 설정
    public void method1(){
        System.out.println("실행 내용1");
    }

    @PrintAnnotation("*") // 엘리먼트 value 값을 "*"로 설정
    public void method2(){
        System.out.println("실행 내용2");
    }

    @PrintAnnotation(value="#", number=20) // value 값을 "#", number 20 설정
    public void method3(){
        System.out.println("실행 내용3");
    }
}
```

다음 PrintAnnotationExample 클래스는 리플렉션을 이용해서 Service 클래스에 적용된 어노테이션 정보를 읽고 엘리먼트 값에 따라 출력할 문자와 출력 횟수를 콘솔에 출력한 후, 해당 메소드를 호출합니다.

#### PrintAnnotationExample 클래스

```java
public class PrintAnnotationExample {
    public static void main(String[] args) {
        //Service 클래스의 메소드 정보를 Method 배열로 리턴합니다.
        Method[] declaredMethods = Service.class.getDeclaredMethods();
        //메소드 객체를 하나씩 처리
        for (Method method : declaredMethods) {
            if (method.isAnnotationPresent(PrintAnnotation.class)) {
                //객체 얻기
                PrintAnnotation printAnnotation = method.getAnnotation(PrintAnnotation.class);

                //메소드 이름 출력
                System.out.println("[" + method.getName() + "] ");

                //구분선 출력
                for (int i = 0; i < printAnnotation.number(); i++) {
                    System.out.print(printAnnotation.value());
                }
                System.out.println();


                try {
                    method.invoke(new Service());
                } catch (Exception e) {}
                    System.out.println();
            }
        }
    }
}
```

위의 isAnnotationPresent 메소드는 지정한 어노테이션이 적용되었는지 여부, Class에서 호출했을때 상위 클래스에 적용된 경우도 true를 리턴합니다.

method.invoke(new Service())는 Service 객체를 생성하고 생성된 Service 객체의 메소드를 호출하는 코드입니다. 메모리에 해당 객체가 있어야만 메소드를 호출이 가능하기 때문이죠.

이러한 어노테이션의 기본적인 개념에 대해서 이해를 하고 @Getter 어노테이션을 살펴보니 전에는 몰랐던 부분이 조금이나마 이해가 되었습니다. 실습을 통해서 리플렉션과 어노테이션에 대해서 깊게는 아니지만 보이지 않았던 부분이 조금이나마 알게 되어서 만족합니다.