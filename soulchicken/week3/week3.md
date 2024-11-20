# week3
이번 챕터 요약 : 타입 추론을 알맞게 써서 생산성을 올리자는 주장

## item 19 - 추론 가능한 타입을 사용해 장황한 코드 방지하기
`let x = 12;`는 딱히 x의 타입을 지정하지 않아도 `number` 타입이다.
이런 것처럼 타입 추론 시스템이 할 수 있는 일까지 코드작업을 해서 코드를 장황하게 만들지 말자
특이한 점은 변수의 타입은 처음 등장할 때 결정된다. 따라서 시작이 중요하다. 시작이 반.
그렇지만 추론이 확실하지 않거나 애매하다면 **적절하게** 타입을 작성하자

## item 20 - 다른 타입에는 다른 변수 사용하기
```ts
let id = "12-34-56";
id = 123456;
```

이렇게 한 변수에 다른 변수를 사용하는 법은 지양하자
type을 선언할 때 `number | string`같은 유니온 타입을 사용해도 좋지만 의도를 훼손할 가능성도 존재하고 이후에 의도와 맞지 않은 런타임의 사건이 발생할 수 있다. 차라리 각자 따로 변수를 도입하자.

변수의 값은 바뀔 수 있지만 **타입은 보통 바뀌지 않는다는 관점**을 가져야 한다. 타입이 바뀌는 경우는 '좁혀지는 경우' 밖에 없어야 한다.

## item 21 - 타입 넓히기
타입 넓히기 `widening`
타입스크립트는 정적 분석 시점에서 변수는 **가능한 값들의 집합**으로 타입을 결정한다.
- 할당 가능한 값들의 집합을 유추하는 타입스크립트의 행위를 **타입 넓히기** 라고 한다.
- 타입을 추론하는 시점은 코드에서도 '선언부' 시점. **초깃값**.

```ts
interface Vector3 {x: number; y: number; z: number;}
function getComponent(vector: Vector3, axis: 'x' | 'y' | 'z') {
	return vector[axis];
}
```

런타임은 되지만 편집기에서 잡아낸 오류
```ts
let x = 'x'; // x는 string!!!
let vec = {x:10, y: 20, z: 30};
getComponent(vec, x); // x는 string 타입이라서 'x' | 'y' | 'z' 타입이 아님.
```

타입스크립트는 타입을 추론할 때, 명확성과 유연성 사이 어딘가에 있음
**생산성, 알잘딱깔센**과 분명한 **의도, 예외없음**의 사이 어딘가. 백종원과 안성재.
- 타입 넓히기: 생산성, 알잘딱, 백종원, 유연함 등
- 타입 좁히기: 의도성, 예외없음, 안성재, 안정성 등

일반적인 규칙으로는 변수가 선언된 후로는 타입이 바뀌지 않아야 한다는 원칙이 있음.

타입 넓히기의 과정을 의도대로 제어하기 위해서 몇가지 방법이 있음

### const 사용하기
만능은 아님. let보다는 더 분명하게 좁혀서 타입을 알려주기에 유용함. 앞의 코드에서 `let`이 아니라 `const x = 'x';`이었다면 정상으로 보여졌을 것임.
그러나 튜플, 객체, 배열같은 경우에서는 `const`로 해결이 안될 수 있음.
객체의 경우 내부 속성들이 전부 `let`으로 할당한다고 가정하고 다루게 되기 때문.
```ts
let str = "hello";
// str의 타입은 'string'으로 넓혀짐

const fixedStr = "hello";
// fixedStr의 타입은 리터럴 타입 'hello'로 고정

// 만약 let을 써야한다면
let msg: 'Hello' = 'Hello';
```

### 타입 체커에 문맥 제공하기
item 26에서 다룸.
문맥을 통해서 '기대'하게 되는 타입을 이야기함
앞에서 인자로 들어온 변수가 있거나 다른 타입을 사용한 경우가 있다면 그 내용을 참고해서 타입 지정
```ts
type Config = {
  readonly url: string;
  method: "GET" | "POST";
};

const config: Config = {
  url: "https://example.com",
  method: "GET", // "GET"은 그대로 유지되며 string으로 넓혀지지 않음
};

function greet(msg = "hello") {
  // msg의 기본값이 "hello"이므로, msg의 타입은 string
}

let arr = [1, "hello", true];
// arr의 타입은 (string | number | boolean)[]로 넓혀짐

let arr: (string | number)[] = [1, "hello"];
// arr는 (string | number)[]로 고정되며 추가 타입은 허용 ㄴㄴ
```

