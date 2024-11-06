타입스크립트의 심벌(Symbol)은 타입 공간, 값 공간 둘 중 하나에 위치함

## 둘을 구분하는 방법
타입은 컴파일 이후에 JS 코드에서 사라짐

## 혼란스럽게 쓰면 슬퍼짐
아래에는 `Cylinder`를 2번 썼다.
둘은 서로 아무 관계가 없어서 상황에 따라서 타입으로 쓰일 수 있고, 값으로 쓰일 수 있다.
```ts
interface Cylinder {
	radius: number;
	height: number;
}

const Cylinder (radius: number, height: number) => ({radius, height});
```

타입을 확인하기 위해 `instanceof` 함수를 사용하면 `const Cylinder`를 체크한다.
자바스크립트에 이미 있는 `instanceof`는 **런타임 연산자**. 따라서 **값**을 참조한다.

```ts
if (shape instanceof Cylinder) {
	shpae.radius .... // 에러 발생. {} 형식에는 'radius' 속성이 없음
}
```

### 클래스가 타입으로 쓰일 경우
타입으로 쓰일 때는 형태(속성과 매서드), 값으로 쓰일 때에는 생성자가 사용됨
```ts
class Cylinder {
	radius = 1;
	height = 1;
}

if (shape instance Cylinder) {
	shape // 타입은 Cylinder
	shape.radius // 타입은 number
}
```

### typeof 연산
타입에서 쓰일 때는 타입스크립트 타입을 반환.
값의 관점에서는 자바스크립트의 typeof 연산이 사용. -> number, string, boolean, undefined, object, function 중 하나 리턴

- `typeof`는 자바스크립트에서는 값을 문자열로 반환하지만, 타입스크립트에서는 변수나 클래스를 사용해 **타입**을 가져옴

```ts
interface Peson {
	first: string;
	last: string;
}

const p: Person = { first: 'John', last: 'Doe' };
function email(p: Person, sub ....): Response { ... };

type T1 = typeof p; // 타입은 Person
type T2 = typeof email; // 타입은 (p: Person, sub....) => Response

const v1 = typeof p; // 값은 'object'
const v2 = typeof email; // 값은 'function'
```

### new 키워드 사용
```ts
const v = typeof Cyliner; // 값이 'function'

// 이 코드와 다른 코드: type T = Cylinder
type T = typeof Cylinder; // 타입이  typeof Cylinder (?!)
// `T`는 `Cylinder`라는 함수나 클래스의 타입.

declare let fn: T; // fn은 이제 Cylinder 타입
const c = new fn(); // 타입이 Cylinder인 인스턴스를 만들기	
```

### instanceType
InstanceType 제너릭으로 생성자, 인스턴스 타입 전환 가능
```ts
type C = InstanceType<typeof Cylinder> // 타입이 Cylinder
```

## `obj['field']` vs `obj.field`
값이 동일하더라도 타입이 다를 수 있음.
정확한 접근을 위해서 전자를 추천.

만약 `obj.field`를 사용하면 어떻게 될까
이런 메시지를 보게 된다.
`'Person'이(가) 네임스페이스가 아니라 형식이므로 'Person.first'에 액세스할 수 없습니다. 'Person'에서 'Person["first"]'과(와) 함께 'first' 속성의 형식을 검색하려고 했나요?ts(2713)`

```ts
interface Person {
first: string;
last: string;
}

const p: Person = { first: 'Jane', last: 'Doe' };
const first: Person.first = p['first']; // 여기에서 메시지 발생
```

그 이유가 뭘까?
`obj.field`는 컴파일시 변수명이 있기 때문에 타입 체크에 선제적으로 들어간다.
그러나 `obj['field']`에서는 `'field'`가 문자열이기에 조금 더 유연하게 타입 체크를 할 수 있다/