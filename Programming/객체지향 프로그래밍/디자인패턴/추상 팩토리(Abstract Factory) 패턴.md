
비행기를 조정하고 미사일을 발사해서 적을 미사일로 쏴 맞춰 잡는 슈팅 게임을 만든다고 가정해봅시다. 이런 게임은 흔히 여러 종류의 적이 출현하고 한 단계의 끝에 다다르면 그 단계의 보스가 출현하고, 이 보스를 맞춰 잡으면 다음단계로 넘어가는 방식을 취합니다. 또한, 중간 중간에 공격은 하지 않지만, 부딪히면 안 되는 장애물이 출현하기도 합니다. 보스 적기, 작은 적기 그리고 장애물은 단계마다 다른 종류가 출현합니다.

예를 들어, 특별 공격으로 작은 분신을 만들어 내는 보스와 강력한 미사일을 발사하는 보스의 두 가지 종류가 있을 수 있고, 적기에도 미사일을 발사하는 적기와 자폭하는 적기가 있을 수 있습니다. 또한, 각 단계마다 적들의 공격력이나 방어력이 달라질 수 있습니다. 이런 보스, 적기, 장애물을 구현하기 위해 아래 그림과 같이 Boss, SmallFlight, Obstacle 클래스 및 하위 클래스를 구성하였습니다.

