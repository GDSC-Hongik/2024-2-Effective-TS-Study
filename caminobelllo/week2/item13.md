## [아이템 13] 타입과 인터페이스의 차이점 알기

타입스크립트에서는 `type`과 `interface` 두 가지 키워드로 객체의 타입을 정의할 수 있다.

책 내용을 살펴보기 전에 type alias와 interface는 언뜻보면 굉장히 유사해 보인다.

각각의 정의를 먼저 살펴보면 type과 interface는 둘 다 타입에 이름을 부여해 주는 것은 맞다. 하지만 type alias는 모든 타입, 즉 any 타입에 이름을 부여할 수 있지만 interface는 오직 객체 타입에만 가능하다는 것이다.

object 타입이 아닌 경우에는 type alias를 사용하면 될 것이다. 그렇다면 object에 대한 타입 선언을 하는 경우에는 대체 뭘 사용해야 할까 헷갈렸다. 그래서 차이점에 대해서 좀 더 살펴보면, type alias는 새로운 프로퍼티에 열려있지 않다고 하고, interface는 항상 열려있다고 한다.

즉, 확장 측면에서 가장 큰 사용 용도가 달라진다고 생각한다.

### 공통점

1. 타입의 기본 동작
   type과 interface 모두 타입 선언 등의 기본적인 동작이 가능하다.

앞에서 이야기한 엄격한 객체 리터럴 체크와 같이, 객체 리터럴을 생성하면서 타입 선언을 할 때 추가 속성과 함께 할당을 한다면 에러가 발생한다.

2. 인덱스 시그니처
   type과 interface 모두 인덱스 시그니처 사용이 가능하다.

3. 함수 타입 정의
   type과 interface 모두 함수의 타입을 정의할 수 있다.

4. 제네릭
   type과 interface 모두 제네릭 사용이 가능하다.

5. 타입 확장 가능
   type과 interface 모두 확장 가능하며, 타입이 인터페이스를 확장하는 것도 가능하다.

```
interface IStateWithPop extends TState{
    population: number;
}
type TStateWithPop = IState & { population: number; };

// 이때 IStateWithPop와 TStateWithPop은 동일하다.
```

하지만 주의해야할 것은, 인터페이스는 union 타입 같은 복잡한 타입을 확장하지 못한다. 만일 복잡한 타입을 확장하는 경우에는 `type`과 `&` (intersection)을 사용해야 한다.

이 책에서는 '확장 가능성'에 초점을 맞췄기 때문에 공통점에 들어가 있으나, 공식 문서를 참고하면 이 부분은 차이점에 해당할 수도 있다.

<img src="https://github.com/user-attachments/assets/8fe2680c-18ff-4fe7-85f8-2a3410ab1446" >

여기서도 type을 확장 시, 인터섹션을 이용해 타입을 확장하고 있다.

다음으로는, 새로운 필드(속성)를 추가하는 경우인데
<img src="https://github.com/user-attachments/assets/9a8ea7ef-3562-4ff9-98a2-13425f46bc37" >

인터페이스에서는 이미 존재하는 인터페이스에 새 필드를 추가하는 것이 가능하나, 타입의 경우에는 이미 생성된 이후에는 해당 타입의 필드를 변경하는 것은 불가능하다.

6. 클래스 구현 가능 (`implements`)
   클래스를 구현할 때는 type과 interface 모두 사용 가능하다.

<hr />

### 차이점

1. 유니온 개념의 유무
   type에는 union 타입이 있지만, interface에는 union 인터페이스가 없다.
   또한 interface에는 인터섹션도 존재하지 않는다.

따라서 interface는 복잡한 타입을 만들어 낼 수 없다.

type 키워드는 유니온이 될 수도 있고, 매핑된 타입 또는 조건부 타입 같은 고급 기능에 활용되기도 한다.

2. 튜플과 배열 타입의 간결한 표현
   type을 이용하면 튜플과 배열 타입도 간결하게 표현 가능하다.

```
type Pair = [number, number];
type StringList = string[];
type NamedNums = [string, ... number[]];
```

interface 사용 시, 형태는 유사하게 만들 수 있으나 튜플의 프로토타입 체인 상에 있는 메서드를 사용할 수 없다.

3. 선언 병합의 사용 가능 유무
   선언 병합이란, 같은 이름의 interface를 선언하면 자동으로 속성이 확장되는 것이다.

   interface은 **선언 병합 (declaration merging)**을 통해 interface을 새로 만들지 않으면서 속성을 확장해 나갈 수 있다.

```
interface IState {
    name: string;
    capital: string;
}
interface IState {
    population: number;
}
const wyoming: IState = {
    name: 'Wyoming',
    capital: 'Cheyenne',
    population: 500000
}
```

병합은 언제든지 가능하다. 따라서 프로퍼티가 추가되길 원하지 않는다면 인터페이스 대신 타입 키워드를 사용해야 한다.

그렇다면 결론은 둘 중에 뭘 쓰라는 걸까?

기존 코드가 있다면 그걸 따라가라고 한다. 복잡한 타입이라면 타입 별칭을 사용한다.

향후에 보강이 필요할 것 같다면 인터페이스를 사용하면 된다.

<hr />

[공식 문서](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#interfaces)
[참고 1](https://tecoble.techcourse.co.kr/post/2022-11-07-typeAlias-interface/)
[참고2](https://junghyunkim.tistory.com/entry/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B813-%ED%83%80%EC%9E%85%EA%B3%BC-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90-%EC%95%8C%EA%B8%B0)
