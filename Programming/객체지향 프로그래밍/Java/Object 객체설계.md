2019년에 조영호님께서 출판한 OBJECTS 코드로 이해하는 객체지향 설계라는 책을 사놓고... 읽지를 않다가 조금씩이라도 읽으면서 나중에 잊어버리지 않도록 하기 위해서 간단하게 1장 객체, 설계에 대해서 블로그에 포스팅을 해보았습니다.

# 패러다임의 시대
가장 먼저 소개하고 있는 부분이 패러다임의 대한 것입니다. 제가 생각하는 패러다임은 `한 시대의 사회 전체가 공유하는 이론이나 방법, 문제 의식 등의 체계` 정도로만 알고 있었는데 이 책에서는 패러다임이 어떻게 등장하였고 프로그래밍 세계에서 패러다임이 의미하는것이 무엇인지 구체적으로 설명하고 있습니다.

과거에는 표준적인 모델을 따르거나 모방하는 상황을 가르키는 매우 제한적인 상황에서만 패러다임이라는 단어를 사용했습니다.
쿤이라는 사람은 과학혁명의 구조라는 책을 세상에 내놓았는데, 이 책에는 기존의 과학사에 대한 관점을 뿌리채 흔들었습니다.
과학혁명은 과학이 단순한 계단식 발전의 형태를 이루는 것이 아니라 새로운 발견이 기존의 과학적 견해를 붕괴시키는 혁명정인 과정을 거쳐 발전해왔다고 주장했습니다.
과학혁명이란 과거의 패러다임이 새로운 패러다임에 의해 대체됨으로써 정상과학의 방향과 성격이 변하는 것을 의미합니다. 이를 패러다임(Paradigm Shift)의 전환이라고 부릅니다. 대표적인 예로는 우주를 바라보는 관점이 천동설에서 지동설로 변화한 사건이 있습니다.

이제 프로그래밍 관점에서 패러다임을 살펴보겠습니다.
프로그래밍 패러다임은 특정 시대의 어느 성숙한 개발자 공동체에 의해 수용된 프로그래밍 방법과 문제 해결 방법, 프로그래밍 스타일이라고 할 수 있습니다. 간단히 말해서 우리가 어떤 프로그래밍 패러다임을 사용하느냐에 따라 우리가 해결할 문제를 바라보는 방식과 프로그램을 작성하는 방법이 달라집니다.
프로그래밍 패러다임은 개발자 공동체가 동일한 프로그래밍 스타일과 모델을 공유할 수 있게 함으로써 불필욜한 부분에 대한 의견 충돌을 방지합니다. 또한 프로그래밍 패러다임을 교욱시킴으로써 동일한 규칙과 방법을 공유하는 개발자로 성장할 수 있도록 준비시킬 수 있습니다.


> 결국 이 책은 객체지향 패러다임에 관한 책으로써 객체지향 패러다임이 제시하는 프로그래밍 패러다임을 설명하는 것에 목적을 두고 있습니다. 또한 객체지향에 대한 다양한 오해를 제거함으로써 객체지향 프로그래밍을 하는 개발자들이 동일한 규칙과 표준에 따라 프로그램을 작성할 수 있게 할 것입니다.



# 티켓 판매 애플리케이션 구현하기
이 책은 이론보다 실무를 강조하는 책입니다. 그렇기 때문에 객체지향에 대한 다양한 측면을 설명하기 위해 이론보다는 코드를 작성하여 설명할 것입니다.

이번 시간에는 간단한 티켓 판매 프로그램을 작성하여 리뷰해보겠습니다.

이 프로그램은 관객이 티켓을 통해 소극장에 입장하여 연극이나 음악회를 즐길 수 있습니다. 여기에 소극장을 홍보도 겸할 겸 관람객들의 발길이 이어지도록 작은 이벤트를 기획하기로 했습니다. 이벤트의 내용은 간단하게 추첨을 통해 선정된 관람객에게 공연을 무료로 관람할 수 있는 초대장을 발송하는 것입니다.

여기서 핀트는 이벤트에 당첨된 관람객과 그렇지 못한 관램객은 다른 방식으로 극장에 입장시켜야 한다는 것입니다. 이벤트에 당첨된 관람객은 초대장을 티켓으로 교환한 후에 입장할 수 있습니다.
이벤트에 담청되지 않은 관람객은 티켓을 구매해야만 입장할 수 있습니다. 따라서 관람객을 입장시키기 전에 이벤트 당첨 여부를 확인해야 하고 이벤트 당첨자가 아닌 경우에는 티켓을 판매한 후에 입장시켜야 합니다.

