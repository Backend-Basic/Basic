## 🙋 들어가며

오늘은 자바의 최상위 객체 `Object`에 `equals()`와 `hashCode()`에 대해서 이야기해보겠습니다.

이번 글을 통해서 각 메서드를 언제, 어떻게 사용하는지 알아보고

왜 두 메서드를 같이 재정의 해야 하는지 알아보겠습니다.

## 🟰 equals()

문자열을 비교할 때 많이 사용하는 친숙한 메서드입니다.

이 메서드를 왜 사용하는지 이해하기 위해서는 `동일성`과 `동등성`의 개념을 알아야 합니다.

간단하게 정리하면 다음과 같습니다. 그리고 이해를 위한 예시를 작성해 보겠습니다.
1. 동일성(identity) : 같은 참조를 가진다.
2. 동등성(equality) : 같은 값을 가진다.

```java
String str1 = new String("hejow");
String str2 = new String("hejow");

System.out.println(str1 == str2); // false
System.out.println(str1.equals(str2));  // true
```

자바에서 동일성을 비교할 때는 `==`을 사용하고, 동등성을 비교할 때는 `equals()`를 사용합니다.

다음 예시를 보면 첫 출력에서는 `new` 키워드로 새로운 주소를 만들었기 때문에 `false`가 출력되고,

다음 출력에서는 같은 값인지 비교하므로 `true`가 출력이 됩니다.

### 🤔 Objects.eqauls()

이제 `equals()`는 동등성 비교를 위해서 만들어짐을 알았습니다.

비슷하게 생긴 `Objects.equals()`와 비교하면서 조금 더 알아보겠습니다.

다음은 각 메서드의 내부 구현입니다.

```java
// Object.equals()
public boolean equals(Object obj) {
    return (this == obj);
}

// Objects.equals()
public static boolean equals(@Nullable Object a, @Nullable Object b) {
    return (a == b) || (a != null && a.equals(b));
}
```

`Object.equals()`의 내부 구현을 보면 `==`을 사용해서 비교합니다. 즉, 동일성을 비교하고 있습니다.

반면에, `Objects.equals()`는 내부적으로 `Object.equals()`를 사용하고 있습니다.

그리고 정적(static) 메서드이고 `null`을 인자로 받을 수 있다는 차이점이 있습니다.

여기서 우리는 재정의하지 않은 `Object.equals()`는 동일성 비교를 하고 있기 때문에,

구현한 객체에서 동등성 비교를 위한 메서드인 `equals()`를 올바르게 사용하기 위해서는 재정의를 해야 한다는 것을 알 수 있습니다.

## 🔐 hashCode()

이번에는 `hashCode()`를 언제 사용하는지 알아보겠습니다.

`hashCode()`는 이름 그대로 hash code를 반환하는데, 이 코드를 통해서 **객체를 식별**합니다.

### 🤔 Hash Code == Memroy Address?

객체를 식별한다고 이야기하면 메모리 주소가 떠오를 수 있습니다.

과연 hash code는 메모리 주소일까요? 간단하게 예시를 통해서 확인해 보겠습니다.

다음과 같이 좌표를 나타내는 `Point`라는 객체를 만들었습니다.

그리고 예시를 위해서 `equals()`와 `hashCode()`가 잘 구현되어 있다고 가정해 보겠습니다.

```java
public class Point {
    private int x;
    private int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
    // equals(), hashCode()...
}
```

다음 코드를 동작시키면 어떻게 결과가 나올까요?

```java
Point point1 = new Point(1, 2);
Point point2 = new Point(1, 2);

System.out.println("point1 == point2 : " + (point1 == point2));
System.out.println("point1.equals(point2) : " + point1.equals(point2));
System.out.println("point1.hashCode() == point2.hashCode() : " + (point1.hashCode() == point2.hashCode()));
```

