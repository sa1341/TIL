## Null 종말의 신호

최근에 서점에서 우연히 `개발자가 되기 위해 꼭 알아야하는 개발 규칙`이라는 책의 서론을 보게 되었습니다. 이 책은 대부분의 개발도서에서 다루는 좋은 프로그래밍 습관 방법을 기술하는게 아니고, 나쁜 개발자가 되기 위한 지침들이 명시되어 있는 책입니다. 이 부분이 상당히 호기심을 자극하여서 구매를 결정하였습니다. 여기서 Null과 관련된 주제가 있어서 한번 포스팅하였습니다.


저는 회사에서 개발을 할때마다 항상 데이터를 조회하는 코드(DB 조회 등)를 작성할 때 제일 신경써야 되는 부분이 NPE(Null Point Exception)입니다.

NPE가 발생하면 예외처리를 무조건 해줘야 하기 때문에 개발자들이 가장 신경써야 되는 부분인것 같습니다.

예를 들어서 아래와 같은 코드가 있다고 합시다.

```java
CustomerAccount c = getNextCustomer(); 
System.out.println(c.getSurname());
```

깔끔하고 올바르게 보이는 위 코드는 CustomerAccount 객체를 가져와서 고객의 surname을 출력합니다. 하지만 개발자가 게으르면(나쁜 프로그래밍을 위한 좋은 습관) 이 부분을 간과하게 되고 언젠가는 getSurname() 메소드가 null을 반환하는 것을 보게 될것입니다.
결국, 코드는 NullPointException이 발생하기만을 기다리는 것입니다.

## 재앙을 심는법

자신만의 서브루틴을 작성할 때, 가능하다면 확실히 null을 반환하도록 해야합니다. 여기에 아래와 같은 놀라운 방법들이 있습니다.

- 서브루틴에 의해 반환될 변수를 만들 때, 해당 변수를 null로 초기화 하기
- 서브루틴이 `empty`값을 반환해야 할 때, null을 반환하기
- 서브루틴의 사용자에게 null을 반환하는 경우에 대한 단서를 남기지 않기, 이는 사용자가 본인이 아는 선에서 서브루틴을 좀 더 견고하게 만들려고 하는 위험을 증가시킬 뿐입니다.

현재도 null과의 싸움은 계속되고 있습니다. 코드 내에서 null을 사용하는 것을 막으려는 매의눈을 가진 개발자나 리뷰어뿐만 아니라, 프로그래밍 언어들도 null의 잠재적 위험성을줄이려는 시도를 하고 이씃ㅂ니다. 리뷰어는 당신의 코드에 어떤 문제가 있는지 살펴볼 것 입니다. 그리고 충분히 주의를 기울인다면 null을 확인하지 않는 곳을 찾아내고 다음과 같이 다시 작성하라고 요구할 것입니다.

```java
CustomerAccount c = getNextCustomer();
if (c.getSurname() != null) {
    System.out.println(c.getSurname());
} 
```

물론 리뷰어들은 CustomerAccount 클래스를 작성한 개발자가 이 코드에 속성값이 null이라면 빈값을 초기화하여 동작하도록 만드는 것을 기대할지도 모릅니다. getSurname() 메서드 호출 시에 surname이 없다면 빈 값을 넣어서 리턴을 해줄 수 있습니다.

서브루틴이 반드시 null을 반환해야 한다면, 그것을 매우 명확하게 나타내야 합니다. 이는 사용된 언어에 따라 달라질 수 있습니다.

```java
/**
* 고객의 성을 기준으로 계정을 검색합니다.
* @return 계정 객체를 반환하거나 계정을 찾을 수 없는 경우 null을 반환합니다.
*/
public CustomerAccount getAccountBySurname(String surname) {
    //...
}
```

또한, 자바는 변수가 null값을 가질 수 있는지 또는 메소드가 null을 반환할 수 있는지를 나타내는 @NotNull과 같은 어노테이션 주석을 지원합니다. 컴파일러나 IDE와 같은 도구는 코드가 이 주석에서 기대한 동작과 일치하는지 확인하고, 그에 따라 컴파일 시에 문제가 있는 코드를 보고할 수 있습니다.

null에 대항하기 위한 또 다른 무기는 Optional 타입입니다. 값을 가지지 않을 가능성이 있는 변수 (즉, null과 같은 값)을 캡슐화하고, 이럴 경우 프로그래머가 무엇을 해야 할지 고려하도록 만듭니다. 이는 null 검사를 수행해야 한다고 의식하는 것보다 잠재적으로 누락할 수 있는 값을 안전하고 쉽게 처리할 수 있게 해줍니다.

아래 예제 코드에서 getGradeForStudent는 시험을 치르는 학생에게 할당된 성적을 반환합니다. 하지만 학생이 아직 시험을 치르지 않았을 가능성도 있는데, 이 경우에는 성적이 없을 것입니다. 따라서 getGradeForStudent는 Grade 객체 대신에 Optional<Grade> 객체를 반환합니다.

```java
// maybeGrade는 Grade를 가지고 있을 수도 아닐 수도 있습니다.
Optional<Grade> maybeGrade = getGradeForStudent[StudentNumber];
//점수가 없는 경우에는 "Unassgned"를 반환합니다.
String grade = maybeGrade
    .map(Grade :: toString)
    .orElse("Unassigned");
System.out.println(grade);
```

null 값을 가진 변수를 출력하면 오류가 발생하므로, Optional 타입은 먼저 grade의 toString 메소드를 호출하여 등급 분자열을 가져가려고 시도할 것입니다. 값이 없는 경우에는 orElse 메소드의 값이 대신 반환될 것입니다.