먼저, 이벤트 당첨자에게 발송하는 초대장을 구현하는 것으로 시작하겠습니다. 
초대장이라는 개념을 구현한 Invitation은 공연을 관람할 수 있는 초대일자를 인스턴스 변수로 포함하는 간단한 클래스입니다.

```java
package object;

import java.time.LocalDateTime;

public class Invitation {

    private LocalDateTime when;
}
```

공연을 관람하기 원하는 모든 사람들은 티켓을 소지하고 있어야만 하기 때문에 Ticket 클래스도 추가합니다.
```java
package object;

public class Ticket {

    private Long fee;

    public Long getFee(){
        return this.fee;
    }
}
```


이벤트 당첨자는 티켓으로 교환할 초대장을 가지고 있습니다. 이벤트에 당첨되지 않은 관람객은 티켓을 구매할 수 있는 현금을 보유하고 있을 것입니다. 따라서 관람객이 가지고 올 수 있는 소지품은 `초대장, 현금, 티켓` 세 가지뿐 입니다.

이제 관람객이 소지품을 보관할 Bag 클래스를 추가해봅니다. Bag 클래스는 `초대장(invitation), 티켓(ticket), 현금(amount)`을 인스턴스 변수로 포함합니다.
또한 초대장의 보유 여부를 판단하는 hasInvitation 메서드와 티켓의 소유 여부를 판단하는 hasTicket 메서드, 현금을 증가시키거나 감소시키는 plusAmount, minusAmount 메서드, 초대장을 티켓을오 교환하는 setTicket 메서드를 구현하고 있습니다.

```java
package object;

public class Bag {

    private Long amount;
    private Ticket ticket;
    private Invitation invitation;

    // 이벤트 당첨자가 아닐 경우 초대장이 없고 현금만 보유하고 있기 때문에 생성자로 아래 this 키워드를 이용하여 초대장에 null값을 참조
    public Bag(Long amount){
        this(null, amount);
    }

    // 이벤트 당첨자일 경우 초대장과, 현금을 둘다 보유하고 있기 때문에 아래와 같은 생성자를 호출합니다.
    public Bag(Invitation invitation, Long amount){
        this.invitation = invitation;
        this.amount = amount;
    }

    // 초대장이 있습니까?
    public boolean hasInvitation(){
        return this.invitation != null;
    }

    // 티켓이 있습니까?
    public boolean hasTicket(){
        return this.ticket != null;
    }
    // 현금 감소
    public void minusAmount(Long amount){
        this.amount -= amount;
    }
    // 현금 증가
    public void plusAmount(Long amount){
        this.amount += amount;
    }
    //티켓 교환
    public void setTicket(Ticket ticket){
            this.ticket = ticket;
    }

}
```

여기서 이벤트에 당첨된 관람객의 가방 안에는 현금과 초대장이 들어있지만 이벤트에 당첨되지 않는 관람객의 경우 가방 안에는 초대장이 들어있지 않을 것입니다. Bag 인스턴스의 상태는 현금과 초대장을 함꼐 보관하거나, 초대장 없이 현금만 보관하는 두 가지 중 하나일 것 입니다.
위의 코드에서 Bag 인스턴스를 생성하는 시점에 이 제약을 강제할 수 있도록 생성자를 추가하였습니다.

다음은 관람객이라는 개념을 구현하는 Audience 클래스를 정의하였습니다. 관람객은 소지품을 보관하기 위해 가방을 소지할 수 있습니다.

```java
package object;

public class Audience {

    private Bag bag;

    public Audience(Bag bag) {
        this.bag = bag;
    }

    public Bag getBag() {
        return bag;
    }
}
```

관람객이 소극장에 입장하기 위해서는 매표소에서 초대장을 티켓으로 교환하거나 구매해야 합니다. 따라서 매표소에는 관람객에게 판매할 티켓과 티켓의 판매 금액이 보관되어야 합니다. 매표소를 구현하기 위해 TicketOffice 클래스를 구현할 것 입니다.
TicketOffice는 판매하거나 교환해 줄 티켓의 목록(tickets)과 판매금액(amount)을 인스턴스 변수로 포함합니다. 티켓을 판매하는 getTicket 메서드는 편의를 위해 tickets 컬렉션에서 맨 첫번째 위치에 저장된 Ticket을 반환하는 것으로 구현했습니다. 또는 판매금액을 더하거나 차감하는 plusAmount와 minusAmount 메서드로 구현돼 있습니다.

