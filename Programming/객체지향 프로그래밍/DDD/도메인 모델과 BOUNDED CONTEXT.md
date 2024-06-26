# 도메인 모델과 BOUNDED CONTEXT

## 도메인 모델과 경계

처음 도메인 모델을 만들 때 빠지기 쉬운 함정이 도메인을 완벽하게 표현하는 단일 모델을 만드는 시도를 하는 것입니다. 그런데 한 도메인은 다시 여러 하위 도메인으로 구분되기 때문에 한 개의 모델로 여러 하위 도메인을 모두 표현하려고 시도하게 되면 모든 하위 도메인에 맞지 않는 모델을 만들게 됩니다.

예를 들어, 상품이라는 모델을 생각해보겠습니다. 카탈로그에서의 상품, 재고 관리에서의 상품, 주문에서의 상품, 배송에서의 상품은 이름만 같지 실제로 의미하는 것이 다릅니다. 카탈로그에서의 상품은 상품 이미지, 상품명, 상품 가격, 옵션 목록, 상세 설명과 같은 상품 정보가 위주라면, 재고 관리에서의 상품은 실존하는 개별 객체를 추적하기 위한 목적으로 상품을 사용합니다. 즉, 카탈로그에서 물리적으로 한 개인 상품이 재고관리에서는 여러개 존재할 수 있습니다.

논리적으로 같은 존재처럼 보이지만 하위 도메인에 따라 다른 용어를 사용하는 경우도 있습니다. 카탈로그 도메인에서 상품이 검색 도메인에서는 문서로 불리기도 합니다. 
비슷하게 시스템을 사용하는 사람을 회원 도메인에서는 회원이라고 부르지만, 주문 도메인에서는 주문자라고 부르고, 배송 도메인에서는 보내는 사람이라고 부르기도 합니다.

