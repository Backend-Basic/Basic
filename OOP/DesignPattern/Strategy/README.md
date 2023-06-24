# 참고 자료

[Refactoring GURU](https://refactoring.guru/ko/design-patterns/strategy)

# 기본 개념 - 행동 패턴

템플릿 메서드 패턴과 해결하고자 하는 요소는 같다.

변하지 않는 부분을 Context (문맥, 알고리즘, 템플릿)

변하는 부분을 Strategy (부가기능, 전략)으로 두어 

`상속이 아닌 위임`으로 해결하는 패턴이다.

# 조금 더 쉽게 알아보기

## 상황 예시 - 내비게이션

바다를 가기 위해 길을 찾는 내비게이션은 자동차 경로만 안내하는 로직이 있었다.

하지만, 국토대장정을 하며 바다로 가기 위해선 도보 경로를 안내하는 로직이 필요하다.

그리고 차량과 도보가 싫은 사용자는 대중교통으로 바다를 갈 수 있어야한다.

만약 이 모든 로직을 하나의 객체에 역할을 부여한다면 유지보수가 어려울 뿐더러 자동화된 테스트, 추가적인 기능 확장에도 어려움을 겪을 수 있다.

## 상황 예시 - 해결책

전략 패턴은 각자 다른 방식으로 해결하는 알고리즘을 `전략`이라는 별도의 객체로 분리한다.

그리고 클라이언트가 특정 전략이 필요하다는 것을 명시하는 것으로 해결하면 된다.

즉, A는 도보 전략을 사용하고, B는 자동차, C는 대중교통 으로 내비게이션 경로를 안내하는 전략을 사용하면 되는 것이다.

---

# 코드로 알아보기

![](https://refactoring.guru/images/patterns/diagrams/strategy/solution-2x.png)

내비게이션 Interface를 두고, Interface에 명세한 메서드를 구현하는 전략들을 구현한다.

클라이언트는 Interface에 명세된 메서드를 호출하되 어떤 구현체를 사용할지는 직접 선택한다.

### 전략 생성

```kotlin
interface Navigation {
    fun route()
}

class WalkStrategy : Navigation {
    override fun route() {
        println("Walk Strategy")
    }
}

class BusStrategy : Navigation {
    override fun route() {
        println("Bus Strategy")
    }
}

class CarStrategy : Navigation {
    override fun route() {
        println("Car Strategy")
    }
}
```

### Context 생성

```kotlin
class Context(
    private val navigation: Navigation,
) {
    fun executeStrategy() {
        return navigation.route()
    }
}
```

### Client

```kotlin
class WalkClient {

    fun main() {
        Context(WalkStrategy()).executeStrategy()
    }
}

class BusClient {

    fun main() {
        Context(BusStrategy()).executeStrategy()
    }
}

class CarClient {
    fun main() {
        Context(CarStrategy()).executeStrategy()
    }
}
```

코드의 흐름을 정리하자면 다음과 같다.

- Navigation을 정의한다. (Navigation)

- Navigation를 구현한 여러 전략을 클래스로 분리한다. (WalkStrategy, BusStrategy, CarStrategy) 

- Client가 사용할 문맥을 생성한다. (Context) 이 때 Context에서 사용할 의존성은 외부에서 주입한다. (생성자 주입)

- Client에서 문맥을 생성하고 전략을 주입하여 호출한다. [객체 합성] (Context(xxxStrategy()).executeStrategy())

# 언제 쓰면 적절할까?

- 런타임에 특정 알고리즘을 동적으로 사용하고 싶을 때

# 장/단점

## 장점

- 런타임에 한 객체 내부에서 사용되는 알고리즘들을 동적으로 사용할 수 있다.

- 알고리즘을 사용하는 코드에서 알고리즘의 구현 세부 정보들을 캡슐화하여 사용할 수 있다.

- 상속을 합성으로 대체할 수 있다.

- OCP를 지킬 수 있다. 사용자가 사용할 Context의 내부를 변경하지 않고도 새로운 전략들을 사용할 수 있다.

## 단점

- 알고리즘이 몇 개밖에 되지 않고 거의 변하지 않는다면 오버 엔지니어링이 될 수도 있다. (하지만 변하지 않는 것 없다. 라는 말대로 해두면 좋음)

- 클라이언트들은 적절한 전략을 선택할 수 있도록 전략 간의 차이점들을 알고 있어야 합니다.

- 익명 내부 클래스로도 해결 가능하다. 굳이 객체를 늘려가면서 까지 해결하지 않아도 되긴 하다.