```java
package object;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class TicketOffice {

    private Long amount;
    private List<Ticket> tickets = new ArrayList<>();

    public TicketOffice(Long amount , Ticket ... tickets){
        this.amount = amount;
        this.tickets.addAll(Arrays.asList(tickets));
    }


    public Ticket getTicket(){
        return this.tickets.remove(0);
    }

    public void minusAmount(Long amount){
        this.amount -= amount;
    }

    public void plusAmount(Long amount){
        this.amount += amount;
    }

}
```

판매원은 매표소에서 초대장을 티켓으로 교환해 주거나 티켓을 판매하는 역할을 수행합니다. 판매원을 구현한 TicketSeller 클래스는 자신이 일하는 매표소(ticketOffice)를 알고 있어야 합니다.

```java
package object;

public class TicketSeller {

    private TicketOffice ticketOffice;

    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }

    public TicketOffice getTicketOffice() {
        return ticketOffice;
    }
}
```

모든 준비가 끝이 났습니다. 이제 아래의 그림처럼 클래스들을 조합해서 관람객을 소극장에 입장시키는 로직을 완성하는 일만 남았습니다.

<img width="844" alt="스크린샷 2019-11-02 오전 1 15 15" src="https://user-images.githubusercontent.com/22395934/68073739-a2f05980-fdd6-11e9-96b6-a53caefc40e8.png">

소극장을 구현하는 클래스 Theater입니다. Theater 클래스가 관람객을 맞이할 수 있도록 enter 메소드를 구현합시다.

```java
package object;

public class Theater {

    private TicketSeller ticketSeller;

    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }


    public void enter(Audience audience){

        if(audience.getBag().hasInvitation()){
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().setTicket(ticket);
        }else {

            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().minusAmount(ticket.getFee());
            ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);

        }
    }
}
```

소극장은 먼저 관람객의 가방 안에 초대장이 들어 있는지 확인합니다. 만약 초대장이 들어 있다면 이벤트에 당첨된 관람객이므로 판매원에게 받은 티켓을 관람객의 가방 안에 넣어줍니다. 가방 안에 초대장이 없다면 티켓을 판매해야 합니다. 이 경우 소극장은 관람객의 가방에서 티켓 금액만큼을 차감한 후  메표소에 금액을 증가시킵니다. 마지막으로 소극장은 관람객의 가방 안에 티켓을 넣어줌으로써 관람객의 입장 절차를 끝냅니다. 

여기까지만 작성해도 프로그램 로직은 간단하고 예상했던대로 동작합니다. 하지만 안타깝게도 이 작은 프로그램은 몇가지 문제점을 가지고 있습니다.

# 무엇이 문제인가

로버트 마틴은 `<클린 소프트웨어: 애자일 원칙과 패턴, 그리고 실천 방법>`에서 소프트웨어 모듈이 가져야 하는 세 가지 기능에 관해 설명합니다. 여기서 모듈이란 크기와 상관없이 클래스나 패키지, 라이브러리와 같이 프로그램을 구성하는 임의의 요소를 의미합니다.

### 모든 소프트웨어 모듈에는 세가지 목적이 있습니다.

- 첫 번째 목적은 실행 중에 제대로 동작하는 것입니다. 이것은 모듈의 존재 이유입니다.

- 두 번째 목적은 변경을 위해 존재하는 것입니다. 대부분의 모듈은 생명주기 동안에 변경되기 때문에 간단한 작업만으로도 변경이 가능해야합니다. 변경하기 어려운 모듈은 제대로 동작하더라도 개선해야 합니다.

- 세 번째 목적은 코드를 읽는 사람과 의사소통 하는 것입니다. 모듈은 특별한 훈련 없이도 개발자가 쉽게 읽고 이해할 수 있어야 합니다. 읽는 사람과 의사소통 할 수 없는 모듈은 개선해야 합니다.

위에서 작성한 티켓 판매 프로그램은 관람객들을 입장시키는데 필요한 기능을 오류 없이 정확하게 수행하고 있습니다. 따라서 제대로 동작해야 한다는 제약은 만족합니다. 하지만 불행하게도 변경 용이성과 읽는 사람과의 의사소통이라는 목적은 만족시키지 못합니다.

# 예상을 빗나가는 코드
마지막에 소개한 Theater 클래스의 enter 메소드가 수행하는 일을 풀어보겠습니다.

