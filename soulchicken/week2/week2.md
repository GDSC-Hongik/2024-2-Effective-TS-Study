# week2

## item 9 - 타입 단언보다는 타입 선언을 사용하기
**요약 : 타입 선언문 쓰세요.**
```ts
interface Person { name: string };

const alice: Person = { name: 'Alice' }; // 타입 선언
const bob = { name: 'Bob' } as Person; // 타입 단언
```

타입 선언은 할당된 값이 인터페이스를 만족하는지 '검사' -> 잉여 속성 체크가 일어남 (아이템 11)
타입 단언은 추론한 타입이 있다고 하더라도 `Person` 타입으로 일단 믿고 가는 방식. 틀린게 있다면 일단 무시하고 가보는 방식.
-> 단언의 경우 타입 체커보다 우리가 더 정확하다는 확신이 있을 때 사용해야만 함. 예를 들면 타입스크립트는 DOM 객체에 대해서 잘 알지 못함.


## item 10 - 객체 래퍼 타입 피하기
자바를 공부할 때 궁금했던 것인데, 비슷한 내용을 이 책을 통해 알게 되어 좋음.
**요약 : 그냥 기본형 쓰세요.**

`String` (객체 타입) vs `string` (기본형)

string 기본형에는 매소드가 없는데, 자바스크립트에서는 기본형과 객체 타입을 서로 자유롭게 변환
-> 기본형을 객체로 래핑하고 매소드를 호출하고 마지막에 래핑한 객체를 버린다.

따라서 객체에 속성을 추가한다거나 변형을 일으켜도 **버려진다.**
```ts
> x = "hello";
> x.language = 'English'; // 실행됨
'English'
> x.language
undefined
```


## item 11 - 잉여 속성 체크의 한계 인지하기
방에 코끼리를 넣을 수 있을까? 상상이 잘 안되고 어렵다. 그런데 코끼리가 있는 방은 상상할 수 있다.

**잉여 속성 체크** : **타입이 명시된** **변수에 객체 리터럴을 할당**할 때, 해당 타입의 속성이 있는지, 그 외의 속성이 없는지 체크하는 행위

잉여 속성 체크와 할당 가능성(구조적 타이핑에 의거한)은 별도라는 것을 확인할 수 있다.

```ts
interface Room {
	numDoors: number;
	ceilingHeightFt: number;
}

const r: Room = {
	numDoor, ...
	elephant: 'present',
	// 객체 리터럴에서 elephant 속성은 없어요! 라는 메시지 출력
}

// 코끼리가 있는 방.
// 이 상황에서는 잉여속성체크를 하지 않음.
const obj = {
	numDoor, ... 
	elephant: 'present',
}
const r: Room = obj;
```

타입스크립트의 의의는 '의도대로 동작하는 코드'이기에 이러한 **잉여속성체크**를 하게 된다.

### 특장점
- 이를 통해 속성 이름같은 오타같은 실수도 잡아낼 수 있다.
- 타이트하고 명확한 인터페이스를 만들어낼 수 있다. 객체의 형태가 인터페이스와 정확히 일치하도록 강제함으로써 코드의 가독성을 높이고 유지보수에도 장점을 가진다.

### 잉여속성 체크를 원하지 않을 때
- 타입 단언문 사용하기
- 인덱스 시그니처로 속성 추가를 할 수 있다는 여지를 주기
```ts
interface Person { name: string; age: number; [key: string]: any; }
```
- `Partial`
```ts
const person: Partial<Person> = { name: "Alice", hobby: "reading" }; // No error 
```
`Partial<T>`는 `T`의 모든 속성을 선택 속성 (optional, `?`)으로 바꾸는 방법.

다시 언급하지만 의도대로 동작하는 코드를 만들기 위해서는 이런 잉여 속성 체크를 피하는 회피형 코더가 되어선 안될 것이다.

## item 12 - 함수 표현식에 타입 적용하기
**요약 : 함수 선언할 때에는 `function` 말고 `const + 화살표 함수`로 하기**

반복된 함구 시그니처를 통합시키면서 '반복 작업'을 줄이는 응용이 가능.
```ts
// function
function add(a: nunber, b: number) { return a + b; };
function sub(a: nunber, b: number) { return a - b; };
function mul(a: nunber, b: number) { return a * b; };
function div(a: nunber, b: number) { return a / b; };

// 표현식
type BinaryFn = (a: number, b: number) => number;
const add: BinaryFn = (a, b) => a + b;
const sub: BinaryFn = (a, b) => a - b;
const mul: BinaryFn = (a, b) => a * b;
const div: BinaryFn = (a, b) => a / b;
```
라이브러리를 만들 때에도 공통 콜백으로 묶는 것도 좋은 일

### 다른 함수의 시그니처를 가져오기
fetch 함수의 형태를 그대로 가져오는 방법을 예시로 보여줌
```ts
// fetch의 d.ts
declare function fetcvh(
	input: ReqeustInfo, init?: RequestINit
): Promise<Response>;
```

```ts
const checkedFetch: typeof fetch = async (input, init) => {
	const response = await fetch(input, init);
	if (...){
		throw new Error(...);
		// 만약 throw가 아니라 return을 사용하게 되면 Promise<Response>가 아니게 되서 에러 발생
	}
	return response;
}
```

## item 13 - 타입과 인터페이스의 차이점 알기
**요약 : 둘 다 비슷한 점이 많아서 평소엔 팀원 합의 하에 일관된 코드 작성이 더 중요해보임. 특수한 경우에만 interface, type을 구분하는게 도움이 되긴 할 듯 함**

