## item19 추론 가능한 타입을 사용해 장황한 코드 방지하기

- 타입 추론이 된다면 명시적 타입 구문은 필요하지 않다. 타입을 확신하지 못하겠다면 에디터를 통해 체크하면 된다.
- 객체, 배열 등에 타입 추론이 가능하다면 값에 추가로 타입을 작성할 필요는 없다.
- 타입이 추론되면 리팩토링이 쉬워진다.

```typescript
inerface Product {
  id: number;
  name: string;
  price: number;
}
function logProduct(product: Product){
  const id: number = product.id;
  const name: string = product.name;
  const price: number= product.price;
  console.log(id, name, price);
}
```

함수 내 명시적으로 타입 구문을 쓴 경우 Product 내 id의 타입을 변경하면 타입오류가 발생한다.  
그러나 타입 추론을 활용하여 사용하지 않았다면 오류가 발생하지 않았을 것이다.

- 타입스크립트는 매개변수의 최종 사용처를 고려하지는 않는다. 타입스크립트의 변수 의 타입은 일반적으로 처음 등장할 때 결정된다.

- 이상적인 타입스크립트 코드는 함수/매서드 시그니처에 타입 구문을 포함하지만,  
  함수 내 생성된 지역변수에는 타입 구문을 넣지 않는다.  
  이렇게 되면 타입 구문을 생략해 방해되는 것들을 최소화하고 코드를 읽는 사람이 구현 로직에 집중할 수 있다.

- 타입 정보가 있는 라이브러리에서, 콜백 함수의 매개변수 타입은 보통 자동으로 추론된다. 이 경우 매개변수의 타입구문을 생략할 수 있다.

- 타입이 추론되지만 타입을 명시하는 것을 사용하는 상황이 있다.
- **객체 리터럴 정의 시 정의에 타입을 명시**하면 **잉여 속성 체크**가 동작한다.  
   잉여 속성 체크를 통해 선택적 속성이 있는 타입의 오타 오류를 잡을 수 있다.  
   변수가 사용되는 시점이 아닌 **할당하는 시점에 오류가 표시**되도록 한다.  
   (아이템 11참고)

  - 함수 반환 시 타입을 명시하여 오류를 방지할 수 있다.  
    타입 구문을 명시하면 구현상의 오류가 함수를 호출한 곳에서 표시되지 않고 정확히 오류가 발생한 곳에 표시된다.  
    반환 타입 명시를 위해서는 입력, 출력 타입이 무엇인지 구현 전에 인지해야 하므로, 구현에 맞추어 함수 시그니처가 작성되는 것이 아니라 제대로 원하는 모양으로 작성할 수 있다.  
    반환 타입을 명시하면 호환되는 명명된 타입과 추론된 타입이 다른 당황스러운 상황을 방지할 수 있다.

  ```typescript
  interface Vector2D {
    x: number;
    y: number;
  }
  function add(a: Vector2D, b: Vector2D) {
    return { x: a.x + b.x, y: a.y + b.y };
  }
  ```

이 경우 타입이 vertor2D가 아닌 { x: number; y: number;}로 추론되게 되지만 이는 코드 작성 시 의도된 바와 다르다.
반환 타입을 명시하면 타입 주석 작성 시에도 활용할 수 있는 이점이 생긴다.

- 린터를 사용하면 esilent 규칙 중 no-inferrable-types 를 사용해 작성된 모든 타입 구문이 정말로 필요한 지 확인할 수 있다.  
  no-inferrable-types : typeScript 코드에서 자동으로 추론될 수 있는 타입을 명시하는 것을 방지하는 린터 규칙

## item23 한꺼번에 객체 생성하기

- 타입스크립트에서 변수의 값과 달리 타입은 일반적으로 변경되지 않는다.  
  따라서 객체를 생성 시 속성을 하나씩 추가하기 보다는 여러 속성을 포함해서 한번에 생성하면 타입 추론에 유리하다.

-

```typescript
const pt = {};
pt.x = 3;
pt.y = 4;
```

pt 타입이 {} 값을 기준으로 추론되어 각 할당문에 오류가 발생한다.

객체를 한번에 정의하면 이 문제를 해결할 수 있다.

```typescript
const pt = {
  x: 3,
  y: 4,
};
```

혹은 단언문을 사용하여 문제를 해결할 수 있다.

```typescript
const pt = {} as Point;
pt.x = 3;
pt.y = 4;
```

- 객체 연산자 **...**를 사용하면 큰 객체를 한번에 만들 수 있다.  
  객체 연산자의 기능

객체 복사: 객체 전개 연산자를 사용하면 객체를 얕은 복사(shallow copy)할 수 있다.

```typescript
코드 복사
const original = { a: 1, b: 2 };
const copy = { ...original };

console.log(copy); // { a: 1, b: 2 }
```

객체 병합: 여러 객체를 병합할 때도 사용할 수 있다.

```typescript
코드 복사
const obj1 = { a: 1, b: 2 };
const obj2 = { b: 3, c: 4 };
const merged = { ...obj1, ...obj2 };

console.log(merged); // { a: 1, b: 3, c: 4 }
// `obj2`의 `b` 값이 `obj1`의 `b` 값을 덮어씌움
```

새로운 속성 추가: 기존 객체에 새로운 속성을 추가할 때도 사용된다.

```typescript
코드 복사
const original = { a: 1, b: 2 };
const newObject = { ...original, c: 3 };

console.log(newObject); // { a: 1, b: 2, c: 3 }

```

- 객체에 조건부로 속성을 추가할때

```typescript
declare let hasMiddle: boolean;
const firstLast = { first: "Harry", last: "Truman" };
const president = { ...firstLast, ...(hasMiddle ? { middle: "S" } : {}) };
```

- 전개 연산자로 두 개 이상의 속성을 추가해보자.  
  start와 end가 항상 함께 정의되어 타입에서 각각의 속성을 읽어올 수가 없게 된다.

```typescript
declare let hasDates: boolean;
const nameTitle = { name: "Khufu", title: "Pharaoh" };
const pharaoh = {
  ...nameTitle,
  ...(hasDates ? { start: -2589, end: -2566 } : {}),
};
```

선택적 필드를 이용하여 헬퍼 함수로 원하는 대로 표현하여 해결한다.

```typescript
function addOptional<T extends object, U extends object>(
  a: T,
  b: U | null
): T & Partial<U> {
  return { ...a, ...b };
}
const pharaoh = addOptional(
  nameTitle,
  hasDates ? { start: -2589, end: -2566 } : null
);
```