소극장은 관람객의 가방을 열어 그 안에 초대장이 들어 있는지 살펴봅니다. 가방 안에 초대장이 들어 있으면 판매원은 매표소에 보관돼 있는 티켓을 관람객의 가방 안으로 옮깁니다. 가방 안에 초대장이 들어 있지 않다면 관람객의 가방에서 티켓 금액만큼의 현금을 꺼내 매표소에 적립한 후에 매표소에 보관돼 있는 티켓을 관람객의 가방 안으로 옮깁니다.


여기서 문제는 관람객과 판매원이 소극장의 통제를 받는 수동적인 존재라는 점입니다.
관람객의 입장에서 문제는 소극장이라는 제3자가 초대장을 확인하기 위해 관람객의 가방을 마음대로 열어 본다는데 있습니다. 만약 누군가가 허락 없이 가방 안의 내용물을 마음대로 뒤적이고 돈을 가져간다면... 어떻겠습니까.. 넋놓고 다른 사람이 저의 가방을 헤집어 넣는 것을 멍하니 바라볼 사람은 없을 것입니다.

판매원도 마찬가지 입니다. 소극장이 판매원의 허락도 없이 매표소에 보관 중인 티켓과 현금을 마음대로 접근 할 수 있기 때문입니다. 더 큰 문제는 티켓을 꺼내 관람객의 가방에 집어넣고 관람객에게서 받은 돈을 매표소에 적립하는 일을 판매원이 아닌 소극장이 수행한다는 점입니다. 판매원 입장에서는 가만히 앉아 티켓이 하나씩 사라지고 돈이 저절로 쌓이는 광경을 두 손 놓고 쳐다볼 수 밖에 없는 것입니다.

현재 위의 코드는 우리의 상식과는 다르게 너무나도 다르게 동작하기 때문에 코드를 읽는 사람과 제대로 의사소통하지 못합니다.
코드를 이해하기 어렵게 만드는 또 다른 이유는 이 코드를 이해하기 위해서는 여러가지 세부적인 내용들을 한꺼번에 기억하고 있어야 합니다. Theater의 enter 메소드를 살펴보면 Audience가 Bag을 가지고 있고, Bag 안에는 현금과 티켓이 들어 있으며 TicketSeller가 TicketOffice에서 티켓을 판매하고, TicketOffice안에 돈과 티켓이 보관돼 있다는 모든 사실을 동시에 기억하고 있어야 합니다. 이 코드는 하나의 클래스나 메서드에 너무 많은 세부사항을 다뤽 때문에 코드를 작성하는 사람뿐만 아니라 코드를 읽고 이해해야 하는 모두에게 큰 부담을 줍니다.

하지만 가장 심각한 문제는 이것이 아닙니다. 그것은 Audience와 TicketSeller를 변경할 경우 Theater도 함께 변경해야 한다는 사실입니다.

# 변경에 취약한 코드
더 큰 문제는 변경에 취약하다는 것입니다. 이 코드는 관람객이 현금과 초대장을 보관하기 위해 항상 가방을 들고 다닌다고 가정합니다. 또한 판매원이 매표소에서만 티켓을 판매한다고 가정합니다. 관람객이 가방을 들고 있지 않다면 어떻게 해야할까요?
관람객이 현금이 아니라 신용카드를 이용해서 결제를 한다면 어떻게 해야할까요? 판매원이 매표소 밖에서 티켓을 판매해야 한다면 어떻게 해야할까요? 이런 가정이 깨지는 순간 모든 코드가 일시에 흔들리게 됩니다.

관람객이 가방을 들고 있다는 가정이 바뀌었다고 상상해봅시다. Audience 클래스에서 Bag을 제거해야 할뿐만 아니라 Audience의 Bag에 직접 접근하는 Theater의 enter 메소드 역시 수정해야 합니다. Theater는 관람객이 가방을 들고 있고 판매원이 매표소에서만 티켓을 판매한다는 지나치게 세부적인 사실에 의존해서 동작합니다. 이러한 세부적인 사실 중 한 가지라도 바뀌면 해당 클래스 뿐만 아니라 이 클래스에 의존하는 Theater도 함께 변경해야 합니다. 이처럼 다른 클래스가 Audience의 내부에 대해 더 많이 알면 알수록 Audience를 변경하기 어려워집니다.

이것은 객체 사이의 의존성(dependency)과 관련된 문제입니다. 문제는 의존성이 변경과 관련돼 있다는 점입니다. 의존성은 변경에 대한 영향을 암시합니다. 의존성이라는 말 속에는 어떤 객체가 변경될 때 그 객체에게 의존하는 다른 객체도 함꼐 변경될 수 있다는 사실이 내포돼 있습니다.

