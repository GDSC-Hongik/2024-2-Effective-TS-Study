## [아이템 9] 타입 단언보다는 타입 선언을 사용하기

타입스크립트에는 변수에 값을 할당하고 타입을 부여하는 두 가지 방법이 존재한다.

### 1. 타입 선언 (Defining Types)

### 2. 타입 단언 (Type Assertions)

```
interface Person { name: string};

const alice: Person = { name: 'Alice'}  // 타입은 Person
const bob = { name: 'Bob' } as Person   // 타입은 Person
```

위 두 방법의 결과는 같지 않다.

타입 선언을 한 경우에는 alice라는 변수에 '타입 선언(: Person)' 을 붙여서 **그 값이 선언된 타입임을 명시**한다.

타입 단언(as Person)을 수행한 경우에는 타입스크립트가 추론한 타입이 존재하더라도 Person 타입으로 간주한다.
즉, 타입을 강제로 지정해서 타입 체커가 오류를 무시하도록 하는 것이다.

기존 인터페이스에 정의하지 않은 속성을 추가해서 객체를 생성하는 경우에도 타입 단언을 한 경우에는 에러 없이 실행된다.

**따라서 타입 단언`(as Type)`이 꼭 필요한 경우가 아니라면, 안전성을 위해 타입 선언`(: Type)`을 사용하자.**

<br />

그러면 타입 단언은 아예 사용하지 않는 것일까? 그건 아니다.
책에서는 타입 단언은 타입 체커가 추론한 타입보다 스스로 판단하는 타입이 더 정확한 경우에 의미가 있다고 한다.

타입스크립트는 DOM에 접근할 수 없기 떄문에, DOM 요소에 대한 내용은 코드를 작성하는 개발자가 더 잘 알 수 있다.

예를 들어, 다음과 같은 코드가 있다.

**`const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement;`**

(위 코드는 `const myCanvas = <HTMLCanvasElement>document.getElementById("main_canvas");` 과 완전히 동일하다.)

이처럼 `document.getElementByID` 를 사용할 때, 타입스크립트는 단지 어떤 종류의 HTMLElement를 반환한다 정도로만 안다. 하지만 우리는 페이지에 주어진 id를 가진 HTMLCanvasElement가 있음을 알 수 있다. 이런 경우에는 타입 단언을 사용하여 보다 구체적을오 타입을 지정하는 것이 좋다.

(하지만 주의해야할 점은, 타입 단언은 컴파일 타임에 완전히 지워지기 떄문에 런타임에는 타입 단언과 관련한 검사가 진행되지 않는다. 타입 단언이 틀린 경우에도 예외를 발생시키거나, null을 생성하지 않는다.)

<br />

또한, 아무렇게나 타입 단언을 사용할 수 없다. 타입 단언을 사용하려면 객체의 현재 타입이 단언될 타입의 상위 집합 또는 부분 집합이어야 한다.

그렇지 않은 경우에는 객체의 타입을 전체 집합인 unknown 타입으로 변환한 후에, 특정 타입으로 단언해야 한다. 이렇게 하는 이유는 unknown 타입이 모든 타입의 상위 집합이기 떄문이다.

그러나 이와 같은 방식은 위험할 수 있기 때문에 주의를 필요로 한다.