- 튜플은 type을 사용할 때 더 간결함
- 보강기법은 interface / 타이트한 프로퍼티 세팅은 type

### 선언 병합
인터페이스는 **보강 기법**을 사용할 수 있다.
```ts
interface State {
	name: string;
}

interface State {
	population: number;
}

const seoul: State {
	name: 'seoul', population: 1000;
}
```
보강 기법, 선언 병합은 여러 라이브러리에 호환할 때 유리하다.

## item 14 - 타입 연산과 제너릭 사용으로 반복 줄이기
**요약 : 중복된 속성이 있을 수 있음. 타입에서도 일반 코드와 마찬가지로 반복해서 사용하지 않아야함**

extends, pick, partial, typeof 등 사용할 수 있는 방법은 무궁무진하고 어떤식으로든 '반복'을 줄이면 된다.

### 확장의 개념
```ts
interface Person {
	firstName: string; lastName: string;
}

// bad
interface PersonWithBirthDate {
	firstName: string; lastName: string;
	birth: Date;
}

// good 인터페이스 확장
interface PersonWithBirthDate extends Person {
	birth: Date;
}
```

### 부분 표현
```ts
interface State {
	userId: string; pageTitle: string;
	recentFiles: string[]; pageContents: string;
}

// bad
interface TopNavState {
	userId: string; pageTitle: string; recentFile: string[];
}

// soso 인덱싱 활용
interface TopNavState {
	userId: State['userId'];
	pageTitle: State['pageTitle'];
	recentFiles: State['recentFiles'];
}

// good  매핑
type TopNavState = {
	[k in 'userId' | 'pageTitle', 'recentFiles']: Statue[k]
}

// good good 'Pick' 사용
type TopNavState = Pick<State, 'userId' | 'pageTitle' | 'recentFiles'>;
```

## item 15 - 동적 데이터에 인덱스 시그니처 사용하기
**요약 : 객체에서 동적 인덱스 시그니처는 런타임에서 어떻게 될 지 예측이 안될 때 사용하자. 그렇지 않다면 Record나 매핑도 고려하자**

자바스크립트의 단점이자 장점은 유연하다는 점. 객체도 엄청 쉽게 만들 수 있음.
`type Rocket = {[property: string]: string};`
이렇게 인덱스 시그니처를 명시하면 유연하게 속성을 때려넣을 수 있다.
단점은 너무 자유분방해서 생긴다. 따라서 정말 **런타임 환경에서 예측할 수 없는 상황**에서만 써야한다.
- 의도한 대로 키 값이 일치한다는 보장도 없다. 이를 보정하려면 `undefined`를 고려하는 것도 방법이다.
- '어느정도'만 예측된다면 Record나 매핑된 타입을 사용하는 것도 좋다.

## item 16 - number 인덱스 시그니처보다는 Array, 튜플, ArrayLike를 사용하기
**요약 : 객체의 키 값은 숫자로 쓰려고 해도 런타임에서 숫자가 아님.**

자바스크립트에서 객체란 기/값 쌍의 모음. 키는 주로 문자열, 심벌일 수도 있긴 함.
배열은 숫자 인덱스를 사용할 수 있는데, 배열은 객체다. 그래서 일반 객체에서도 숫자 인덱스, 숫자 키를 허용할까?

자바스크립트에서는 속성 명을 숫자로 사용하면 문자열로 바꿔버린다.

```ts
// d.ts
interface Array<T> {
	...
	[n: number]: T;
}
```
타입스크립트에서는 숫자로 일단 받아들이는 것이 타입 체크시 유리하기 때문에 이렇게 구현되어 있다.
그러나 여전히 런타임에에서는 문자열로 바뀔 것이다. 실제 동작과 타입 체크는 다르다는 점을 여기에서도 알 수 있다.


## item 17 - 변경 관련된 오류 방지를 위해 readonly 사용하기

함수에 파라미터로 들어오는 객체를 수정하지 않는다면 readonly를 사용하자
오류의 범위를 줄여줄 수 있다. 타입스크립트는 readonly를 보고 변하지 않을 것이라는 보장을 받게 만든다. 명시적으로 표시하는 것이 컴파일러와 사람 둘 다에게 좋다고 한다.

주의 : 얕게 동작한다.


## item 18 - 매핑된 타입을 사용하여 값을 동기화하기
**요약 : 타입의 속성이 바뀌거나 추가될 때에 우리가 직접 코드를 바꾸는건 실수도 생기고 번거롭다. 따라서 자동 동기화가 되는 방식인 매핑을 잘 써먹자.**

예시로 산점도 그리는 코드를 보여주는데, 이 때 이야기로 나오는 것은 **실패에 닫힌 접근법**과 **실패에 열린 접근법**이다.
실패에 닫힌 접근법은 오류 발생시, 뭔가 변경이 있을 시 적극적으로 대처하는 이상징후가 없기를 바라는 방식이라고 보인다. 반대로 실패에 열린 접근법은 소극적인 대처이자 여유로운 대처로 보인다.
성능과 보안의 관계처럼 서로 트레이드 오프 관계이다.

위 2가지 방식으로 산점도 그리는 코드를 보면 이상적이라고 보이지 않는다. 새로운 속성이 추가되면 직접 타입스크립트가 반응할 수 있도록 만들어줘야한다. item 14에서도 사용한 매핑을 사용하자. 속성을 동기화 할 수 있다.