그렇다고 해서 객체 사이의 의존성을 완전히 없애는 것이 정답이 아닙니다. 객체지향 설계는 서로 의존하면서 협력하는 객체들의 공동체를 구축하는 것입니다. 따라서 우리의 목표는 애플리케이션의 기능을 구현하는데 필요한 최소한의 의존성만 유지하고 불필요한 의존성을 제거하는 것입니다.

<img width="876" alt="스크린샷 2019-11-03 오전 2 07 21" src="https://user-images.githubusercontent.com/22395934/68074478-b43d6400-fdde-11e9-88d2-afa201721bbe.png">

객체 사이의 의존성이 과한 경우를 가리켜 결합도(coupling)가 높다고 말합니다. 반대로 객체들이 합리적인 수준으로 의존할 경우에는 결합도가 낮다고 말합니다. 결합도는 의존성과 관련돼 있기 때문에 결합도 역시 변경과 관련이 있습니다. 두 객체 사이의 결합도가 높으면 높을수록 함께 변경될 확률도 높아지기 때문에 변경하기 어려워 집니다. 따라서 설계의 목표는 객체 사이의 결합도를 낮춰 변경이 용이한 설계를 만드는 것입니다.

# 설계 개선하기
예제 코드는 로버트 마틴이 이야기한 세 가지 목적 중 한가지는 만족시키지만 다른 두 조건은 만족시키지 못합니다. 이 코드는 기능은 제대로 수행하지만 이해하기 어렵고 변경하기 쉽지 않습니다.

여기서 변경과 의사소통이라는 문제가 서로 엮여 있다는 점에 주목합니다. 코드를 이해하기 어려운 이유는 Theater가 관람객의 가방과 판매원의 매표소에 직접 접근하기 때문입니다. 이것은 관람객과 판매원이 자신의 일을 스스로 처리해야 한다는 우리의 직관을 벗어납니다. 다시 말해서 의도를 정확하게 의사소통하지 못하기 때문에 코드가 이해하기 어려워진 것입니다. Theater가 관람객의 가방과 판매원의 매표소에 직접 접근한다는 것은 Theater가 Audience와 TicketSeller에 결합된다는 것을 의미합니다. 따라서 Audience와 TicketSeller를 변경할 때 Theater도 함꼐 변경해야 하기 때문에 전체적으로 코드를 변경하기도 어려워집니다.

해결방법은 간단합니다. Theater가 Audience와 TicketSeller에 관해 너무 세부적인 부분까지 알지 못하도록 정보를 차단하면 됩니다. 사실 관람객이 가방을 가지고 있다는 사실과 판매원이 매표소에서 티켓을 판매한다는 사실을 Theater가 알아야 할 필요가 없습니다. Theater가 원하는 것은 관람객이 소극장에 입장하는 것 뿐입니다. 따라서 관람객이 스스로 가방 안의 현금과 초대장을 처리하고 판매원이 스스로 매표소의 티켓과 판매 요금을 다루게 한다면 이 모든 문제를 한 번에 해결할 수 있습니다.

> 다시 말해서 관람객과 판매원을 자율적인 존재로 만들면 되는 것이 이 장의 핀트입니다.

# 자율성을 높이자
해결 방법은 Audience와 TicketSeller가 직접 Bag과 TicketOffice를 처리하는 자율적인 존재가 되도록 설계를 변경하는 것입니다.

첫 번째 단계는 Theater의 enter 메소드에서 TicketOffice에 접근하는 모든 코드를 TicketSeller 내부로 숨기는 것입니다. TicketSeller에 sellTo 메소드를 추가하고 Theater에 있던 로직을 이 메서드로 옮깁니다.

```java
public void enter(Audience audience){

    if(audience.getBag().hasInvitation()){
        Ticket ticket = ticketSeller.getTicketOffice().getTicket();
        audience.getBag().setTicket(ticket);
    }else {
        Ticket ticket = ticketSeller.getTicketOffice().getTicket();
        audience.getBag().minusAmount(ticket.getFee());
        ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
        audience.getBag().setTicket(ticket);
    }
}
---------------------변 경 전 후 ------------------------
package object;

public class TicketSeller {

    private TicketOffice ticketOffice;

    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }

    public void sellTo(Audience audience){
        if(audience.getBag().hasInvitation()){
            Ticket ticket = ticketOffice.getTicket();
            audience.getBag().setTicket(ticket);
        }else{
            Ticket ticket = ticketOffice.getTicket();
            audience.getBag().minusAmount(ticket.getFee());
            ticketOffice.plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
    }
}
```