### 단언문 사용
앞서 객체 내부 속성은 let으로 선언된 것으로 간주한다고 했다. 거기에 as const를 사용한다면 타입은 좁혀진다.
```ts
const config = {
  url: "https://example.com",
  method: "GET" as const,
};

// config는 url:string과 method:"GET"으로 구성된 객체가 됨
```

### 초기값에 대해서
**변수**는 **초기값**을 통해 가장 넓은 타입으로 만든다.

**변수에서의 `null` 또는 `undefined` 초기값**

```ts
let value = null; // value의 타입은 기본적으로 'any'가 아닌 'null'에서 시작
value = "hello"; // 이제 value의 타입은 null | string
```

**숫자, 문자열, 객체 초기값**
```ts
let num = 42; 
// num은 기본적으로 number 타입으로 추론

let obj = { x: 10 };
// obj는 { x: number }로 타입이 고정되며, 필요시 확장
```

### any 타입을 암시적으로 사용하기
명시적인 타입 선언이 없고 `undefined`, `null`을 할당하면 `any` 타입으로 암묵적으로 정해진다.
```ts
let x; // x의 타입은 암시적으로 'any'
x = 123; // x는 number 타입으로 추론
x = "hello"; // x는 string 타입으로 변경 가능

let y: number; // y는 number로 고정
y = 123; 
y = "hello"; // 오류

```

## item 22 - 타입 좁히기
narrow down. 조금씩 좁혀 들어가서 타입을 구체화.
`if` 문과 `typeof`, `instanceof` 등을 사용해서 타입을 좁힐 수도 있고 **태그 기법**(다른 아이템에서 다뤘던 객체 내부에 태그처럼 사용할 속성을 두는 방식)을 사용하기도 한다.
때로는 커스텀 함수로 타입 식별을 하는 **사용자 정의 타입 가드 방식**도 채용할 수 있다.

### 사용자 정의 타입 가드 방식
함수로 조건문을 캡슐화하는 방법
```ts
// 사용자 정의 타입 가드
function isInputElement(el: HTMLElement): el is HTMLInputElement {
	return 'value' in el;
	// el is HTMLInputElemnt는 함수의 반환이 true인 경우 타입 체커에게 매개변수 타입을 좁힐 수 있다고 알려주는 문법. 일종의 보증
}
```

### in 연산
```ts
type A = { type: "a"; aProp: string };
type B = { type: "b"; bProp: number };

function handle(obj: A | B) {
	if ("aProp" in obj) { // obj는 A로 좁혀짐
	 console.log(obj.aProp);
	 } else { // obj는 B로 좁혀짐
	 console.log(obj.bProp); } }
```

### 태그 기법
아래에서는 `kind`라는 속성을 태그로 사용함
```ts
type Shape = 
  | { kind: "circle"; radius: number }
  | { kind: "square"; side: number }
  | { kind: "triangle"; base: number; height: number };

function getArea(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      // shape는 { kind: "circle"; radius: number }로 좁혀짐
      return Math.PI * shape.radius ** 2;
    case "square":
      // shape는 { kind: "square"; side: number }로 좁혀짐
      return shape.side ** 2;
    case "triangle":
      // shape는 { kind: "triangle"; base: number; height: number }로 좁혀짐
      return (shape.base * shape.height) / 2;
  }
}
```

### if 문을 통한 좁하기
분기점을 통해서 가능한 타입을 좁혀가는 방식.

```ts
function example(value: string | number | boolean) {
  let result: string;
  if (typeof value === "string") {
    result = value.toUpperCase(); // 여기서 value는 string
  } else if (typeof value === "number") {
    result = value.toFixed(2); // 여기서 value는 number
  } else {
    result = value ? "True" : "False"; // 여기서 value는 boolean
  }
  console.log(result);
}
```

## item 23 - 한꺼번에 객체 생성하기
지난주에 공부한 interface로 하는 **선언 병합**처럼 객체도 조금씩 구체화하는 일은 좋지 않다.
객체는 한 번에 정의하자.

