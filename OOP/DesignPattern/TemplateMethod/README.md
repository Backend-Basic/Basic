# 참고 자료

[Refactoring GURU](https://refactoring.guru/ko/design-patterns/template-method)

# 기본 개념

부모 클래스에서 알고리즘(핵심 기능)의 골격을 정의한다.

자식 클래스에서 해당 알고리즘의 구조를 변경하지 않고 알고리즘(핵심 기능)의 특정 부분을 오버라이드 하여 부가 기능을 덧붙일 수 있도록 한다.

- 변하지 않는 것 : 알고리즘 (핵심 기능)

- 변하는 것 : 부가 기능

# 조금 더 쉽게 알아보기

## 상황 예시 - 문서 분석 데이터 마이닝 요구사항

![](https://refactoring.guru/images/patterns/diagrams/template-method/problem-2x.png?id=fc8b434afec7b6135043d0d2f48189f0)

- 첫 번째 버전은 DOCS 파일을 다룰 수 있다.

- 두 번째 버전은 CSV 파일을 다룰 수 있다.

- 세 번째 버전은 PDF 파일을 다룰 수 있다.

위 세가지 버전의 코드를 살펴보면 중복되는 코드가 자주 등장하는 것을 발견할 수 있을 것이다.

각 버전마다 데이터 형식을 처리하는 방법은 다르겠지만, 데이터 처리 및 분석을 위한 코드는 같다.

따라서 변하지 않는 알고리즘은 그대로 두되 코드 중복을 제거하는 것이 좋을 것 같다.

또한 각 세가지 버전을 사용하는 클라이언트 입장에서는 아래와 같이 분기처리로 코드를 처리해야할 것이다.

```kotlin
fun clientCode(fileType: String) {
    if (fileType == "DOCS") {
        // DOCS Data Mining Call
    } else if (fileType == "CSV") {
        // CSV Data Mining Call
    } else if (fileType == "PDF") {
        // PDF Data Mining Call
    }
}
```

세 가지 버전에 대한 `공통 인터페이스`나 `클래스`가 있었다면 클라이언트에서 굳이 이렇게 분기처리를 하지 않아도 될 것이다.

## 상황 예시 - 해결책

![](https://refactoring.guru/images/patterns/diagrams/template-method/solution-ko-2x.png?id=100524477c04daae219a201c35478ea2)

우선 필요한 모든 로직을 abstract로 선언하여 자식 클래스에게 오버라이딩을 하도록 명시한다.

사실 이게 전부이다. 바뀌지 않는 로직은 기본 메서드로 제공하되 변하는 부가기능에 대한 메서드는 abstract로 제공하여 오버라이딩 할 수 있도록 명시하면 되는 것이다.

---

# 코드로 알아보기 - 스타크래프트

![](https://refactoring.guru/images/patterns/diagrams/template-method/example-2x.png?id=d8b309659c4b722237ec97733da935bd)

대충 스타크래프트를 떠올리면 각 유닛 별로 AI가 존재한다.

이때 테란 종족의 AI, 프로토스 종족의 AI를 따로 만들어야하는 상황이라고 가정해보자.

```kotlin
class GameAI {

    // 기본 알고리즘
    fun move() {
        ...
    }

    // 부가 기능
    abstract fun buildStructures()
    abstract fun buildUnits()
    abstract fun attack()
}

class TerranAI extends GameAI {
    override fun buildStructures() {
        if (there are some resources) {
            ...
        }
    }

    override fun buildUnits() {
        if (there are plenty of resources) {
            ...
        } else if (there are no scouts) {
            ...
        } else {
            ...
        }
    }
}

class ProtossAI extends GameAI {
    override fun buildStructures() {
        if (there are some resources) {
            ...
        }
    }

    override fun buildUnits() {
        if (there are plenty of resources) {
            ...
        } else if (there are no scouts) {
            ...
        } else {
            ...
        }
    }
}
```

위와 같이 기본적으로 제공할 메서드는 부모 클래스에서 정의한 후 각 자식 클래스마다 다르게 동작해야하거나 부가기능을 추가해야할 땐

abstract method로 오버라이딩을 하도록 유도하여 해결할 수 있다.

---

# 언제 쓰면 적절할까?

핵심 기능을 그대로 둔 채 확장을 열어두기 위한 방식이다. 따라서 핵심 알고리즘의 변경 없이 사용하며, 부가기능을 확장해야하는 상황이 적절해보인다.

# 장/단점

## 장점

- 클라이언트는 알고리즘의 특정 부분만 오버라이드하도록 하여 부가기능으로 인해 발생하는 변경에 영향을 덜 받도록 할 수 있다.

- 중복 코드를 부모 클래스로 가져올 수 있다.

## 단점

- 일부 클라이언트들은 알고리즘의 제공된 골격에 의해 제약이 발생한다.

- 자식 클래스를 통해 디폴트 단계 구현을 억제하여 리스코프 치환 원칙을 위반할 수 있다.

- 템플릿 메서드들은 구현해야할 것이 많을수록 유지가 더 어려운 경향이 있다.