TicketSeller에서 getTicketOffice 메소드가 제거됐다는 사실에 주목합시다. ticketOffice의 가시성이 private이고 접근 가능한 퍼블릭 메소드가 더이상 존재하지 않기 때문에 외부에서 ticketOffice에 직접 접근할 수 없습니다.
결과적으로 ticketOffice에 대한 접근은 오직 ticketSeller 안에만 존재하게 됩니다. 따라서 TicketSeller는 ticketOffice에서 티켓을 꺼내거나 판매 요금을 적립하는 일을 스스로 수행할 수밖에 없습니다.

이처럼 개념적이거나 물리적으로 객체 내부의 세부적인 사항을 감추는 것을 `캡슐화(encapsulation)`이라고 부릅니다. 캡슐화의 목적은 변경하기 쉬운 객체를 만드는 것입니다. 캡슐화를 통해 객체 내부로의 접근을 제한하면 객체와 객체 사이의 결합도를 낮출 수 있기 때문에 설계를 좀 더 쉽게 변경할 수 있게 됩니다.


이제 Theater의 enter 메소드는 sellTo 메소드를 호출하는 간단한 코드로 변경됩니다.

```java
package object;

public class Theater {

    private TicketSeller ticketSeller;

    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }


    public void enter(Audience audience){
        ticketSeller.sellTo(audience);
    }
}
```

이제는 Theater는 TicektOffice가 TicketSeller 내부에 존재한다는 사실을 알지 못합니다. Theater는 단지 ticketSeller가 sellTo 메시지를 이해하고 응답할 수 있다는 사실만 알고 있을 뿐입니다.

Theater는 오직 TicketSeller의 `인터페이스(interface)`에만 의존합니다. TicketSeller가 내부에 ticketOffice 인스턴스를 포함하고 있다는 사실은 구현의 영역에 속합니다. 객체를 인터페이스와 `구현(implementation)`으로 나누고 인터페이스만을 공개하는 것은 객체 사이의 결합도를 낮추고 변경하기 쉬운 코드를 작성하기 위해 따라야 하는 가장 기본적인 설계 원칙입니다.


<img width="937" alt="스크린샷 2019-11-03 오후 2 27 13" src="https://user-images.githubusercontent.com/22395934/68080803-12e9f880-fe46-11e9-88d5-fa33f57346cd.png">

#### Theater의 결합도를 낮춘 설계

위의 그림은 수정 후의 클래스 사이의 의존성을 나타낸 것입니다. Theater의 로직을 TicketSeller로 이동시킨 결과, Theater에서 TicketOffice로의 의존성이 제거됐다는 사실을 알 수 있습니다. TicketOffice와 협력하는 TicketSeller의 내부 구현이 성공적으로 캡슐화 된 것입니다.

아제 Audience의 캡슐화를 개선해야 합니다. TicketSeller는 Audience의 getBag 메소드를 호출해서 Audience 내부의 Bag 인스턴스에 직접 접근합니다. Bag 인스턴스에 접근하는 객체가 Theater에서 TicketSeller로 바뀌었을 뿐 Audience는 여전히 자율적인 존재가 아닌 것입니다.

TicketSeller와 동일한 방법으로 Audience의 캡슐화를 개선할 수 있습니다. Bag에 접근하는 모든 로직을 Audience 내부로 감추기 위해 Audience에 buy 메소드를 추가하고 TicketSeller의 sellTo 메소드에서 getBag 메소드에 접근하는 부분을 buy 메소드로 옮겨 보겠습니다.

```java
package object;

public class Audience {

    private Bag bag;

    public Audience(Bag bag) {
        this.bag = bag;
    }

    public Bag getBag() {
        return bag;
    }
    
    public Long buy(Ticket ticket) {
        if (bag.hasInvitation()) {
            bag.setTicket(ticket);
            return 0L;
        } else {
            bag.setTicket(ticket);
            bag.minusAmount(ticket.getFee());
            return ticket.getFee();
        }
    }
}
```
변경된 코드에서 Audience는 자신의 가방 안에 초대장이 들어있는지를 스스로 확인합니다. 외부의 제3자가 자신의 가방을 열어보도록 허용하지 않습니다. Audience가 직접 Bag을 처리하기 때문에 외부에서는 더 이상 Audience가 Bag을 소유하고 있다는 사실을 알 필요가 없습니다.

이제 TicketSeller가 Audience의 인터페이스에만 의존하도록 수정하면 됩니다.TicketSeller가 buy 메서드를 호출하도록 코드를 변경하면 됩니다.