![image](https://github.com/Backend-Basic/Basic/assets/81742959/c754942a-6baf-4d84-9201-71d10f44bd47)

위 예시를 통해서 **hash code는 메모리 주소가 아니다**를 알 수 있습니다.

보다 자세한 내용은 [스택 오버플로우](https://stackoverflow.com/questions/1961146/memory-address-of-variables-in-java/20680667#20680667)를 참고하시기 바랍니다.

### 🤷‍♂️ 그래서 언제 사용하나요?

`hashCode()`는 hash 기반의 `Collection`에서 사용합니다.

우리가 자주 사용하는 HashMap, HashSet, HashTable 등에서 사용합니다.

다른 글에서 자세하게 내부적으로 어떻게 찾고, 값이 만들어지는지 설명하겠습니다.

## 🧐 왜 같이 재정의할까?

우리가 자주 사용하는 `Lombok`에서도 `@EqualsAndHashCode`를 제공하고,

인텔리제이의 `Generate` 기능도 두 메서드를 같이 재정의합니다.

왜 같이 재정의해야 하는걸까요??

<br>

우선 `equals()`를 재정의해야 하는 이유는 `Object.equals()`는 동일성 비교를 하기 때문입니다.

따라서 재정의를 하지 않았다면 다음 코드는 `false`를 출력하게 됩니다.

```java
Point point1 = new Point(1, 2);
Point point2 = new Point(1, 2);

System.out.println(point1.equals(point2));
```

다음으로, `hashCode()`도 같이 재정의해야 하는 이유는 `HashSet`을 사용한 예시를 보면서 이야기해보겠습니다.

### 1️⃣ equals()만 재정의

`Person` 객체는 이름과 나이를 가집니다. 그리고 다음과 같이 `equals()`만 재정의했습니다.

```java
public class Person {
    private final String name;
    private final int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public boolean equals(Object o){
        if (o instanceof Person person) {
            return this.age == person.age && Objects.equals(this.name, person.name);
        }
        return false;
    }
}
```

그리고 살펴볼 예시는 다음과 같습니다.
```java
Set<Person> people = new HashSet<>();

people.add(new Person("john", 27));
people.add(new Person("john", 27));

System.out.println(people.size()); // 1 ??
```

`Set`이라는 자료구조는 중복이 없어야 합니다.

중복을 확인할 수 있게 `equals()`를 구현했으므로 예상되는 결과는 1입니다.

하지만 예상과 다르게도 출력되는 값은 2입니다.

### 2️⃣ hashCode()만 재정의

이번에는 `hashCode()`만 재정의한 경우를 보겠습니다.

hash code는 객체를 식별하기 위한 값이므로, 같은 값을 리턴하도록 구현했습니다.

```java
@Override
public int hashCode() {
    return 0;
}
```
위의 같은 예시에서 식별값이 같아 중복이 없을 것 같지만 예상과 다르게 1을 출력합니다. 대체 왜 그럴까요??

### 💥 Hash 자료구조의 동작원리

Hash 자료구조는 다음과 같은 순서를 기반으로 동작합니다.

![image](https://github.com/Backend-Basic/Basic/assets/81742959/d8b39404-364e-4af0-ba22-061889966b9a)

먼저 `hashCode()`를 호출해서 같은지 비교하고, 같다면 `equals()`를 호출해서 또 한 번 검사합니다.

`equals()`만 재정의한 경우, `hashCode()`의 값이 다르므로 `HashSet`에서는 다르다고 판단했습니다.

반대의 경우, `hashCode()`는 같지만 재정의하지 않은 `equals()`는 동일성 비교를 하므로 주소값이 달라서 `HashSet`이 다른 객체라고 판단하였습니다.

<br>

두 메서드를 같이 정의하지 않으면 **코드가 어떻게 동작할지 예측할 수 없습니다.**

따라서, `equals()`를 재정의했다면 반드시 `hashCode()`를 재정의해야 합니다.

각 메서드의 내부에 작성되어 있는 문서와 함께 `equals()`와 `hashCode()`를 조금 더 알아보겠습니다.

### 📝 equals() 메서드 규약

다음 그림은 `equals()`에 작성되어 있는 문서입니다.

![image](https://github.com/Backend-Basic/Basic/assets/81742959/3f05be55-e675-4723-8c8b-6a331ee18fd9)

정리하면 `null`이 아닌 객체 x, y, z에 대해서 다음을 만족해야 합니다.
1. 반사성 : `x.equals(x)`는 참이다.
2. 대칭성 : `x.equals(y)`가 참이면, `y.equals(x)`도 참이다.
3. 추이성 : `x.equals(y)`와 `y.equals(z)`가 참이면, `z.equals(x)`는 참이다.
4. 일관성 : 반복해서 `eqauls()`를 호출해도 같은 결과를 가진다.
5. Not-Null : `x.equals(null)`은 거짓이다.

### 📝 hashCode() 메서드 규약

다음 그림은 `hashCode()`에 작성되어 있는 문서입니다.

![image](https://github.com/Backend-Basic/Basic/assets/81742959/efa5455d-7b05-4711-8f6c-dd044f170209)

정리하면 다음과 같습니다.
1. `equals()`에 사용되는 정보가 변하지 않았다면 `hashCode()`도 같은 값을 반환해야 한다.
2. `equals()`가 같다고 판단했으면 두 객체의 `hashCode()`는 같아야 한다.
3. `equals()`가 다르다고 판단했어도 두 객체의 `hashCode()`는 다를 수 있다. (해쉬 충돌)

## 😋 정리
1. eqauls()는 동등성 비교를 위해서 사용한다.
2. hashCode()는 객체 식별을 위해서 사용한다.
3. eqauls()와 hashCode()는 반드시 같이 재정의해야 한다.
