## Spring Data JPA + QueryDsl을 이용한 게시판

### 해당 프로젝트를 만들면서 배울수 있었던 점은 아래와 같습니다.

#### 1. 스프링부트 + Spring Data JPA

에전에 이클립스 IDE 환경에서 스프링 프레임워크와 SQL Mapper 프레임워크인 Mybatis를 이용하여 간단한 게시글과 댓글을 작성하는 사내 게시판을 만든적이 있습니다. 
그 당시에는 도메인과 VO 개념이 전혀 없었기 때문에 테이블에 해당 도메인에 매핑된 값만 넣는 CRUD 작업으로 게시판과 댓글 기능이 되는 그럴듯하게 동작하게 만드는데 포커를 맞추었었습니다. 그러다가 한동안 손을 두다가 스프링부트와 JPA를 접하면서 영속컨텍스트와 Querydsl을 배우면서 신세계를 접하였습니다. 테이블을 설계하는 시간보다 비즈니스 로직에 집중을 할 수 있었고, 이러한 집중은 그나마 전에 만들었던 게시판보다 더 객체지향적으로 게시판을 구현할 수 있었습니다.


#### 2. 게시판 웹사이트를 JPA를 통한 리팩토링
그 전에 만들었던 Mybatis 프레임워크 기반의 게시판 프로젝트는 테이블을 설계하는데 사실 많은 시간을 보내었고, 객체중심이 아니라 테이블 중심으로 설계를 하고 구현을 하였습니다. 하지만 스프링부트와 Spring Data JPA를 사용하면서 좀 더 객체지향적으로 비즈니스 로직을 구현하고, 도메인과 VO, 뷰 템플릿에 보여줄 Response 객체까지 구현하여 유지보수성을 좀 더 쉽게 할 수 있도록 하면서 객체지향 설계에 대한 중요성을 조금이라도 알게 되었습니다.


#### 3. 사용한 기술 및 툴

- Spring Boot
- Spring Data JPA
- IntelliJ IDEA Ultimate 
- JAVA 8
- MySQL
- H2
- QueryDsl
- Postman(REST API)


------------------




# Step-01 프로젝트 설계 및 프로젝트 생성

게시판이 처음에 가장 많은 주니어 개발자들이 만드는 대표적인 첫 도전과제이지만, 돌아가는 프로그램도 중요하지만, 안정적이고 다른 개발자들이 쉽게 보고 이해 할 수 있는 유지보수성이 좋은 프로젝트를 구현하는게 중요하다고 생각합니다.

이번 게시판 프로젝트를 만들면서 리뷰할 내용은 아래와 같습니다.
* 어떤 서비스를 만들 것인가?
* 도메인 구조, 테이블 구조
* 프로젝트 구조
* Spring Boot 프로젝트 생성


# 1. 어떤 서비스를 만들것인가.
## 게시판을 등록하고 해당 게시글에 댓글을 REST API 방식으로 서비스를 제공하는 서버를 만들 것입니다. 

1. 게시글을 등록하기 위해서는 제목을 반드시 입력해야 합니다.
2. 게시글을 수정, 삭제를 할 수 있습니다.
3. 해당 게시글에 댓글을 작성할 수 있습니다.
4. 해당 게시글에 달린 댓글을 수정, 삭제 할 수 있습니다.

이번 프로젝트의 목적은 Spring Data JPA를 사용하여 엔티티관의 연관관계를 설정하여 간단하게 어떻게 연관관계를 매핑하고 테이블과 객체지향 사이의 패러다임 불일치를 해결하는지 과정을 알기 위해서 만든 프로젝트이기 때문에 따로 회원가입 기능을 추가하여 특정 권한과 인증여부와 상관없이 REST 방식으로 서비스를 호출하는 방식으로 실제 구현을 하였습니다. 추후 스프링 시큐리티나 OAUTH 2.0 방식을 이용하여 인증처리를 하는 기능까지 추가하여 리뷰를 하겠습니다.


# 2. 도메인 구조, 테이블 구조

게시글과 댓글 도메인의 클래스 다이어그램은 아래와 같습니다.

