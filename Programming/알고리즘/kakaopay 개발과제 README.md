## 카카오페이 서버개발 과제

카카오페이 서버개발 과제로 REST API를 이용한 투자 서비스 기능을 개발하였습니다. 전체적으로  만든 API 기능들은 아래와 같습니다.

- 전체 투자 상품 조회 API
- 투자하기 API
- 나의 투자 상품 조회 API

프로그래밍 언어로는 Java를 사용하였고, 빌드 툴로는 Gradle, 웹 애플리케이션 개발을 위한 프레임 워크로 스프링 부트와 DB는 Java 진영의 ORM 스펙인 JPA 구현체인 하이버네이트를 이용하여 DB와 엔티티를 관리하도록 설계하였습니다. 여기에 조회 리포지토리용으로 Querydsl 플러그인을 사용하여 최대한 객체지향적으로 JPQL로 쿼리를 조회하는 기능을 구현하였습니다.

### 클래스 다이어 그램

![image](https://user-images.githubusercontent.com/22395934/111059931-8b51d680-84dc-11eb-87c5-bdf5d9c0621d.png)

도메인은 크게 회원 엔티티, 투자상품 엔티티, 상품 엔티티로 분류하였습니다.

회원이 상품을 여러개를 투자할 수 있고, 상품 또한 여러 투자자들이 투자할 수 있기 때문에 가운데에 투자 상품 엔티티를 만들어서 회원(1) <-> (*)투자 상품, 투자상품(*) <-> 회원(1)로 연관관계를 설정하였습니다.

Java에서는 DB와의 패러다임 불일치로 인해서 참조를 통해서 연관관계를 맺을 수 있기 때문에 저 같은 경우에는 투자 상품 엔티티에서 회원 엔티티를 참조하는 단방향 연관관계로 매핑을 하였고, 상품 엔티티도 마찬가지로 단방향으로 참조하도록 설계 하였습니다. 만약 상품이나 회원 엔티티에서 투자 상품 정보를 가져올 필요가 있다면 그때는 양방향으로 연관관계를 매핑해주도록 관계를 설정해주면 되기 때문입니다.

### MaraiDB 테이블 구조

![image](https://user-images.githubusercontent.com/22395934/111060348-9b1eea00-84df-11eb-818e-8d441d5f1211.png)


### 사용한 기술 및 툴

- Spring Boot
- Spring Data JPA
- QueryDSL
- MariaDB
- JAVA8
- Intellij IDEA Ultimate
- Gradle

### 투자 상품 REST API 서비스 개발 전략

#### 1. Clinet에서 전달한 Request Data값을 DTO로 받아서 응용 계층에 전달하기

이렇게 DTO로 받는 이유는 만약 HttpServletRequest 객체를 통해서 Request Body 값을 받거나, Map 혹은 @RequestParam으로 모든 데이터를 받아서 처리하게 되면 현재는 기억할지라도 나중에 이 데이터가 어떤 용도인지 잊어버리기 때문입니다.

아래 DTO 클래스는 전체 투자상품 조회 API에서 클라이언트에게 전달받는 투자 모집기간이 포함된 파라미터들을 담고 있는 DTO 객체입니다.

#### Request Data를 담는 DTO 객체

```java
@ToString
@Getter
public class InvestmentItemDateReq {
    @NotBlank(message = "투자 공모 모집 날짜를 입력하세요.")
    private String startedAt;
    @NotBlank(message = "투자 공모 모집 마감 날짜를 입력하세요.")
    private String finishedAt;
}
```

### Response Data를 담는 DTO 객체

```java
@ToString
@NoArgsConstructor
@AllArgsConstructor
@Getter
@Setter
public class InvestmentItemDateRes {
    private Long id;
    private String title;
    private Long totalInvestingAmount;
    private Long currentInvestingAmount;
    private Long investorCount;
    private ItemStatus investingStatus;
    private LocalDateTime startedAt;
    private LocalDateTime finishedAt;
}
```

### 2.파라미터 검증 전략

표현 계층에서는 서비스 계층으로 DTO 객체를 전달하기 전에 검증 작업이 필요하다고 생각합니다. 그래서 클라이언트로 전달받은 파라미터들을 검증하기 위해서 어떤 방법을 써야할지 고민을 하였습니다. 별도의 파라미터를 검증하는 역할을 가진 서비스 클래스를 정의할까 고민하다가 공통으로 검증할 수 있는 방법이 떠오르지 않아서 Bean Validation을 사용하기로 결정하였습니다.

```java
@ToString
@Getter
@Setter
public class InvestmentItemReq {

    @NotNull(message = "상품 id는 필수입니다.")
    private Long itemId;
    @NotNull(message = "투자 금액은 필수입니다.")
    private Long investingAmount;
}
```

#### ControllerAdvice에서 예외 처리 수행

```java
@ExceptionHandler(MethodArgumentNotValidException.class)
@ResponseBody
public Object handleMethodArgumentNotValidException(MethodArgumentNotValidException e) {
    String errorMessage = e.getBindingResult()
            .getAllErrors()
            .get(0)
            .getDefaultMessage();

    log.error("MethodArgumentNotValidException: {}", e);
    ErrorResponse responseError = new ErrorResponse(errorMessage, HttpStatus.BAD_REQUEST.value());
    return new ResponseEntity<>(responseError, HttpStatus.BAD_REQUEST);
}
```

위의 코드는 만약 ControllerAdvice 내에 Bean Validation에 대한 오류가 발생하였을 경우 처리하기 위한 예외처리 메서드입니다.

DTO에서 @NotNull attribute인 message 값을 받아서 ErrorResponse 객체에 담아서 JSON 포맷으로 클라이언트에게 전달해주도록 구현하였습니다.

### 3.조회 리포지터리 전략

투자 모집 기간 (started_at, finished_at) 데이터를 쿼리 질의 값으로 동적으로 사용하기 위해서 QueryDSL을 사용하였습니다. 기존의 JPA JPQL 표준인 Criteria에 비해서 JPQL 작성 시 더 좋은 가독성을 제공해주면서, 컴파일 시점에서 문법 오류를 발견할 수 있는 이점이 있어서 사용하였습니다.


```java
@RequiredArgsConstructor
@Repository
public class InvestmentQueryRepository {

    private final JPAQueryFactory queryFactory;

    public List<InvestmentItemDateRes> getInvestmentItemsByDate(LocalDateTime startDateTime, LocalDateTime finishedDateTime) {
        List<InvestmentItemDateRes> result = queryFactory.select(Projections.constructor(InvestmentItemDateRes.class,
                item.id.as("id"),
                item.title.as("title"),
                item.total_investing_amount.as("totalInvestingAmount"),
                investmentItem.investing_amount.sum().coalesce(0L).as("currentInvestingAmount"),
                investmentItem.count().as("investorCount"),
                item.itemStatus.as("investingStatus"),
                item.startedAt,
                item.finishedAt))
                .from(investmentItem)
                .join(investmentItem.item, item)
                .where(item.startedAt.goe(startDateTime).and(item.finishedAt.loe(finishedDateTime)))
                .groupBy(item.id)
                .fetch();
        return result;
    }
}
```

위의 코드를 보면 JpaQueryFactory 객체를 사용하여 동적 쿼리를 작성하는 메서드를 구현했습니다. 리턴 값으로 `List<InvestmentDateRes>`을 리턴하였습니다.

이렇게 한 이유는 DB에서 조회 결과를 엔티티로 리턴할 경우 영속성 컨텍스트에 별도의 스냅샷 객체를 만들지 않아서 성능적인 이점을 누릴 수 도 있고, API 스펙이 변경 될 경우(리턴 값이 추가되거나 바뀔 경우) DTO 코드만 수정하면 되기 때문에 리포지터리 계층에 영향을 주지 않은 장점이 있습니다. 이러한 이유 때문에 DB 조회 결과를 DTO로 변환해서 데이터를 응답하도록 설계하였습니다. 


## 테스트 코드 작성

투자 REST API 서비스 기능을 작성하면서 주로 표현 계층, 응용 계층, 리포지터리 계층을 위주로 JUnit4 프레임워크를 이용하서 테스트 코드를 작성하였습니다.

## MockMvc 객체를 이용하여 표현 계층 테스트 코드 작성

요청 데이터의 content-type은 `json`이고, 응답 역시 `json`으로 리턴하도록 API를 정의하였습니다.

```java
@RunWith(SpringRunner.class)
@WebMvcTest
public class InvestmentRestControllerTests {

    @Autowired
    private MockMvc mockMvc;

    @Test
    public void 투자결과_리턴_테스트() throws Exception {

        //given
        JSONObject jsonObject = new JSONObject();
        jsonObject.put("itemId", 2);
        jsonObject.put("investingAmount", 10000);
        String jsonBody = jsonObject.toString();

        //when
        //then
        mockMvc.perform(post("/api/investment")
                .content(jsonBody)
                .header("X-USER-ID", 1)
                .contentType(MediaType.APPLICATION_JSON)
                .accept(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.message", is("sold-out")));
    }
}
```

위의 테스트 코드는 `/api/investment` URL로 요청을 할 경우 해당 식별값을 가진 사용자가 특정 상품에 투자를 하는 REST API를 작성한 테스트 코드입니다. 만약 사용자의 투자 금액이 상품의 총 투자 모집 금액을 넘는 경우에는 `sold-out`이라는 json 포맷의 리턴 값이 정상적으로 들어오는지 테스트를 하였습니다.

## 리포지터리 테스트 코드 작성 

회원 엔티티, 상품 엔티티와 단방향으로 연관관계를 가진 투자상품 엔티티를 DB 테이블에 저장하는 테스트 코드를 작성하였습니다. EntityManager 객체를 통해서 회원 엔티티와 상품 엔티티를 미리 저장 후 투자 상품 엔티티를 생성 후 연관관계를 설정하고 정상적으로 해당 엔티티들과 매핑이 되었는지 테스트를 하는 코드입니다. 

```java
@Transactional
@RunWith(SpringRunner.class)
@SpringBootTest
public class InvestmentItemRepoTests {

    private final Logger logger = LoggerFactory.getLogger(InvestmentItemRepoTests.class);

    @Autowired
    private EntityManager em;

    @Before
    public void setUp() throws Exception {
        //given
        Member member = Member.builder()
                              .name("임준영")
                              .email("a79007714@gmail.com")
                              .build();
        Item item = Item.builder()
                        .title("개인신용 포트폴리오")
                        .total_investing_amount(1000000L)
                        .itemStatus(ItemStatus.IN_PROGRESS)
                        .build();

        InvestmentItem investmentItem = InvestmentItem.builder()
                                                      .investing_amount(100000L)
                                                      .build();
        
        investmentItem.addItem(item);
        investmentItem.addMember(member);

        //when
        //then
         em.persist(member);
         em.persist(item);
         em.persist(investmentItem);

        logger.info("Persistence Context End!!!");
     }

     @Test
     public void investmentItem_조회_테스트() throws Exception {
         //given
         InvestmentItem investmentItem = em.find(InvestmentItem.class, 1L);

         //when
         logger.info("investingAmount: {}",investmentItem.getInvesting_amount());
         logger.info("itemTitle: {}", investmentItem.getItem().getTitle());
         logger.info("email: {}", investmentItem.getMember().getEmail());

         //then
         Assertions.assertThat(investmentItem.getInvesting_amount()).isEqualTo(100000L);
         Assertions.assertThat(investmentItem.getItem().getTitle()).isEqualTo("개인신용 포트폴리오");
         Assertions.assertThat(investmentItem.getMember().getEmail()).isEqualTo("a79007714@gmail.com");
      }
}
```

## 예외 처리를 위한 RestControllerExceptionAdvice 클래스 작성

각 계층마다 발생할 수 있는 예외들을 전역으로 관리하기 위해서 스프링에서 제공해주는 @ControllerAdvice 어노테이션을 사용하였습니다.

Checked Exception과 UnChecked Exception 대한 처리를 기술하였습니다. 

```java
@Slf4j
@ControllerAdvice
public class RestControllerExceptionAdvice {

    @ExceptionHandler(value = MemberNotFoundException.class)
    @ResponseBody
    public ResponseEntity<ErrorResponse> handleMemberNotFound(MemberNotFoundException e) {
        log.error("Member is not exist", e);
        ErrorResponse responseError = new ErrorResponse(e.getMessage(), HttpStatus.NOT_FOUND.value());
        return new ResponseEntity<>(responseError, HttpStatus.NOT_FOUND);
    }

    @ExceptionHandler(value = ItemNotFoundException.class)
    @ResponseBody
    public ResponseEntity<ErrorResponse> handleItemsNotFound(ItemNotFoundException e) {
        log.error("Item is not exist", e);
        ErrorResponse responseError = new ErrorResponse(e.getMessage(), HttpStatus.NOT_FOUND.value());
        return new ResponseEntity<>(responseError, HttpStatus.NOT_FOUND);
    }

    @ExceptionHandler(value = Exception.class)
    @ResponseBody
    public ResponseEntity<ErrorResponse> handleException(Exception e) {
        log.error("Exception: {}", e);
        ErrorResponse responseError = new ErrorResponse(e.getMessage(), HttpStatus.INTERNAL_SERVER_ERROR.value());
        return new ResponseEntity<>(responseError, HttpStatus.NOT_FOUND);
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    @ResponseBody
    public Object handleMethodArgumentNotValidException(MethodArgumentNotValidException e) {
        String errorMessage = e.getBindingResult()
                .getAllErrors()
                .get(0)
                .getDefaultMessage();

        log.error("MethodArgumentNotValidException: {}", e);
        ErrorResponse responseError = new ErrorResponse(errorMessage, HttpStatus.BAD_REQUEST.value());
        return new ResponseEntity<>(responseError, HttpStatus.BAD_REQUEST);
    }
}
```

해당 Exception 발생 시 상태코드와 메시지를 담는 ErrorResponse 객체를 생성해서 클라이언트쪽에 JSON 포맷으로 리턴하도록 설계하였습니다.

```java
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@AllArgsConstructor
public class ErrorResponse {
    private Integer code;
    private String message;
}
```




