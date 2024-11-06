**집합**이라는 개념으로 접근하기
- `never` 타입은 **공집합**. 쓰이는 예시로는 에러 처리 (`throw`)에서 쓰일 것 같음. 
- `strictNullChecks` : config에서 여부에 따라 `null`, `undefined`는 `number` 타입이 될 수도 있고 아닐 수도 있음.

## 리터럴(literal) 타입
유닛(unit) 타입이라고도 부름. **값 하나!** 만을 의미
```ts
type A = 'A';
type B = 'B';
type Twelve = 12;
```

### 유니온(union) 타입
합집합의 개념
타입 오류에서 '할당 가능한'이라는 문구를 볼 수 있는데, 이를 해석하면 '-의 부분 집합', '-의 원소'로도 쓸 수 있음.
```ts
type AB = 'A' | 'B';
type AB12 = 'A' | 'B' | 12;

const a: AB = 'A';  // 할달할 수 있음.
const c: AB =  'C'; // 할당할 수 없음.

// 같은 집합의 범위
const ab: AB = Math.random() < 0.5 ? 'A' : 'B'; // 가능!

// 상위 집합 사용
const ab12: AB12 = ab; // 가능! 'ab'는 {A, B}만 되고, 'AB12'는 {A, B, 12}가 됨

declare let twelve: AB12;
const back: AB = twelve; // AB12라서 안되는 상황
```

## 객체의 집합 범위
```ts
interface Indentified {
	id: string;
}
```
**구조적 타이핑**에 의해 `id: string`인 객체는 모두 `Identified`이다.
따라서 다른 속성을 가져도 `Identified`이다.

### 교집합 (intersection) `&`
**집합의 범위**의 개념에서 보기
```ts
interface Person {
	name: string;
} // birth, death 속성이 있어도 되고 없어도 됨

interface Lifespan {
	birth: Date;
	death?: Date;
} // name 속성이 있어도 되고 없어도 됨

type PersonSpan = Person & Lifespan;
```

{name 속성} 객체 타입과 {birth, death 속성}의 객체 타입을 합치면 `never`이 아니다.
```ts
const ps: PersonSpan = {
	name: 'John',
	birth: new Date('1912/06/23'),
	death: new Date('1954/06/07'),
}
```

`name` 속성을 갖는 집합과 `birth`, `death` 속성을 가진 집합의 **교집합**

`never`은 이렇게 해야 나온다.
```ts
type K = keyof (Person | Lifespan); // never
```

### 명확한 유니온과 인터섹션 정의 (두피가 얼얼)
```ts
keyof (A & B) = (keyof A) | (keyof B)
keyof (A | B) = (keyof A) & (keyof B)
```

## `extends` 상속 관계로 만들기
`extends`는 `-에 할당 가능한`, `-의 부분 집합`으로 쓰임

```ts
interface Vector1D = {x: number};
interface Vector2D extends Vector1D = {y: number};
interface Vector3D extends Vector2D = {z: number};

// 위와 같음
interface Vector1D = {x: number};
interface Vector2D = {x: number, y: number};
interface Vector3D = {x: number, y: number, z: number};
```

`Vector1D` > `Vector2D` > `Vector3D`

### Generic 타입 활용
`extends`에서 제너릭을 통해 한정시킬 수 있음
```ts
// key는 'string' 타입의 집합에 있어야함
function getKey<K entends string>(val: any, key: K) {
	// 로직
}

getKey({}, 'x'); // x는 string의 부분 집합
getKey({}, Math.random() < 0.5 ? 'a' : 'b'); // a | b는 string의 부분 집합
getKey({}, document.title); // string은 string의 부분 집합
getKey({},12); // 놉
```

### 배열과 튜플
```ts
const list = [1, 2]; // 타입은 number[]
const tuple: [number, number] = list; // 에러. number[] 타입이 더 상위 집합
```

