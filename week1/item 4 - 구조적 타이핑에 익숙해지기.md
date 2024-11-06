## 덕 타이핑
JS는 본질적으로 **덕 타이핑(Duck Typing)** 기반
- 어떤 함수의 매개변수 값이 모두 제대로 주어진다면, 그 값이 어떻게 만들어졌는지 신경쓰지 않음. (객체의 형태가 아니라 동작에 의해 타입이 결정!)
- **덕 타이핑** : 객체가 어떤 타입에 부합하는 변수와 메서드를 가질 경우 객체를 해당 타입에 속하는 것으로 간주
	- *만약 어떤 새가 오리처럼 걷고, 헤엄치고, 꽥꽥 거린다면 나는 그 새를 오리라고 부를 것이다.*

> 타입 스크립트는 동작(매개변수 값이 요구사항을 만족한다면) 타입이 무엇인지 신경 쓰지 않는 동작을 그대로 모델링

Duck Typing의 특징은 객체가 필요한 메서드나 속성을 포함하면, 그 객체를 해당 타입으로 간주하는 방식으로 유연하게 동작

```js
function quack(duck) {
	if (duck.quack) { // quack 메서드가 존재한다면
		console.log("Quack! Quack!");
	} else {
		console.log("Not a duck!");
	}
}
const duck = { quack: () => console.log("Quack!") };
const dog = { bark: () => console.log("Bark!") };

quack(duck); // "Quack! Quack!"
quack(dog); // "Not a duck!"
```

### 덕 타이핑의 문제점
덕 타이핑과 구조적 타이핑은 둘 다 유연하다는 장점이 있음
그러나 덕 타이핑의 단점은 **타입 안정성**

```js
function processDuck(duck: { quack: () => void }) {
  duck.quack(); // quack 메서드가 없으면 런타임 오류 발생
}

const notADuck = { walk: () => console.log("walking") };
processDuck(notADuck); // 런타임 에러
```

## 구조적 타이핑
structural typing
덕 타이핑의 문제점은 구조적 타이핑에서는 생기지 않음. 타입 체크를 통해 컴파일 타임에 오류를 방지.
```ts
interface Vector2D {
	x: number;
	y: number;
}

function calculateLength(v: Vector2D) {
	return Math.sqrt(v.x * v.x + v.y * v.y);
}

interface NamedVector {
	name: string;
	x: number;
	y: number;
}

const v: NamedVector = { name: 'v', x: 3, y: 4 };

calculateLength(v); // 결과는 5
```

`Vector2D`와 `NamedVector`는 관계가 없다.
타입 시스템은 자바스크립트 런타임 동작을 모델링한다. `NamedVector`의 구조가 `Vector2D`에 호환되기에 `calculateLength` 호출 가능

### 구조적 타이핑으로 생기는 문제
`calculateLength`를 사용할 수 있는 3D 벡터에서는 문제가 된다. 호환이 되버려서 생기는 문제.
**타입스크립트의 타입 시스템은 좋든 싫든 '열려(open)'있다.**
```ts
interface Vector3D {
	x: number;
	y: number;
	z: number;
}

// 벡터의 길이를 1로 만드는 정규화 함수
function normalize(v: Vector3D) {
	const length = calculateLength(v);
	return {
		x: v.x / length,
		y: v.y / length,
		z: v.z / length,
	};
}

normalize({x: 3, y: 4, z: 5}) // {x: 0.6, y: 0.8, z: 1} 길이가 1이 아님

// 틀린 함수
function calculateLengthL1(v: Vector3D) {
	let length = 0;
	for (const axis of Object.keys(v)) {
		const coord = v[axis]; // axis는 any가 됨.
		length += Math.abs(coord);
	}
	return length;
}
calculateLengthL1({x:3, y:4, z:1, address: '123 boardway'}); // NaN 리턴

// 올바른 함수
function calculateLengthL1(v: Vector3D) {
	return Math.abs(v.x) + Math.abs(v.y) + Math.abs(v.z);
}
```

## 클래스에서의 구조적 타이핑
```ts
class C {
	foo: string;
	constructor(foo: string) {
		this.foo = foo;
	}
}

const c = new C('foo');
const d: C = { foo: 'foo' }; // 정상
```
구조적으로 필요한 속성과 생성자가 존재하기에 문제가 없음.
생성자에 단순 할당이 아닌 연산 로직이 존재한다면, d는 문제가 발생될 수 있음.

## 테스트코드에서의 구조적 타이핑
### 테스팅 해야하는 함수
DB에 쿼리하고 결과를 처리하는 함수라는 가정
```ts
interface Author {
	first: string;
	last: string;
}

function getAuther(database: PostgresDB): Author {
	const authorRows = database.query('SELECT first, last FROM authors');
	return authorRows.map(row => ({
		first: row[0],
		last: row[1]
	}));
}
```

### 구조적 타이핑으로 구체적인 인터페이스 구현
테스트하기 위해서는 **모킹(mocking)** 한 postgreDB를 생성해야함.
**구조적 타이핑**을 활용하여 **더 구체적인 인터페이스를 정의**하는 것이 더 나은 방법.
runQuery 메소드가 있기에 postgre 환경에서도 사용할 수 있음.
```ts
interface DB {
runQuery: (sql: string) => any[];
}

function getAuthors(database: DB): Author[] {
	const authorRows = database.runQuery('SELECT first, last FROM authors');
	return authorRows.map(row => ({
		first: row[0],
		last: row[1]
	}));
}
```

### 테스트 코드
테스트 코드에도 구조적 타이핑으로 객체를 정의해놨기에 더 간단한 객체를 매개변수로 사용할 수 있음.
실제 환경의 데이터베이스에 대한 정보가 불필요하고 모킹 라이브러리도 필요하지 않음.
DB를 추상화함으로써, 로직과 테스트를 postgre의 구현과 분리됨.
**-> 라이브러리 간의 의존성을 분리**
```ts
test('getAuthors', () => {
	const authors = getAuthors({
		runQuery: () => [['John', 'Doe'], ['Jane', 'Smith']]
});

expect(authors).toEqual([
	{ first: 'John', last: 'Doe' },
	{ first: 'Jane', last: 'Smith' }
	]);
});
```

## 타입 좁히기와 타입 가드
`makeSound` 함수는 `Cat`과 `Dog`의 공통적인 구조가 없기 때문에 직접 타입 가드를 활용하여 필요한 메서드를 호출. **타입 좁히기**는 구조적 타이핑에 기반을 두면서 런타임에 타입 안정성을 높힘.
- 타입 좁히기 : 넓은 타입에서 조건문으로 구체적인 타입으로 narrow down
- 타입 가드 : `typeof`, `instanceof`, `in`, 사용자 정의 타입 가드 등을 통해 타입 체크를 하는 과정.
```ts
interface Cat {
  meow: () => void;
}

interface Dog {
  bark: () => void;
}

function makeSound(animal: Cat | Dog) {
  if ("meow" in animal) {
    animal.meow();
  } else {
    animal.bark();
  }
}

```