![스크린샷 2019-10-20 오전 12 46 19](https://user-images.githubusercontent.com/22395934/67147785-183a3580-f2d3-11e9-909c-93e14d5b3c7d.png)

아직 회원 엔티티는 엔티티만 만들어두고 실제로 사용하지 않기 때문에 따로 ERD에 포함시키지 않았습니다. 위 그림을 보면 테이블 구조와 도메인 구조가 다르다는 것을 보실 수 있습니다. 테이블은 외래키라는 하나의 연관관계를 가지고 양 방향으로 조회가 가능하지만, 객체지향 설게에서는 객체 간의 관계는 참조를 통해서 서로 다른 단방향으로만 참조가 가능합니다. 즉 객체 그래프 탐색을 이용하기 위해서는 양방향 매핑을 해야하는데 아래 프로젝트를 진행하면서 어떻게 JPA가 RDB와 Entity 사이의 패러다임 불일치를 해결하는지 살펴보겠습니다.

![스크린샷 2019-10-20 오전 1 00 18](https://user-images.githubusercontent.com/22395934/67148023-913a8c80-f2d5-11e9-8387-3723032d032e.png)




# 3. 프로젝트 구조
프로젝트 구조는 다음과 같이 이루어질것입니다.

![](https://i.imgur.com/gkCTUtC.png)


| 계층               | 설명                                                                                                                                                                                                       |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **표현영역**           | 클라이언트에 요청을받아 그것을 응용 영역에 전달하고 응용영역에서 처리한값을 클라이언트에게 전달하는 역할을 합니다. 클라이언트로부터 HTTP요청을 받아 처리후 JSON형태로 클라이언트에 전달하는 역할을 합니다. |
| **응용영역**           | 표현 계층으로부터 전달받은 데이터를 기반으로 비지니스 로직을 수행하기보다는 도메인들한테 시키는 역할을합니다.                                                                                              |
| **도메인영역**         | 도메인들에 핵심 로직을 구현하는 역할입니다.(객체지향 관점에 맞게 객체한테 시키시킨다고 보시면됩니다.)                                                                                                      |
| **인프라스트럭쳐영역** | 이 영역은 DB, 외부 api 등에 대한 처리를 하는 영역입니다. 그렇게해야 응용 영역에서 통일된 데이터를 사용할 수 있습니다.| 


각 계층은 자신의 계층의 책임에만 집중해야 합니다. 자신의 영역을 넘어서는 코드를 짜게되면 코드가 복잡해지고 코드의 응집력이 떨어지기 때문입니다. 결국엔 변화에 대한 유연성이 떨어지고 이해하기 힘든 코드가 되어 유지보수가 어려워집니다. 그러기 때문에 항상 이것을 염두하고 코딩을 해야합니다.


![스크린샷 2019-10-19 오후 11 15 28](https://user-images.githubusercontent.com/22395934/67146476-5d0b9f80-f2c6-11e9-8251-80c2f4334818.png)

기본적으로 프로젝트를 진행하기위해 아래와같은 의존성 라이브러리들을 선택해줘야합니다.
H2는 테스트 용으로 사용할 DB 입니다. 메모리 기반이기 때문에 테스트를 하는데 가볍기 때문에 사용하였습니다. 

참고로 스프링 부트에서는 H2만 의존성으로 설정해주면 자동으로 메모리 기반으로 잡아주기 때문에 따로 application.properties 같은 설정파일에 명시하지 않아도 실행하는데 문제가 없습니다.

![스크린샷 2019-10-19 오후 11 11 36](https://user-images.githubusercontent.com/22395934/67146416-e7073880-f2c5-11e9-826a-7e5737d44fb8.png)

이프로젝트에서는 2.1.9를 사용합니다. 프로젝트가 만들어진 이후 build.script에서 변경하실 수 있습니다.

![](https://i.imgur.com/6Kk0Bj9.png)

최종적으로 Finish를 누르면 프로젝트 생성이됩니다. 

# 마치며
다음장부터 본격적으로 API서버를 만들어보는 시간을 가져보겠습니다!



# Step-02 게시판 및 댓글 엔티티 정의

## 1. 게시판 엔티티 정의

![스크린샷 2019-10-20 오전 1 19 33](https://user-images.githubusercontent.com/22395934/67148204-c051fd80-f2d7-11e9-8c50-09169988c220.png)


게시글을 등록하기 위해서는 Board 엔티티를 정의해야 합니다. 위 그림은 테이블을 기반으로 Board 엔티티를 정의하였습니다.

```java
package com.jun.jpacommunity.domain;

import com.jun.jpacommunity.domain.enums.BoardType;
import com.jun.jpacommunity.dto.BoardForm;
import lombok.*;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import javax.persistence.*;
import javax.validation.constraints.NotEmpty;
import java.sql.Timestamp;
import java.util.ArrayList;
import java.util.List;

@Entity
@Getter
@NoArgsConstructor(access = AccessLevel.PUBLIC)
@ToString(exclude = {"replies", "member"})
public class Board {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "board_id")
    private Long id;

    private String writer;

    @NotEmpty(message = "제목을 넣으셔야 합니다.")
    @Column(nullable = false)
    private String title;

    @NotEmpty(message = "내용을 넣어주셔야 합니다.")
    @Column(length = 500)
    private String content;

    @ManyToOne(fetch = FetchType.EAGER)
    @JoinColumn(name = "member_id")
    private Member member;

    @OneToMany(mappedBy = "board", fetch = FetchType.LAZY)
    private List<Reply> replies = new ArrayList<Reply>();

    @CreationTimestamp
    @Column(name = "sys_creation_date", nullable = false, updatable = false)
    private Timestamp createdAt;

    @UpdateTimestamp
    @Column(name = "sys_update_date", nullable = false)
    private Timestamp updatedAt;


    public static Board createBoard(final Long id, final String writer ,final String title, final String content, final Member member){
        Board board = new Board();
        board.id = id;
        board.writer = writer;
        board.title = title;
        board.content = content;
        board.member = member;
        return board;
    }

    // 게시글 업데이트를 수행하는 로직
    public void updateBoard(BoardForm boardForm){
        this.title = boardForm.getTitle();
        this.content = boardForm.getContent();
    }

    public void setMember(final Member member) {
        this.member = member;

        member.getBoards().add(this);

    }
}
```

## 댓글 엔티티 정의

```java
package com.jun.jpacommunity.domain;

import com.fasterxml.jackson.annotation.JsonIgnore;
import lombok.Getter;
import lombok.Setter;
import lombok.ToString;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import javax.persistence.*;
import java.sql.Timestamp;

@Getter
@Entity
@Setter
@ToString(exclude = "board")
public class Reply {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "reply_id")
    private Long id;

    private String content;

    private String replier;

    @JsonIgnore
    @ManyToOne
    @JoinColumn(name = "board_id")
    private Board board;

    @CreationTimestamp
    private Timestamp createdAt;

    @UpdateTimestamp
    private Timestamp updateAt;

}
```



위의 정의되어 있는 Board 클래스와 Reply 클래스의 어노테이션들이 어떻게 정의되었는지, 어떤 용도인지 알아보겠습니다.




## @Entity
JPA를 통해 ORM으로 관리할 클래스들을 명시적으로 Entity라고 선언하는 것입니다. 도메인을 Entity로 사용하기 위해서 필수로 입력해줘야 합니다.

## @Table
테이블 어노테이션은 엔티티와 매핑할 실제 데이터베이스 테이블을 지정합니다. 위에서 정의한 엔티티에서는 해당 어노테이션을 지정하지 않았기 때문에 ddl 자동 생성 기능을 사용시에 테이블에는 엔티티 명으로 Board 테이블이 생성이 됩니다.


### @NoArgsConstructor(access = AcessLevel.PROTECTED)
다음은 파라미터가 없는 기본생성자를 만들어주는 어노테이션입니다. Spring JPA의 구현체인 하이버네이트를 사용하기 위해서 반드시 기본생성자가 필요합니다. 하이버네이트는 해당 테이블의 데이터를 엔티티로 생성할때 리플렉션을 이용하여 해당 엔티티 타입정보를 알아내기 때문에 반드시 기본생성자가 필요합니다. 롬복을 사용하지 않고 Java로 표현하면 다음과 같이 protected로 선언하는 것과 같습니다.

```java
protected Board(){

}
```

여기에서 굳이 접근지정자 레벨을 Protected로 하는 이유는 어플리케이션 내에서 이유없이 도메인을 생성하는 것을 막기 위해서 입니다. Board가 만들어지기 위해서는 필수적인 정보가 있을 텐데 기본 생성자를 이용해서 객체를 만들면 불완전한 객체가 생성되기 때문입니다.

이러한 방법으로 Protected 생성자를 지정하면 같이 협업하는 개발자분들이 이 객체는 적어도 기본생성자로 생성하면 안되는 객체인지를 인식합니다.

## @Id
JPA에서 해당 필드에 기본키를 지정하기 위해서 해당 어노테이션을 지정해야 합니다.
참고로 JPA에서 엔티티를 관리하는 영역인 영속 컨텍스트에서 실제 엔티티를 Map처럼 key,value 방식으로 해당 엔티티들을 관리하는데 key 값이 @Id입니다. 이 값으로 해당 엔티티의 인스턴스를 조회합니다.

## @GeneratedValue(strategy = GenerationType.IDENTITY)
@GeneratedValue(strategy = GenerationType.IDENTITY) 지정하면 Mysql의 AUTO_INCREMENT 기능으로 데이터베이스가 기본키를 생성합니다. 예를 들어 게시글을 2개 생성하면 게시글을 생성할 때는 id가 1,2 값으로 순서대로 게시글 엔티티를 생성해줍니다.


## @Embedded
해당 어노테이션은 실제로 쓰진 않았지만 데이터의 응집력과 데이터 타입 규합을 높여주기 때문에 객체지향적으로 프로그래밍을 가능하게 해줍니다. 어떻게 응집력을 높이고, 데이터를 규합시키는지 아래 간단한 예를 설명하겠습니다.

## BoardType class

```java
package com.jun.jpacommunity.domain.enums;

import lombok.Getter;

@Getter
public enum BoardType {

    notice("공지사항"), free("자유게시판");

    private String value;

    BoardType(String value) {
        this.value = value;
    }
}
```

위의 코드같이 BoardType 클래스가 있습니다. 이것은 실제로 Board 엔티티에 Embedded 되어 있지 않지만 만약에 Embedded 되어 있다면 객체타입이기 때문에 JPA가 아무리 강력한 기능을 제공한다고 해도 쉽게 데이터베이스에 데이터를 CRUD 할 수는 없습니다. 

그리고 단순히 String이 아닌 BoardType를 클래스로 하는 것에 대해 과한편이 아니냐고 생각할 수 있지만 만약 BoardType를 검증하기 위해 @NotEmpty 같은 어노테이션을 사용하여 스프링에서 클라이언트로 넘어온 데이터가 해당 어노테이션 조건을 만족하는지 validation 체크를 해주고 유효하지 않으면 Exception을 발생시켜 클라이언트한테 유효하지 않다고 알려준다고 해봅시다.

또한 String으로 선언했고, Board 클래스가 아닌 다른 클래스에서 BoardType 클래스를 맴버 필드로 참조한다고 가정을 한다면 BoardType 클래스나 다른 클래스에도 똑같이 validation 체크 어노테이션을 설정해줘야 하는 번거로움이 있습니다.

그렇다고 해서 모든 것을 Embedded 타입으로 가져가자는 의미는 아닙니다. 의미있는 데이터끼리 또는 위와같이 기능의 확장성이 필요로하는 데이터를 잘 선별하여 Embedded 타입으로 뺄 수 있다고 생각합니다. 


## @CreationTimestamp, @UpdateTimestamp
어노테이션은 만들어진 시간과 데이터 변경시간을 자동으로 처리해주는 어노테이션입니다.


## @Column(name = "sys_creation_date", nullable = false, unique = true)
다음은 Spring JPA에서 제공해주는 DDL 생성 기능입니다. Spring JPA에서는 애플리케이션을 다시 시작할 때 도메인을 기반으로 이러한 DDL 문을 통해 테이블을 새롭게 만들 수 있습니다. 물론 이런 DDL 설정을 auto-create로 하지 않는다면 필요 없는 기능일 수 있습니다. 하지만 데이터베이스와 Entity에 DDL 설정을 동기화하는 것을 추천드립니다.

그래야 크리티컬한 버그 줄일 수 있다고 생각하기 때문입니다. 예를 들어 NULL이 들어가면 안 되는 칼럼인데 개발자도 모르게 칼럼에 NULL이 들어가고 있다고 가정해보겠습니다. 위와 같은 제약조건들이 없으면 이것을 인식하는데 오래 걸려 치명적인 버그로 이어질 수 있습니다. 차라리 DBMS 제약조건 Exception 나서 빠르게 버그를 수정하는 것이 낫습니다.

 Reply 엔티티에서 @JoinColumn은 외래키와 매핑할 컬러명을 지정하는 어노테이션입니다. 연관관계를 설정할때는 반드시 연관관계의 주인을 명시해줘야 합니다.
 저같은 경우에는 Board와 Reply 엔티티를 일대다 양방향 관계로 매핑하였지만.....
 객체 그래프 탐색을 어떻게 할지에 따라서 굳이 양방향 매핑을 할 필요가 없습니다. 왜냐하면 테이블에서는 외래키 하나로 양방향 참조가 가능하기 때문입니다.

 JPA 연관관계 매핑에 대해서는 제 블로그에 자세하게 나와있으니 참고하시면 될거같습니다.

[JPA - 엔티티 매핑](hhttps://webcoding-start.tistory.com/13?category=812494)


## 마치며
이장에서 엔티티 및 도메인을 정의하는 방법에 대해서 설명하였습니다. 사실 이장에서 객체지향도 중요하지만 JPA가 정확히는 JPA 구현체인 하이버네이트가 엔티티와 관계형 데이터베이스 사이에서 ORM 기술을 어떻게 구현하는지 설명하고 싶었습니다.

JPA가 런닝커브가 높은 편이지만 그만큼 강력한 기능을 지원하기 때문에 배워두면 정말 유용하게 사용할 수 있는 기술이라고 생각합니다. Mybatis로 실제 게시판과 댓글기능을 구현해 보았다면 JPA를 사용할때 얼마나 갓 기술인지 실감하게 됩니다. 물론 연관관계를 고민없이 무턱대고 사용하면 오히려 Mybatis 프레임워크보다 쓰는 것보다 못할수 있기 때문에 영속 컨텍스트와 Fetch 전략, 연관관계 매핑에 대한 이론과 실습을 충분히 해야된다고 생각합니다.

JPA를 충분히 익힌다면 객체지향적으로 프로그래밍을 하는데 최고의 서포터가 될 것 입니다.


# Step-03 게시판 Controller 만들기 : 표현영역 효율적으로 관리하기

# 표현 영역

표현 영역 -> 응용 영역(서비스) -> 도메인 -> 인프라스트럭쳐 영역으로 시작합니다.
표현 영역은 클라이언트에 요청을 받아 알맞은 응용서비스를 호출한 후 결과값을 클라이언트에게 보내주는 역할을 합니다. 표현 영역은 어플리케이션 시작인 만큼 아주 중요한 역할을 하는 영역이라고 생각합니다. 단순 컨트롤러로써 Request Data 값을 받아서 대충 서비스로 던지는 역할이라고 생각할 수도 있습니다.

하지만 표현영역에서 Request Data 값을 받을 때 아주 잘 받아야 합니다. 대충 받는 순간 그 뒤쪽 서비스 영역, 도메인 영역, 인프라스트럭쳐 영역이 힘들어지기 때문입니다.
특히 서비스 영역이 어려워집니다. 

* Request 값을 DTO로 명시적으로 받아서 응용 영역으로 넘깁니다.
* 클라이언트로 부터 넘어오는 데이터 validate 확실하게 하기
* 표현영역에서 사용되는 객체들을 Service에 넘기지 않기(ex : HttpServletRequest)
* 도메인 객체 바로 프론트엔드로 넘기지 않기

# 1. Request Data값 DTO로 명시적으로 받아서 응용 영역으로 넘기기
먼저 Request Data값을 명시적으로 받아야합니다. 이 문제는 같이 협업하는 사람뿐만 아니라 나중에 유지보수할때도 서비스 영역에서 힘들어 집니다. 컨트롤러에서 HttpServletRequest 값을 통해서 Request Body 값을 받거나, Map을 이용하거나,
RequestParam으로 모든 데이터를 받아서 처리하게 되면 그당시 소스를 코딩할  때는 기억하지만 금방 그 데이터들이 어떤 용도에 데이터들인지 잊어버리기 때문입니다.

```java
//Map으로 받는예제
 public Map<String, Object> postBoard(@RequestBody Map<String, Object> params){
     
     //something
 }
```


예를 들어서 다른 개발자가 이것을 유지 보수한다고 가정하면, 유지 보수하는 사람은 그 Map 객체가 어떻게 사용되는지 모르기 때문에 하나하나의 키값 등을 추적해야 이 Map 객체의 Request 데이터를 명세할 수 있습니다.

심지어 클라이언트로부터 넘겨받은 값을 Map을 이용하여 아래 코드와 같이 키 값이 state인 value를 꺼낸다고 해보겠습니다. 클라이언트에 요구 사항으로 인해 이 키값이 status로 바뀌었습니다. 그렇게 되면 서비스 단과 컨트롤러에서 2군 대 이상 쓰인다고 하면 이것을 하나하나 다 찾아서 수정해야 합니다. 이것은 type형이 아닌 String 값이기 때문에 추적하기도 힘듭니다. 검색해서 찾을 수도 있지만 프로젝트 내에 같은 단어가 있으면 이 또한 매우 까다롭게 됩니다.

```java
request.getParameter("state");
```

그렇기 때문에 아래와 같이 명시적으로 DTO를 만들고 데이터를 받는게 좋습니다. 이렇게 명시적으로 정의하게 되면 유지 보수 하는 사람 입장에서도 게시글을 등록하기 위해서 이러한 값들은 필수고 이러한 값은 선택인 것을 알 수 있습니다. 또한 요구 사항 등 변경에 의해 데이터가 추가되거나 하더라도 유연하게 변경할 수 있습니다. 위에서 말한 Request Body key 값이 변경된다고 해도 해당 키값이 변수이기 때문에 IDE를 이용해 전체 변경을 하면 되기 때문에 아주 쉽게 변경할 수 있습니다.

다음은 게시글을 등록하기 위한 BoardForm을 정의합니다.

```java
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@ToString(exclude = "member")
public class BoardForm {

    private Long id;

    private String writer;

    @NotBlank(message = "제목을 넣으셔야 합니다.")
    private  String title;

    private  String content;

    private Member member;


    public Board toEntity(){
        Board board = Board.createBoard(this.writer ,this.title, this.content, this.member);
        return board;
    }

}
```
```java
@Getter
@Entity
@Setter
@ToString(exclude = "board")
public class Reply {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "reply_id")
    private Long id;

    private String content;

    private String replier;

    @JsonIgnore
    @ManyToOne
    @JoinColumn(name = "board_id")
    private Board board;

    @CreationTimestamp
    private Timestamp createdAt;

    @UpdateTimestamp
    private Timestamp updateAt;

}
```


댓글 같은 경우에는 DTO로 Request Data를 받을지 도메인으로 값을 받을지 고민하다가 굳이 화면단에서 보여줄 데이터와 크게 다르지 않아서 도메인으로 값을 받기로 결정했습니다. 사실 위의 게시판 또한 크게 다르지 않아서 도메인으로 받으려고 했지만 유지 보수성이 좋은 코드를 작성하기 위해서 요청 데이터를 받은 DTO, 데이터베이스에 저장하기 위한 도메인모델, 화면단에 보여줄 Reponse 객체 등 총 3개의 영역으로 나누었습니다. 



 
# 2. 클라이언트로 부터 넘어오는 데이너 validate 확실하게 하기 
다음은 클라이언트로부터 넘어오는 값에 대해 필수 값과 값의 형태를 표현 영역에서 확실하게 validate를 진행해야 표현 영역 이후에 영역들이 안 힘들어 집니다. 
만약 표현 영역에서 필수 값, 값의 형식에 대한 validate 검사들이 제대로 되지 않는다면 서비스 영역과 도메인 영역은 계속해서 비즈니스 로직을 구현하는 도중에 NULL 체크를 하는 소스를 작성해야 합니다. 이렇게 되면 비즈니스 로직을 집중할 수 없게 되고 코드의 가독성이 떨어지게 되어 버그로 이어질 가능성이 커집니다.

```java
public void postBoard(final BoardForm boardForm){
    if(boardForm == null)
        throw new NullPointException("boardForm is null!");
}
```

물론 응용서비스를 실행하는 주체가 같은 응용서비스이거나, 파라미터로 전달받은 값이 응용영역에서 validate 처리를 해야 할 수도 있습니다. 하지만 이 문제도 처음 표현 영역에서부터 무결한 데이터를 받거나, 서비스 자체에서도 NULL을 지양하는 코드를 작성해나간다면 응용서비스 내에 validate 코드 자체를 많이 줄여 나가실 수 있습니다.

이러한 무결한 데이터를 보장하기 위해 표현영역에서부터 NULL이 들어오는 것을 방어해야 합니다. NULL을 방어하기 위해서 다음과 같이 Request Data에 대한 Validate 어노테이션을 사용하는 방법이 있습니다. @NotBlank라는 어노테이션을 사용하게 되면 NULL, "", 빈칸 값들이 서비스 영역으로 들어오게 하는 것을 막을 수 있습니다.

저 같은 경우에는 BindingResult 객체를 사용하여 클라이언트로부터 요청데이터를 받게 될때 스프링에서 @valid 어노테이션을 사용하여 해당 요청 데이터를 스프링이 검증을 하게 됩니다. 

```java
    @NotBlank(message = "제목을 넣으셔야 합니다.")
    private  String title;
```

```java
    @PostMapping
    public ResponseEntity<?> postBoard(@RequestBody @Valid final BoardForm boardForm, final BindingResult bindingResult){

        if(bindingResult.hasErrors()){
            return new ResponseEntity<>("제대로 입력하세요", HttpStatus.BAD_REQUEST);
        }

        Board board = boardForm.toEntity();
        boardService.save(board);

        return new ResponseEntity<>("{}", HttpStatus.CREATED);
    }
```

위 NotBlank 어노테이션을 사용하게 되면 invalid 한 값이 넘어오면 프론트엔드로 BadRequest(400)을 처리하게 됩니다.
이때 bindingResult 객체를 프론트엔드 쪽으로 함께보내주는데 이것을 세부적으로 처리 할 수 있도록 Error code를 함께 보내는 게 좋습니다.

만약 Request Data를 정상적으로 프론트엔드 쪽에서 넘어온다면 응답코드를 201로 보내 줍니다.


# 3. Domain 객체를 바로 프론트엔드로 넘기지 않고 DTO로 사용하기
도메인을 바로 클라이언트 쪽으로 넘기게 되면 다음과 같은 문제점들을 가집니다.

* 도메인의 중요한 데이터가 외부에 노출되어 질 수 있습니다.

현재는 간단하게 누구나 회원가입 없이 게시판을 등록하여 댓글을 작성할 수 있는 커뮤니티이지만 만약 회원가입을 통해서 글을 남기도록 한다고 가정한다면, 도메인 객체 자체를 리턴하게 된다면 회원의 개인정보, 패스워드 등이 로그, 클라이언트에 노출이 되어집니다. 물론 어노테이션을 통해서 중요한 필드가 노출되는것을 막을 수 있습니다. 하지만 어노테이션은 실수할 확률이 큽니다. 실수로 어노테이션을 삭제하게 되면 이 문제에 대해 쉽게 인지할 수 없기 때문입니다.

반면 DTO를 사용하게 되면 현저히 실수할 확률이 줄어들게 됩니다. 그 이유는 어노테이션의 방법은 DTO 방법과는 반대로 클라이언트에 표시되지 않기 위한 데이터에 대해 어노테이션을 표시하는 반면 DTO는 필요로 하는 데이터를 필드에 추가해야 하기 때문에 실수를 할 확률이 줄어들게 됩니다. 예를 들어 화면에 보여줄 데이터가 안 보이는 것은 DTO에 추가를 안 한 것이기 때문에 쉽게 에러를 잡을 수 있습니다. 또 한 화면에 보여야 할 데이터가 안보인다는 것은 대부분 Test Code 등으로 잡히는 문제입니다.


* 댓글 정보를 리턴시 무한순환참조 Exception을 발생하게 됩니다.

```java
@Getter
@Entity
@Setter
@ToString(exclude = "board")
public class Reply {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "reply_id")
    private Long id;

    private String content;

    private String replier;

    @JsonIgnore
    @ManyToOne
    @JoinColumn(name = "board_id")
    private Board board;

    @CreationTimestamp
    private Timestamp createdAt;

    @UpdateTimestamp
    private Timestamp updateAt;
}
```

위의 코드를 살펴보면 Rest API를 통해 클라이언트(웹브라우저)가 게시글의 댓글을 조회하는 기능이 있습니다. 이 Reply 엔티티를 클라이언트로 넘겨주기 위해서는 JSON 포맷으로 직렬화가 되어야 합니다. 문제는 Board 엔티티와 서로 다른 다대일 양방향 관계를 가지고 있기 때문에 서로 계속 참조하게 되어 무한 순환 참조 Exception이 발생하게 됩니다. 물론 이러한 문제를 어노테이션등을 통해 방지 할 수 도 있습니다. 


저 같은 경우에는 Reply 엔티티 같은 경우에 직렬화를 할때  Board 엔티티를  @JsonIgnore 어노테이션을 이용하여 JSON 포맷에서 빼도록 하였습니다. 하지만 이것도 좋은 방법은 아닙니다. 이유는 도메인은 하나인데 그 도메인 하나로 필드를 제어하려고 했기 때문입니다.

그렇기 때문에 실무에서는 더욱 복잡한 엔티티 연관관계를 가지고 있다면 DTO를 사용하는 것을 추천드립니다.


다음은 게시판에 게시글을 등록 후 Board 객체를 리턴하기 위해 사용되는 DTO입니다.

```java
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class BoardResponse {


    private Long id;

    private String writer;

    private String title;

    private String content;

    private Member member;

    private List<Reply> replies = new ArrayList<Reply>();

    private Timestamp createdAt;

    private Timestamp updatedAt;


    public BoardResponse(Board board){
        this.id = board.getId();
        this.writer = board.getWriter();
        this.title = board.getTitle();
        this.content = board.getContent();
        this.member = board.getMember();
        this.replies = board.getReplies();
        this.createdAt = board.getCreatedAt();
        this.updatedAt = board.getUpdatedAt();
    }
}
```


# 마치며
표현 영역에 대해 간단하게 알아보고 어떻게 하면 효율적으로 표현 영역을 관리할지 알아보았습니다. 위에서 말했듯이 표현 영역에서는 필수 값, 값의 형식 등을 검증을 확실히 해야만 응용서비스에서 반복된 로직이 나오지 않습니다. 그리고 응용 영역은 NULL에 대한 걱정, 데이터 format에 대한 걱정을 하지 않게 됨으로써 소스의 비지니스로직에 집중할 수 있고, 가독성이 높아집니다.

물론 응용서비스를 실행하는 주체가 같은 응용서비스이거나, 파라미터로 전달받은 값이 불안정하다면 응용 영역에서 validate 처리를 해야 할 수도 있습니다. 하지만 처음부터 무결한 데이터를 받거나, 응용서비스 자체에서도 최소한 NULL을 지양하는 코드를 작성해나간다면 NULL에 대한 검사 코드는 거의 없어질것입니다. 다른장에서 어떤식으로 NULL을 지양해나가는지 알아보겠습니다.


# Step-04 게시판 페이징 처리 구현하기 

# 응용 영역 : 페이징처리 기능

이 단계에서는 게시판에서 페이징 처리를 어떻게 객체 지향적으로 작성할지 알아보겠습니다.
페이징 처리는 주로 사용자가 게시판에 글을 작성하고 작성한 게시글 목록을 프론트엔드쪽으로 게시글을 몇건을 보여주고 페이지 화면을 어떻게 보여줄 것인지에 대한 모든것을 의미합니다.

먼저, 클라이언트(웹 브라우저)쪽에서 게시글 목록을 요청하는 Request Data가 오면 페이징 처리에 대한 정보를 PageVO 객체로 바인딩을 합니다. 만약 요청 URI에 페이징 처리에 대한 파라미터 없이 전송된다면 PageVO는 기본 생성자 호출시에 기본적으로 페이지 번호는 1, 페이지 크기는 10으로 초기화가 됩니다.

```java
@Getter
public class PageVO {

    private static final int DEFAULT_SIZE = 10;
    private static final int DEFAULT_MAX_SIZE = 50;

    private int page;
    private int size;

    public PageVO(){
        this.page = 1;
        this.size = DEFAULT_SIZE;
    }

    //파라미터로 페이지 번호가 넘어오면 설정자로 인스턴스 변수에 값을 대입
    public void setPage(int page) {
        this.page = page;
    }

    // 페이지 크기에 대한 파라미터 값이 넘어오면 해당 값으로 사이즈를 설정
    // 최대 크기는 50을 넘을 수가 없도록 설계하였습니다.
    public void setSize(int size) {
        this.size = size < DEFAULT_SIZE || size > DEFAULT_MAX_SIZE ? DEFAULT_SIZE : size;
    }

    public Pageable makePageable(int direction, String... props){

        Sort.Direction dir = direction == 0 ? Sort.Direction.DESC : Sort.Direction.ASC;
        
        // 실제 페이지 번호는 내부적으로 0번 index부터 시작합니다.
        // 사용자 입장에서는 페이지 번호가 직관적이지 않기 때문에 -1로 리턴
        return PageRequest.of(this.page - 1, this.size, dir, props);
    }
}
```

이 프로젝트는 웹 서버에서 간단한 게시판 커뮤니티를 구현하는게 목적이기 때문에 페이징 처리에 유용한 Pageable 인터페이스를 사용하였습니다. 

> 스프링 부트 2.0의 경우 Pageable 인터페이스를 구현하는 PageRequest 객체를     생성할때 PageRequest.of() 메소드를 사용해야 합니다.

PageVO 객체는 페이지 번호와 크기를 설정하고, 정렬 방향과 정렬 기준 정보를 가진 Pageable 인터페이스를 구현하는 PageRequest 객체를 생성해주는 메소드를 가지고 있습니다.


```java
 @GetMapping("/list")
    public String boardList(PageVO pageVO, Model model, @ModelAttribute("boardSearch") BoardSearch boardSearch){


        // 페이징 처리에 필요한 정보를 제공함.
        // 표현계층에서 정렬 방향과 정렬 대상을 처리하는 부분
        Pageable page = pageVO.makePageable(0,"id");

        //Page<T> 타입을 이용하면 Spring MV와 연동할 때 상당한 편리함을 제공해줍니다. 단순 데이터 추출용도가 아니고 웹에서 필요한 데이터들을 추가적으로 처리해줍니다.
        Page<Board> boards = boardService.findAll(boardService.makePredicate(boardSearch), page);

        log.info("" + page);
        log.info("" + boards);
        log.info("" + boardSearch.getType());
        log.info("" + boardSearch.getKeyword());

        model.addAttribute("boardList", new PageMaker<>(boards));

        return "board/list";
    }
```

이제 페이징 처리에 대한 정보를 가진 객체를 만들었으니 실제로 Repository에서 Board 엔티티를 조회할때 해당 페이징 처리정보를 참조하여 JPA에서 쿼리를 날리때 limit이 적용되는 것을 확인 할 수 있습니다. 

Repository의 findAll() 메소드는 Pageable 타입이 매게변수일때 다음과 같이 Page<T> 타입의 객체를 리턴하게 되는데 Spring Data JPA에서 결과 데이터가 여러 개인 경우 List<T> 타입을 이용하기도 하지만, Page<T> 타입을 이용하면 Spirng MVC와 연동할 때 상당한 편리함을 제공합니다. 

```java
Pageable page = boardRepository.findAll(Pageable page)
```

### **Page\<T>** 가 제공하는 메소드 입니다.

| 메소드               | 설명                                                                                                                                                                                                       |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **int getNumber()**           | 현재 페이지의 정보 |
| **int geSize()**           | 한 페이지의 크기                                                                                              |
| **int getTotalPages()**         | 전체 페이지의 수                              
+                                                                        |
| **List\<T> getContent()** | 조회된 데이터 | 


# 응용 영역 : 검색 기능
두번째로 검색 기능에 대해서 살펴보겠습니다. 클라이언트에서 요청한 검색 정보를 바인딩할 BoardSearch 클래스를 정의하였습니다.

```java
@Getter
@Setter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class BoardSearch {

    private String type;
    private String keyword;
}

```


![스크린샷 2019-10-23 오전 1 05 56](https://user-images.githubusercontent.com/22395934/67306058-4fa61d80-f531-11e9-9ce0-f0f5f7607c64.png)


제목, 내용, 작성자 같은 검색 타입을 선택하고 키워드를 입력하여 검색을 요청하면 BoardSearch 객체에 바인딩 됩니다.

바인딩 되어진 boardSearch 객체는 응용 계층으로 값이 전달 됩니다.
응용 계층(서비스)에서는 검색정보를 가진 boardSearch를 아큐먼트로 받아서 Predicate 타입의 인터페이스를 구현하는 BooleanBuilder 객체를 리턴하는데 이 객체의 역할은 동적 쿼리를 생성하는 책임을 가지고 있는 객체입니다.

동적쿼리 생성부분은 아래 URL을 참조하시면 됩니다.

[동적 쿼리 : QueryDSL](https://jojoldu.tistory.com/372)

```java
Service
@RequiredArgsConstructor
@Transactional(readOnly = true)
@Slf4j
public class BoardService {

    private final BoardRepository boardRepository;

    public Predicate makePredicate(final BoardSearch boardSearch){
        return boardRepository.makePredicate(boardSearch);
    }
```

실제로 아래 코드에서 BoardRepository가 QueryDSL 의존성 라이브러리를 이용하여 동적으로 쿼리를 생성하는 부분입니다. 

```java
public interface BoardRepository extends JpaRepository<Board, Long>, QuerydslPredicateExecutor<Board> {

    @Query("select count(r) from Board b " + " JOIN b.replies r where b.id = ?1")
    public int getReplyCount(Long count);

    // 자바 8부터 인터페이스에 디폴트 메소드가 추가가 가능해졌습니다.
    public default Predicate makePredicate(BoardSearch boardSearch) {

        String type = boardSearch.getType();
        String keyword = boardSearch.getKeyword();

        // 메소드 체인형식으로 동적으로 조건절을 추가해주는 BooleanBuilder 객체 생성
        BooleanBuilder builder = new BooleanBuilder();

        QBoard board = QBoard.board;

        builder.and(board.id.gt(0));

        System.out.println( "type:" + type);
        System.out.println( "keyword:" + keyword);


        if(type == null){
            return builder;
        }
        // type 값에 따른 switch 문 수행
        switch(type){
            case "t":
                builder.and(board.title.like("%" + keyword + "%"));
                break;

            case "c":
                builder.and(board.content.like("%" + keyword + "%"));
                break;

            case "w":
                builder.and(board.writer.like("%" + keyword + "%"));
                break;
        }
        return builder;
    }
}
```

# QueryDSL 장점

1. QueryDsl을 사용하면 문법적으로 잘못된 쿼리를 허용하지 않습니다.
2. IDE 코드 자동완성 기능 사용
3. 도메인 타입과 Property를 안전하게 참조할 수 있습니다.
4. 도메인 타입의 리펙토링을 더 잘 할 수 있습니다.


> 한마디로 QueryDSL은 타입에 안전한 방식으로 쿼리를 실행하기 위한 목적으로         만들어졌습니다. 그것이 가능한 이유는 and(), like()와 같은 문자열이 아닌 메서드   호출 방식으로 쿼리가 수행되기 때문입니다.


이렇게 Predicate, Pageable 인터페이스를 구현한 객체들을 Repository 메소드의 매게변수로 넣어주므로써 페이징처리와 검색처리를 하는 기능이 대부분 구현되었습니다.

이제 클라이언트(웹 브라우저)쪽으로 페이징 화면을 어떻게 보여주는지 살펴보겠습니다.

```java
page<Board> boards = boardService.findAll(boardService.makePredicate(boardSearch), page);

    
model.addAttribute("boardList", new PageMaker<>(boards));
```

model 객체를 이용해서 뷰에 보내줄 PageMaker 객체를 생성하여 보내줍니다. PageMaker는 페이지 화면을 클라이언트가 전송한 페이지 번호에 따라서 페이지 화면 계산을 처리해주는 역할을 하는 객체입니다.

```java
@Getter
@ToString(exclude = "pageList")
public class PageMaker<T> {

    
    private Page<T> result;
    //이전 페이징처리 정보
    private Pageable prevPage;
    //다음 페이징처리 정보
    private Pageable nextPage;
    //현재 페이지 번호
    private int currentPageNum;
    //전체 페이지 개수
    private int totalPageNum;
    //현재 페이징처리 정보
    private Pageable currentPage;

    //실제 화면에서 보여줄 페이지 번호들을 가진 Collection 타입의 객체
    private List<Pageable> pageList;

    public PageMaker(Page<T> result) {
        this.result = result;

        this.currentPage = result.getPageable();

        this.currentPageNum = result.getNumber() + 1;

        this.totalPageNum = result.getTotalPages();

        this.pageList = new ArrayList<>();
        calcPages();
    }
    
    // 페이지 화면 처리하는 부분
    private void calcPages(){

        // 페이지 끝 번호를 나타냅니다.
        int tempEndNum = (int)(Math.ceil(this.currentPageNum/10.0)* 10);
        // 페이지 시작번호 
        int startNum = tempEndNum -9;

        Pageable startPage = this.currentPage;

        //뷰 화면에서 보여줄 페이지 번호를 계산하는 부분
        for(int i = startNum; i < this.currentPageNum; i++){
            startPage = startPage.previousOrFirst();
        }

        // 계산한 페이지 번호가 0이하면 prevPage 객체는 null값으로 
        // 이전 페이지 버튼을 보여주지 않기 위해 처리하는 부분입니다.
        this.prevPage = startPage.getPageNumber() <= 0? null :startPage.previousOrFirst();

        // 만약 클라이언트에서 전체 페이지 번호보다 큰 번호를 요청했을 시
        // 끝 페이지 번호가 전체 페이지보다 커지기 때문에 필요한 로직입니다.
        if(this.totalPageNum < tempEndNum){
            tempEndNum = this.totalPageNum;
            this.nextPage = null;
        }

        // 실제로 화면에 보여줄 페이지 번호들을 가지고 있는 List 객체에 
        // 페이징 처리 정보를 넣어주는 로직입니다.
        for(int i = startNum ; i <= tempEndNum; i++){
            pageList.add(startPage);
            startPage = startPage.next();
        }
        //끝 번호까지 보여준 후에 다음 페이지 번호를 보여주기 위한 로직
        //startPage가 전체 페이지 번호 이상이면 끝이기 때문에 null 값을 넣어줍니다.
        this.nextPage = startPage.getPageNumber() +1 < totalPageNum ? startPage: null;

    }
}
```

실제로 calc() 메소드는 클라이언트에서 요청한 페이지 번호에 따른 페이지 화면을 처리해주는 메소드입니다. 페이징 처리를 할때 기본적으로 10개씩 처리하기로 설정했기 때문에 시작페이지와 끝페이지는 기본적으로 9정도 차이가 납니다.







옵션 | 설명 | 
:--------|:--------:|--------:| 
| create | 기존 테이블을 삭제하고 새로 생성한다. DROP + CREATE 
| create-drop| create 속성에 추가로 어플리케이션을 종료할 때 생성한 DDL을 제거한다. DROP + CREATE + DROP 
| update | 데이터베이스 테이블과 엔터티 매핑정보를 비교해서 변경 사항만 수정한다. 
| validate | 데이터베이스 테이블과 엔터티 매핑정보를 비교해서 차이가 있으면 경고를 남기고 애플리케이션을 실행하지 않는다. 이설정은 DDL을 수정하지 않는다. 
| none | 자동 생성 기능을 사용하지 않으려면 hibernate.ddl-auto 속성 자체를 삭제하거나 유효하지 않는 옵션 값을 주면 된다.


@Table은 엔터티와 매핑할 테이블을 지정한다. 생략하면 매핑한 엔터티 이름을 테이블 이름으로 사용합니다. 

|  <center>옵션</center> |  <center>설명</center> | 
|:--------|:--------:|--------:|
| create | <center>기존 테이블을 삭제하고 새로 생성한다. DROP + CREATE</center> | <center>엔터티 이름을 사용한다.</center> |
| create-drop | <center>create 속성에 추가로 어플리케이션을 종료할 때 생성한 DDL을 제거한다. DROP + CREATE + DROP</center> 
| update | <center>데이터베이스 테이블과 엔터티 매핑정보를 비교해서 변경 사항만 수정한다. </center> 
| validate | <center>데이터베이스 테이블과 엔터티 매핑정보를 비교해서 차이가 있으면 경고를 남기고 애플리케이션을 실행하지 않는다. 이설정은 DDL을 수정하지 않는다..</center> 
| none | <center>자동 생성 기능을 사용하지 않으려면 hibernate.ddl-auto 속성 자체를 삭제하거나 유효하지 않는 옵션 값을 주면 된다.</center> 