![Untitled Diagram (4)](https://user-images.githubusercontent.com/22395934/81499868-70856300-9309-11ea-8648-9c4200544fec.png)

실제 게임 플레이를 진행하는 Stage 클래스는 몇 단계인지 따라 서로 다른 적기, 장애물 또는 보스를 생성해야 합니다. 이를 처리하기 위해 Stage 클래스의 코드를 다음과 같이 작성할 수 있을 것입니다.

```java
// Stage 클래스
public void createEnemies() {

    for (int i = 0; i<= ENEMY_COUNT; i++) {
        if(stageLevel == 1) {
            enemies[i] = new DashSmallFlight(1, 1); // 공격/수비력 1
        } else if (stageLevel == 2) {
            enemies[i] = new MissileSmallFlight(1, 1);
        }
    }

    if (stageLevel == 1) {
        boss = new StrongAttackBoss(1, 10);
    } else if (stageLevel == 2) {
        boss = new CloningBoss(5, 20);
    }
}

private void createObstacle() {
    for(int i = 0; i < OBSTACLE_COUNT; i++) {
        if (stageLevel == 1) {
            obstacles[i] = new RockObstacle();
        } else {
            obstacles[i] = new BombObstacle();
        }
    }
}
```

위 코드의 문제는 단계별로 적기, 보스, 장애물을 생성하는 규칙이 Stage 클래스에 포함되어 있다는 점입니다. 새로운 적 클래스가 추가되거나 각 단계의 보스 종류가 바뀔 때 Stage 클래스를 함께 수정해 주어야 하고, 각 단계별로 적기 생성 규칙이 달라질 경우에도 Stage 클래스를 수정해 주어야 합니다.
또한 중첩되거나 연속된 조건문으로 인해 코드가 복잡해지기 쉽고 이는 코드 수정을 어렵게 만드는 원인이 됩니다.

적과 장애물 객체의 생성을 Stage 클래스에서 직접 수행하면서 앞서 언급한 문제들이 발생하기 때문에, Stage 클래스로부터 객체 생성 책임을 분리함으로써 이 문제를 해소할 수 있습니다. 이 때 사용되는 패턴이 바로 `추상 팩토리 패턴입니다.`

추상 팩토리 패턴에서는 관련된 객체 군을 생성하는 책임을 갖는 타입을 별도로 분리합니다. 앞서 예의 경우 SmallFlight, Boss, Obstacle 객체를 생성해 주는 책임을 갖는 EnemyFactory 타입을 추가할 수 있습니다.

EnemyFactory 클래스는 Boss, SmallFlight, Obstacle 객체를 생성해 주는 메서드를 정의하고 있습니다. 여기서 EnemyFactory 클래스는 객체 생성 메서드를 선언하는 추상 타입으로서 팩토리에 해당되며, 팩토리가 생성하는 대상인 Boss, SmallFlight, Obstacle은 제품 타입이 됩니다.

EnemyFactory.getFactory() 메서드는 정적 메서드로서 파라미터로 전달받은 레벨에 따라 알맞은 EnemyFactory 객체를 리턴하도록 정의하였습니다. 아래 코드를 보고 어떻게 생성하는지 살펴보겠습니다.

```java
public abstract class EnemyFactory {
    public static EnemyFactory getFactory(int level) {
        if (level == 1) 
            return EasyStageEnemyFactory();
        else 
            return HardEnemyFactory();
    }

    // 객체 생성을 위한 팩토리 메서드
    public abstract Boss createBoss();
    public abstract SmallFlight createSmallFlight();
    public abstract Obstacle createObstacle();
}
```

팩토리인 EnemyFactory를 구현한 콘크리트 팩토리 클래스는 아래 코드에서 보듯이 알맞은 객체를 생성합니다.

```java
public class EasyStageEnemyFactory extends EnemyFactory {
    
    public Boss createBoss() {
        return new StrongAttackBoss();
    }
    
    public abstract SmallFlight createSmallFlight() {
        return new DashSmallFlight();
    }
 
      public abstract SmallFlight createSmallFlight() {
        return new RockObstacle();
    }
}
```
```java
public class HardEnemyFactory extends EnemyFactory {

     public Boss createBoss() {
        return new CloningAttackBoss();
    }
    
    public abstract SmallFlight createSmallFlight() {
        return new MissileSmallFlight();
    }
 
      public abstract SmallFlight createSmallFlight() {
        return new BombObstacle();
    }
}
```

Stage 클래스는 객체 생성이 필요한 경우 직접 생성하기 보다는 아래 코드처럼 추상 팩토리 타입인 EnemyFactory를 이용해서 객체를 생성합니다.

```java
// 팩토리를 사용하도록 바뀐 Stage 클래스
public EnemyFactory enemyFactory;

public class Stage(int level) {
    enemyFactory = EnemyFactory.getFactory(level);
}

private void createEnemy() {
    for (int i = 0; i <= ENEMY_COUNT; i++) {
        enemies[i] = enemyFactory.createSmallFlight();
          
    boss = enemyFactory.createBoss();
}

private void createObstacle() {
    for (int i = 0; i < OBSTACLE_COUNT; i++) {
        obstacle[i] = enemyFactory.createObstacle();
    }
}
```
 
변경된 Stage 클래스의 코드를 보면, Stage 클래스는 더 이상 StrongAttackBoss 클래스와 DashSmallFlight 클래스와 같은 콘크리트 제품 클래스를 사용하지 않습니다. 단지, 추상 타입인 Boss, SmallFlight, Obstacle만 사용할 뿐입니다. 각 콘크리트 제품 클래스를 사용해서 객체를 생성하는 코드는 아래 코드세어 보듯이 EnemyFactory 팩토리의 하위 타입인 EasyStageEnemyFactory 클래스와 HardEnemyFactory 클래스로 옮겨졌습니다.

Stage 클래스는 EnemyFactory 클래스의 정적 메서드인 getFactory() 메서드를 이용해서 사용할 EnemyFactory 클래스를 구하고 있습니다.

```java
private EnemyFactory enemyFactory;
public class Stage(int level) {
    //EnemyFactory 객체를 구함
    enemyFactory = EnemyFactory.getFactory(level);
}
```
 
 따라서 1 레벨에서 사용되는 적 객체를 완전히 다른 타입으로 변경하고 싶다면 State 클래스를 변경할 필요 없이, 새로운 EnemyFactory 구현 클래스를 만들고, EnemyFactory.getFactory() 메서드에서 이 클래스의 객체를 리턴하도록 수정해 주면 됩니다.
 
 
 ```java
public abstract class EnemyFactory {
    public static EnemyFactory getFactory(int level) {
        if (level == 1) {
            // 적 생성 규칙 변경 시, 새로운 팩토리 클래스를 만들면 됩니다.
            return SomethingNewEnemyFactory();
        else {
            return HardEnemyFactory();
        }
    }
```

위 코드에서 EnemyFactory 객체를 구하는 기능을 EnemyFactory 클래스에 정의했는데, DI를 사용해도 됩니다. DI를 사용하면 아래 코드처럼 생성자나 설정 메서드를 통해서 EnemyFactory 객체를 전달받게 되므로, EnemyFactory 클래스에 getFactory() 메서드를 정의할 필요가 없어집니다. 따라서 EnemyFactory 추상 클래스를 인터페이스로 전환할 수 있게 됩니다.

```java
public class Stage {

    private EnemyFactory enemyFactory;

    //DI를 적용하면 팩토리를 구하는 기능을 EnemyFactory에 구현할 필요가 없습니다.
    public Stage(int level, EnemyFactory enemyFacotry) {
        this.level = level;
        this.enemyFactory = enemyFactory;
    }
    ...
}
```

추상 팩토리 패턴을 사용할 때의 장점은 클라이언트에 영향을 주지 않으면서 사용할 제품(객체)군을 교체할 수 있다는 점입니다

예제에서 Stage 클래스는 Boss, SmallFlight, Obstacle 타입의 객체를 사용하는데, CloningBoss, MissileSmallFlight, BombObstacle 객체(즉, 제품)를 사용하다가 StrongAttackBoss, DashSmallFlight, RockObstacle 객체를 사용하도록 변경하더라도 Stage 클래스는 전혀 영향을 받지 않습니다. 오직 Stage 클래스가 사용할 콘크리트 팩토리 객체만 변경해 주면 됩니다. 사용할 콘크리트 객체를 변경하는 작업 역시 Stage 클래스에는 영향을 주지 않기 때문에, 제품군을 쉽게 변경할 수 있습니다.
 
 만약 팩토리가 생성하는 객체가 늘 동일한 상태를 갖는다면, 프로토타입 방식으로 팩토리를 구현할 수 있습니다. 프로토타입 방식은 아래 코드처럼 생성할 객체의 원형 객체를 등록하고, 객체 생성 요청이 있으면 원형 객체를 복제해서 생성합니다.

```java
// 프로토타입 방식의 팩토리
public class Factory {

    private ProductA productAProto;
    private ProductA productAProto;

    public Factory(ProductA productAProto, ProductB productBProto) {
        this.productAProto = productAProto;
        this.productBProto = productBProto;
    }

    public ProductA createA() {
        return (ProductA) ProductAProto.clone();
    }

    public ProductB createB() {
        return (ProductB) ProductBProto.clone();
    }

}
```

프로토 타입 방식의 팩토리를 사용하면, 객체 군 마다 팩토리 클래스를 작성할 필요 없이 객체 군 마다 팩토리 객체를 생성해 주면 됩니다.

```java
// 객체 군 1을 위한 팩토리 객체
Factory family1Factory = new Factory(new HighProductA(), new HighProductB());
ProductA a = family1Factory.createA(); // HighProductA 객체 복제본 생성

// 객체 군 2를 위한 팩토리 객체
Factory family2Factory = new Factory(new LowProductA(), new LowProductB());
ProductB b = family2Factory.createB(); // LowProductB 객체 복제본 생성
```

프로토타입 방식을 사용하면 추상 팩토리 타입과 콘크리트 팩토리 클래스를 따로 만들 필요가 없어 구현이 쉽지만, 반면에 제품 객체의 생성 규칙이 복잡할 경우 적용할 수 없는 한계가 있습니다.

우리가 흔히 볼 수 있는 코드 중에 추상 팩토리 패턴을 적용한 대표적인 예가 자바의 JDBC API입니다.












 
 