![image](https://user-images.githubusercontent.com/22395934/102223545-32e45a80-3f28-11eb-96c3-3debb1249ed7.png)

이렇게 하위 도메인 마다 같은 용어라도 의미가 다르고 같은 대상이라도 지칭하는 용어가 다를 수 있기 때문에 한 개의 모델로 모든 하위 도메인을 표현하려는 시도는 올바른 방법이 아니며 표현할 수도 없습니다.

하위 도메인마다 사용하는 용어가 다르기 때문에 올바른 도메인 모델을 개발하려면 하위 도메인 마다 모델을 만들어야 합니다. 각 모델은 명시적으로 구분되는 경계를 가져서 섞이지 않도록 해야합니다. 여러 하위 도메인 모델이 섞이기 시작하면 모델의 의미가 약해질 뿐만 아니라 여러 도메인의 모델이 서로 얽혀 있기 때문에 각 하위 도메인별로 다르게 발전하는 요구사항을 모델에 반영하기 어려워 집니다.

모델은 특정한 컨텍스트 하에서 완전한 의미를 갖습니다. 같으느 제품이라도 카탈로그 컨텍스트와 재고 컨텍스트에서 의미가 서로 다릅니다. 이렇게 구분되는 경계를 갖는 DDD에서는 BOUNDED CONTEXT라고 부릅니다.

>>BOUNDED CONTEXT를 우리말로 번역하면 경계를 갖는 컨텍스트라고 다소 길게 번역할 수 있지만 DDD 용어가 잘 드러나지 않습니다.

## BOUNDED CONTEXT

BOUNDED CONTEXT는 모델의 경계를 결정하며 한 개의 BOUNDED CONTEXT는 논리적으로 한 개의 모델을 갖습니다. BOUNDED CONTEXT는 용어를 기준으로 구분합니다. 카탈로그 컨텍스트와 재고 컨텍스트는 서로 다른 용어를 사용하므로 이 용어를 기준으로 컨텍스트를 분리할 수 있습니다. 또한, BOUNDED CONTEXT는 실제로 사용자에게 기능을 제공하는 물리적 시스템으로 도메인 모델은 이 BOUNDED CONTEXT안에서 도메인을 구현합니다.

이상적으로 하위 도메인과 BOUNDED CONTEXT가 일대일 관계를 가지면 좋겠지만 현실은 그렇지 않을 때가 많습니다. BOUNDED CONTEXT가 기업의 팀 조직 구조에 따라 결정되기도 합니다. 예를 들어, 주문 하위 도메인이라도 주문을 처리하는 팀과 복잡한 결제 금액 계산 로직을 구현하는 팀이 따로 있다고 해봅시다. 이 경우 주문 하위 도메인에 주문 BOUNDED CONTEXT와 결제 금액 계산 BOUNDED CONTEXT가 존재하게 됩니다. 아직 용어를 명확하게 하지 못해 두 하위 도메인을 BOUNDED CONTEXT라고에서 구현하기도 합니다. 카탈로그와 재고 관리가 아직 명확하게 구분되지 않은 경우 도 하위 도메인을 한 BOUNDED CONTEXT에서 구현하기도 합니다.

규모가 작은 기업은 전체 시스템을 한 개 팀에서 구현할 때도 있습니다. 예를 들어, 소규모 소핑몰을 운영할 경우 한 개의 웹 애플리케이션으로 온라인 쇼핑을 서비스 합니다. 이 경우 하나의 시스템에서 회원, 카탈로그, 재고, 구매, 결제와 관련된 기능을 제공합니다. 즉, 여러 하위 도메인을 한 개의 BOUNDED CONTEXT에서 구현합니다.

여러 하위 도메인을 하나의 BOUNDED CONTEXT에서 개발할 때 주의할 점은 하위 도메인 모델이 뒤섞이지 않도록 하는 것입니다. 한 개의 이클립스 프로젝트에 각 하위 도메인 모델이 위치하면 아무래도 전체 하위 도메인을 위한 단일 모델을 만들고 싶은 유혹에 빠지기 쉽습니다. 이런 유혹에 걸려들면 결과적으로 도메인 모델이 개별 하위 도메인을 제대로 반영하지 못해서 하위 도메인 별 기능 확장이 어렵게 되고 이는 서비스의 경쟁력을 떨어뜨리는 원인이 됩니다. 따라서, 비록 한개의 BOUNDED CONTEXT에서 여러 하위 도메인을 포함하더라도 하위 도메인마다 구분되는 패키지를 갖도록 구현해야 하위 도메인을 위한 모델이 서로 뒤섞이지 않아서 하위 도메인마다 BOUNDED CONTEXT를 갖는 효과를 낼 수 있습니다.


## BOUNDED CONTEX의 구현

BOUNDED CONTEXT가 도메인 모델만 포함하는 것은 아닙니다. BOUNDED CONTEXT는 도메인 모델뿐만 아니라 도메인 기능을 사용자에게 제공하는 데 필요한 표현 영역, 응용 서비스, 인프라 영역 등을 모두 포함합니다. 도메인 모델의 데이터 구조가 바뀌면 DB 테이블 스키마도 함께 변경해야 하므로 해당 테이블도 BOUNDED CONTEXT에 포함됩니다.

표현 영역은 인간 사용자를 위해 HTML 페이지를 생성할 수도 있고 다른 BOUNDED CONTEXT를 위해 REST API를 제공할 수도 있습니다.

모든 BOUNDED CONTEXT를 반드시 도메인 주도로 개발할 필요는 없습니다. 상품의 리뷰는 복잡한 도메인 로직을 갖지 않기 때문에 CRUD 방식으로 구현해도 됩니다. 즉, DAO와 데이터 중심의 밸류 객체를 이용해서 리뷰 기능을 구현해도 기능을 유지보수하는 데 큰 문제가 없습니다.

서비스 DAO 구조를 사용하면 도메인 기능이 서비스에 흩어지게 되지만 도메인 기능 자체가 단순하면 서비스 DAO로 구성된 CRUD 방식을 사용해도 코드를 유지보수하는 데 문제되지 않습니다.

한 BOUNDED CONTEXT에서 두 방식을 혼합해서 사용할 수도 있습니다. 대표적인 예가 CQRS 패턴입니다. CQRS는 Command Query Responsebility Segregation의 약자로 상태를 변경하는 명령 기능과 내용을 조회하는 쿼리 기능을 위한 모델을 구분하는 패턴입니다. 
이 패턴은 단일 BOUNDED CONTEXT에 적용하면 상태 변경과 관련된 기능은 도메인 모델 기반으로 구현하고 조회 기능은 서비스 DAO를 이용해서 구현할 수도 있습니다.

## BOUNDED CONTEXT 간 통합

온라인 쇼핑 사이트에서 매출 증대를 위해 카탈로그 하위 도메인에 개인화 추천 기능을 도입하기로 했습니다. 기존 카탈로그 시스템을 개발하던 팀과 별도로 추천 시스템을 담당하는 팀이 생겨서 이 팀에 주도적으로 추천 시스템을 만들기로 했습니다. 이렇게 되면 카탈로그 하위 도메인에는 기존 카탈로그를 위한 BOUNDED CONTEXT와 추천 기능을 위한 BOUNDED CONTEXT가 생깁니다.

두 팀이 관련된 BOUNDED CONTEXT를 개발하면 자연스럽게 두 BOUNDED CONTEXT 간 통합이 발생합니다. 카탈로그와 추천 BOUNDED CONTEXT 간 통합이 필요한 기능은 다음과 같습니다.

- 사용자가 제품 상세 페이지를 볼 때, 보고 있는 상품과 유사한 상품 목록을 하단에 보여줍니다.

사용자가 카탈로그 BOUNDED CONTEXT에 추천 제품 목록을 요청하면 카탈로그 BOUNDED CONTEXT는 추천 BOUNDED CONTEXT로 부터 추천 정보를 읽어와 추천 제품 목록을 제공합니다. 이때 카탈로그 컨텍스트와 추천 컨텍스트의 도메인 모델은 서로 다릅니다. 카탈로그 제품을 중심으로 도메인 모델을 구현하지만 추천은 추천 연산을 위한 모델을 구현합니다. 예를 들어, 추천 시스템은 상품의 상세 정보를 포함하지 않으며 상품 번호 대신 아이템 ID라는 용어를 사용해서 식별자를 표현하고 추천 순위와 같은 데이터를 담게 됩니다.

카탈로그 시스템은 추천 시스템으로부터 추천 데이터를 받아오지만, 카탈로그 시스템에서는 추천의 도메인 모델을 사용하기 보다는 카탈로그 도메인 모델을 사용해서 추천 상품을 표현해야 합니다. 즉, 다음과 같이 카탈로그의 모델을 기반으로 하는 도메인 서비스를 이용해서 상품 추천 기능을 표현해야 합니다.


```java
/**
* 상품 추천 기능을 표현하는 도메인 서비스
*/
public interface ProductRecommendationService {
    public List<Product> getRecommendationsOf(ProductId id);
}
```

도메인 서비스를 구현한 클래스는 인프라스트럭처 영역에 위치합니다. 이 클래스는 외부 시스템과의 연동을 처리하고 외부 시스템 모델과 현재 도메인 모델 간의 변환을 책임집니다.

예를 들어, RecSystemClient는 위의 ProductRecommendationService를 구현한 인프라스트럭처 영역에 있는 클래스입니다. RecSystemClient의 역할은 외부 추천 시스템이 제공하는 REST API를 이용해서 특정 상품을 위한 추천 상품 목록을 로딩하는 것입니다. 이 REST API가 제공하는 데이터는 추천 시스템 모델을 기반으로 하고 있기 때문에 API 응답은 다음과 같이 상품 도메인 모델과 일치하지 않는 데이터를 제공할 것입니다.

```java
[
    {itemId: 'PROD-1000', type: 'PRODUCT', rank: 100},
    {itemId: 'PROD-1001', type: 'PRODUCT', rank: 54}
]
```

RecSystemClient는 REST API로부터 데이터를 읽어와 카탈로그 도메인에 맞는 상품모델로 변환합니다. 다음은 일부 코드를 가상으로 만들어 본 것입니다.

```java
public class RecSystemClient implements ProductRecommendationService {
    private ProductRepository productRepository;

    @Override
    public List<Product> getRecommendationsOf(ProductId id) {

        List<RecommendationItems> items = getRecItems(id.getValue());
        return toProducts(items);
    }

    
    private List<RecommendationItems> getRecItems(String itemId) {
        // externalRecClient는 외부 추천 시스템을 위한 클라이언트라고 가정
        return externalRecClient.getRecs(itemId);
    }

    private List<Product> toProducts(List<RecommendationItems> itmes) {
        items.stream()
            .map(item -> toProductId(item.getItemId()))
            .map(productId -> productRepository.findById(productId))
            .collect(toList());

    }

    private ProductId toProductId(String itemId) {
        return new Product(itemId);
    }
}
```

이 코드에서 getRecItems() 메서드에서 사용하는 externalRecClient는 외부 추천 시스템에 연결할 때 사용하는 클라이언트로서 추천 시스템을 관리하는 팀에서 배포하는 모듈이라고 가정합시다. 이 모듈이 제공하는 RecommendationItem은 추천 시스템의 모델을 따를 것입니다. RecSystemClient는 추천 시스템의 모델을 받아와 toProducts() 메서드를 이용해서 카탈로그 도메인의 Product 모델로 변환하는 작업을 처리합니다.