```java
package object;

public class TicketSeller {

    private TicketOffice ticketOffice;

    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }


    public void sellTo(Audience audience){
        ticketOffice.plusAmount(audience.buy(ticketOffice.getTicket()));
    }

}
```

코드를 수정한 결과, TicketSeller와 Audience 사이의 결합도가 낮아졌습니다. 또한 내부 구현이 캡슐화 됐으므로 Audience의 구현을 수정하더라도 TicketSeller에는 영향을 미치지 않습니다.

캡슐화를 개선한 후에 가장 크게 달라진 점은 Audience와 TicketSeller가 내부 구현을 외부에 노출하지 않고 자신의 문제를 스스로 책임지고 해결한다는 것입니다.
다시 말해 자율적인 존재가 된 것입니다.

# 무엇이 개선됐는가
수정된 Audience와 TicketSeller는 자신이 가지고 있는 소지품을 스스로 관리합니다. 이것은 우리의 예상과도 정확하게 일치합니다. 따라서 코드를 읽는 사람과의 의사소통이라는 관점엣허 이 코드는 확실히 개선된 것으로 보입니다.
더 중요한 점은 Audience나 TicketSeller의 내부 구현을 변경하더라도 Theater를 함께 변경할 필요가 없다는 것입니다. Audience가 가방이 아니라 작은 지갑을 소지하도록 코드를 변경하고 싶으면 Audience 내부만 변경하면 됩니다.
TicketSeller가 매표소가 아니라 은행에 돈을 보관하도록 만들고 싶으면 TicketSeller 내부만 변경하면 됩니다.

# 어떻게 한 것인가
간단하게 판매자가 티켓을 판매하기 위해 TicketOffice를 사용하는 모든 부분을 TicketSeller 내부로 옮기고, 관람객이 티켓을 구매하기 위해 Bag을 사용하는 모든 부분은 Audience 내부로 옮겼습니다. 다시 말해 자기 자신의 문제를 스스로 해결하도록 코드를 변경하였습니다. 우리는 우리의 직관을 따랐고 그 결과로 코드는 변경이 용이하고 이해 가능학도록 수정됐습니다.

> 우리는 객체의 자율성을 높이는 방향으로 설계를 개선했습니다. 그 결과, 이해하기 쉽고 유연한 설계를 얻을 수 있었습니다.

# 캡슐화와 응집도
핵심은 객체 내부의 상태를 캡슐화하고 객체 간에 오직 메시지를 통해서만 상호작용하도록 만드는 것입니다. Theater는 TicketSeller의 내부에 대해서는 전혀 알지 못합니다.단지 TicketSeller가 sellTo 메시지를 이해하고 응답할 수 있다는 사실만 알고 있을 뿐입니다. TicketSeller 역시 Audience의 내부에 대해서는 전혀 알지 못합니다. 단지 Audience가 buy 메시지에 응답할 수 있고 자신이 원하는 결과를 반환할 것이라는 사실만 알고 있을 뿐입니다.

밀접하게 연관된 작업만을 수행하고 연관성 없는 작업은 다른 객체에게 윙미하는 객체를 가리켜 `응집도(cohesion)`가 높다고 말합니다. 자신의 데이터를 스스로 처리하는 자율적인 객체를 만들면 결합도를 낮출 수 있을뿐더러 응집도를 높일 수 있다.

> 외부의 간섭을 최대한 배제하고 메시지를 통해서만 협력하는 자율적인 객체들의 공동체를 만드는 것이 훌륭한 객체지향 설계를 얻을 수 있는 지름길 입니다.


# 절차지향과 객체지향
수정하기 전의 코드에서 Audience, TicketSeller, Bag, TicketOffice는 관람객을 입장시키는데 필요한 정보를 제공하고 모든 처리는 Theater의 enter 메소드안에 존재했습니다.

이 관점에서 Theater의 enter 메소드는 `프로세스`이며 Audience, TicketSeller,Bag, TicketOffice는 데이터 입니다. 이처럼 프로세스와 데이터를 별도의 모듈에 위치시키는 방식을 절차적 프로그래밍이라고 부릅니다.

절차적 프로그래밍 세상에서는 데이터 변경으로 인한 영향을 지역적으로 고립시키기 어렵다는 것입니다. Audience, TicketSeller의 내부 구현을 변경하려면 Theater의 enter 메소드를 함께 변경해야 합니다. 변경은 버그를 부르고 버그에 대한 두려움은 코드를 변경하기 어렵게 만듭니다. 따라서 절차적 프로그래밍 세상은 변경하기 어려운 코드를 양산하는 경향이 있습니다.