```ts
const pt = {};
pt.x = 3; // 이런 식으로 하면 pt 타입은 {}라서 x가 없다고 메시지가 나온다.

// 따라서 이렇게 그냥 한 번에 해버리자.
const pt = {
	x: 3,
	y: 4,
}
```

### 작은 객체를 조합해서 큰 객체를 만들어야 하는 경우
작은 객체 타입이 존재한 상황에서 조합해야만 하는 경우가 있다.
그럴 때에도 한 번에 하자. 한 문장에서 해결이 가능해야한다.
```ts
const pt = {x:3, y:4}
const id = {name: 'Pythagoras'}
const namedPoint = {};
Object.assign(namedPoint, pt, id);

namedPoint.name; // 에러 발생. {} 형식에서는 name 속성이 없다는 메시지.
```

**객체 전개 연산**
```ts
const namedPoint = {...pt, ...id};
namedPoint.name; // 정상 string
```

## item 24 - 일관성 있는 별칭 사용하기
별칭을 남발하면 제어 흐름을 찾아가기 어렵다. 구체적으로 별칭을 만들고자 하는 욕구도 있겠으나 너무 타이트한 구조는 오히려 혼동이 오는 일도 있다.

```ts
const borough = {name: 'Brroklyn', location: [40.688, -73.979]}
const loc = borough.location;

loc[0] = 0; // borough = {name: 'Brroklyn', location: [0, -73.979]}
```

`loc`같은 변수는 만든다는 어떨까.
`loc`의 값을 바꾸면 `borough` 내부도 변경된다. 갑자기 `borough`가 바뀐건데 추적하기 어려워진다.

연산이 어려워서 변수로 뽑아내고 싶다면 **지역**적으로만 사용할 '**임시 변수**'를 활용하자
**예측가능한 코드작업**이 타입 스크립트가 던지는 근본적인 목적임을 상기하기
원하는 방향으로 타입을 좁히는 것에도 방해가 되기에 별칭 사용 남발은 지양하자

## item 25 - 비동기 코드에는 콜백 대신 async 함수를 사용하기

사실 async가 진짜 then 처리보다 훠얼씬 편하긴 함
콜백 지옥에서 탈출하기 위해 promise가 나온 점은 너무 좋지만 그것보다도 더 좋은 async
타입을 관리하는 입장에서도 무척 간편한 기능.

그런데 그래도 비동기 코드 자체는 의미가 있음.
async는 비동기의 동기화고 비동기는 비동기의 가치가 있을 수 있음.
```ts
const promise1 = fetch(url1);
// ... 이런저런 코드 처리 300줄

// 이제 promise1의 데이터가 필요한 시점
promise1.then(...)
```

필요하다면 적절히 `Promise.all`, `Promse.race`를 사용하기도 하고, 구조 분해할당과 await도 유용해보임

## item 26 - 타입 추론에 문맥이 어떻게 사용되는지 이해하기
타입 스크립트는 타입 추론할 때 값만 보는게 아닌 맥락도 파악하는 고도화된 무언가
무언가 틀렸다면 틀렸고 혹시 이건가요? 알려주는 친절함을 가진 무언가

**let 변수**

```ts
type Lang = 'JS' | 'TS' | 'PY'
function setLang(lang: Lang) { }
setLang('JS') // 정상

let lang = 'JS' // lang의 타입은 string
setLang(lang) // string은 Lang 인수로 넣을 수 없어요!

// 해결하고 싶다면
let lang: Lang = 'JS';
let lang = 'JS' as Lang; // 단언문은 지양하는 편
```

-> 다른 해결방법은 **const로 만드는 상수** `const js = 'JS';`
타입 스크립트에서는 문자열을 보고 더 장확한 타입인 문자열 리터럴로 만들기 때문



## item 27 - 함수형 기법과 라이브러리로 타입 흐름 유지하기
자바스크립트는 표준 라이브러리의 역할을 하는 외부 라이브러리가 있음.
jQuery, Lodash 등등

함수형 프로그래밍 기법은 map, forEach등 잘 되있지만 대체해서 요긴하게 사용하는 라이브러리들이 많은 편.
함수형으로 코드를 작성하게 되면 먼저 코드의 길이가 줄어든다. 또한 라이브러리에는 타입도 같이 딸려오는 경우가 많기에 더 작업 시간을 줄일 수 있다.

심지어 Lodash는 코드의 양도 기본 내장 함수보다 줄여주면서 속도도 빠름.