변경하기 쉬운 설계는 한 번에 하나의 클래스만 변경할 수 있는 설계입니다. 절차적 프로그래밍은 프로세스가 필요한 모든 데이터에 의존해야 한다는 근본적인 문제점 때문에 변경에 취약할 수 밖에 없습니다.

수정한 후의 코드에서는 데이터를 사용하는 프로세스가 데이터를 소유하고 있는 Audience와 TicketSeller 내부로 옮겨졌습니다. 이처럼 데이터와 프로세스가 동일한 모듈 내부에 위치하도록 프로그래밍 하는 방식을 객체지향 프로그래밍이라고 부릅니다.

# 책임의 이동
두 방식 사이에 근본적인 차이를 만드는 것은 책임의 이동입니다. 여기서는 `책임`을 기능을 가리키는 객체지향 세계의 용어로 생각해도 무방합니다.

두 방식의 차이점을 가장 쉽게 이해할 수 있는 방법은 기능을 처리하는 방법을 살펴보는 것입니다. 

<img width="677" alt="스크린샷 2019-11-03 오후 11 29 27" src="https://user-images.githubusercontent.com/22395934/68086784-9c271c80-fe92-11e9-8f6b-eb84222e62d3.png">

#### 책임이 중앙집중된 절차적 프로그래밍 

위의 절차지향 프로그래밍 처리 흐름도 그림에서 알수 있듰이 작업 흐름이 주로 Theater에 의해 제어된다는 사실을 알 수 있습니다.
객체지향 세계의 용어를 사용해서 표현하면 책임이 Theater에 집중돼 있는 것입니다.


<img width="657" alt="스크린샷 2019-11-03 오후 11 35 06" src="https://user-images.githubusercontent.com/22395934/68086785-9cbfb300-fe92-11e9-9a59-cfbffd8cfeaf.png">

#### 책임이 분산된 객체지향 프로그래밍

그에 반해 객체지향 설계에서는 제어 흐름이 각 객체에 적절하게 분산돼 있음을 알 수 있습니다. 다시 말해 하나의 기능을 완성하는데 필요한 책임이 여러 객체에 걸쳐 분산돼 있는 것입니다.

변경 전의 절차적 설계에서 Theater가 전체적인 작업을 도맡아 처리했습니다. 변경 후의 객체 지향 설계에서는 각 객체가 자신이 맡은 일을 스스로 처리했습니다. 다시 말해 Theater에 몰려 있던 책임이 개별 객체로 이동한 것 입니다. 이것이 바로 `책임의 이동`이 의미하는 것입니다.

이렇게 객체지향적으로 코드를 작성하면서 더 즐거운 일은 코드가 더 이해하기 쉬워졌다는 점입니다. TicketSeller의 책임은 티켓을 판매하는 것이고, Audience는 티켓을 사는 책임을 가졌습니다. Theater는 관람객을 입장시키는 책임을 가졌습니다. 적절한 객체에 적절한 책임을 할당하면 이해하기 쉬운 구조와 읽기 쉬운 코드를 얻게 됩니다.

설계를 어렵게 만드는 것은 `의존성`이라는 것을 기억해야 합니다. 해결 방법은 불필요한 의존성을 제거함으로써 객체 사이의 결합도를 낮추는 것입니다. 예제코드에서 결합도를 낮추기 위해 선택한 방법은 Theater가 몰라도 되는 세부사항을 Audience와 TicketSeller 내부로 감춰 캡슐화하는 것입니다. 결과적으로 불필요한 세부사항을 객체 내부로 캡슐화하는 것은 객체의 `자율성`을 높이고 `응집도` 높은 객체들의 공동체를 창조할 수 있게 합니다.

# 더 개선할 수 있다
현재의 설계는 이전의 설계보다 분명히 좋아졌지만 아직도 개선의 여지가 있습니다. Audience 클래스를 살펴보겠습니다.

```java
package object;

public class Audience {

    private Bag bag;

    public Audience(Bag bag) {
        this.bag = bag;
    }


    public Long buy(Ticket ticket) {
        if (bag.hasInvitation()) {
            bag.setTicket(ticket);
            return 0L;
        } else {
            bag.setTicket(ticket);
            bag.minusAmount(ticket.getFee());
            return ticket.getFee();
        }
    }
}
```

Audience는 분명 자율적인 존재입니다. 스스로 티켓을 구매하고 가방 안의 내용물을 직접 관리합니다. 하지만 Bag은 