# week5

*6장 - 타입 선언과 @types 파트 중 item 45 - 51*

6장의 내용은 주로 npm같은 곳에서 받는 의존성에 대한 내용을 잡는다. 의존성을 관리하다보면 생기는 문제, 해결법, 특히 타입스크립트를 다루면서 의존성을 통제할 때의 이야기를 다룬다.

## item 45 - devDependencies에 typescript와 @types추가하기

node 진영을 사용하는 우리는 패키지 매니저를 사용하게 되고, 의존성 리스트는 `package.json`에서 관리한다.

`dependencies` : 현재 프로젝트를 실행하는 데 필요한 라이브러리. 런타임에 사용되는 라이브러리.
`devDependencies` : 개발하고 테스트할 때만 필요하지만 런타임에서는 필요 없는 라이브러리. **타입스크립트도 이 곳에 속한다.**
`peerDependecies` : 런타임에 필요하지만, 의존성을 직접 관리하지 않는 라이브러리. 일종의 패키지의 패키지(?). 라이브러리간의 호환이나 충돌을 방지하기 위해 따로 관리가 되어준다.


### 타입스크립트 설치
타입스크립트를 global로 설치해서 시스템 레벨로 설치할 수 있지만, 협업할 때 버전 이슈가 생길 수 있기에 권장하지 않는다.
-> `devDependencies`로 설치
어짜피 IDE와 빌드 툴은 `devDependencies`로 설치되면 알잘딱으로 인식한다.

### @types
타입 의존성을 고려해야한다. 라이브러리에 타입 라이브러리가 궁금하다면 [DefinitelyTyped](https://definitelytyped.org/) 사이트에서 확인하자.
이러한 타입 라이브리러도 `devDependencies`에 들어간다.

## item 46 - 타입 선언과 관련된 세 가지 버전 이해하기
의존성 관리, 버전 이슈는 생각보다 빈번하고 힘든 일.
타입스크립트를 사용한다면 오히려 의존성 관리가 복잡해진다. 타입 스크립트를 사용한다면 세 가지 사항을 고려야한다.
1. 라이브러리의 버전
2. 타입 선언(@types)의 버전
3. 타입스크립트 자체의 버전

셋 중 하나라도 맞지 않으면, 의존성과 관계 없이 오류가 발생할 수 있다.

**라이브러리는 업데이트했는데, 타입 선언이 업데이트 되지 않은 경우**
그냥 버전을 맞춰주면 된다. 만약 타입 선언 라이브러리가 업데이트 되지 않았다면 보강기법으로 직접 추가하거나 업데이트 코드를 직접 PR 날리는 방법이 있다.

**만약 라이브러리보다 타입 선언이 최선인 경우**
타입 선언의 버전을 낮추면 된다.

**만약 타입 스크립트 버전이 다른 라이브러리보다 낮은 경우**
1. 타입 스크립트의 버전을 올린다.
2. 라이브러리 버전을 내린다.
3. (만약 라이브러리에서 typesVersions 관리를 하는 경우) 맞춰서 설치한다.

**만약 @types 의존성이 중복될 경우**
중복 선언이 존재해버린다. 두 라이브러리에서 충돌이 발생한다면 한 버전을 업데이트하거나 내려서 상호 호환되게 맞출 수 있다. 타입 선언을 따로 작성하는 방법도 있을 수 있다.

### 시멘틱 버전 규칙 (Semantic Versioning, SemVer)
``MAJOR.MINOR.PATCH``
1. **MAJOR (주 버전)**
    - **의미**: 이전 버전과 호환되지 않는 변경 사항이 있을 때 증가.
    - **예**: 기존 API를 변경하거나 제거하여 코드가 호환되지 않을 때.
    - **예시**: `1.0.0` → `2.0.0`
2. **MINOR (부 버전)**
    - **의미**: 이전 버전과 **호환되는** 새로운 기능이 추가되었을 때 증가.
    - **예**: 새로운 API를 추가하거나 기존 기능을 개선하지만, 기존 코드와 호환성을 유지하는 경우.
    - **예시**: `1.0.0` → `1.1.0`
3. **PATCH (수정 버전)**
    - **의미**: 이전 버전과 **호환되는** 버그 수정이 이루어졌을 때 증가.
    - **예**: 기능 추가 없이 버그나 보안 문제를 해결한 경우.
    - **예시**: `1.0.0` → `1.0.1`

-> 따라서 주 버전은 당연히 맞아야하고, 부 버전이 같다면 API 명세가 바뀌지 않기에 호환된다.

### 라이브러리 자체적으로 하는 타입 번들링
**내 힘으로 해결할 수 없는 경우**
라이브러리를 타입스크립트로 작성하여 타입 선언이 포함된 경우도 있는데, 타입 스크립트가 업데이트가 되면 종종 문제가 생기곤 한다. 마이크로소프트가 DefinitelyTyped 사이트의 모든 타입 선언을 점검하니까 걱정하지말라구.

**프로젝트 내에 타입 선언이 다른 라이브러리의 타입 선언에 의존한다면**
보통 의존성이 `devDependencies`에 들어가지만 그렇지 않은경우도 있다. @types가 있는지 없는지 확인해서 설치

### 필자의 정리 겸 변명
굉장히 라이브러리 복잡도가 증가하지만 그 보상으로 개발 생산성이 증가한다. 코드 복잡도가 높아지는 것보다 라이브러리 복잡도를 컨트롤하는 것이 더어렵지 않다. 겁은 겁대로 줬지만 어짜피 써야하니까 참고하시라.

## item 47 - 공개 API에 등장하는 모든 타입을 익스포트하기
타입스크립트를 사용하다보면 서드파티 모듈에서 익스포트하지 않은 타입 정보가 필요한 경우가 생긴다. 요즘엔 도구가 좋아서 어지간하면 참조하는 방법을 찾아볼 수 있다고 한다.

라이브러리 제작자는 최대한 익스포트를 해주세요. 라는 내용.
만약 라이브러리에서 특정 타입을 숨기고 싶다면 제너릭이나 우회해서 보이는 방법도 있을 것이다. 그러나 굳이?

## item 48 - API 주석에 TSDoc 사용하기
JSDoc을 타입스크립트 관점에서 TSDoc이라고 한다고 한다.
무슨 의미가 있나 싶지만... 차이점이 있다!

interface, type에도 주석을 사용할 수 있다는 점이 첫 차이점이고
두 번째는 코드에서 함수에 매개변수를 집어 넣을 때 그 변수 필드에 대한 설명이 잘 나온다는 점이 장점이다.
그리고 굳이 **JSDoc처럼 타입정보를 기록하지 않아도 된다.**

*팁 : 주석은 수필이 아니니까 장황해선 안된다.*

```ts
/**
/ 인사말을 생성하는 함수
/ @param name 사람의 이름 <- Good
/ @param {string} name 사람의 이름, 예를 들면 김동현같은 부를 수 있는 사람의 이름을 뜻한다. <- Bad. 타입정보가 있고, 장황하다.
*/
function greetFullTSDoc(name: string ....) { ... }
```

### TypeDoc
TSDoc 주석을 기반으로 TypeScript 프로젝트의 문서를 자동생성해주는 도구.
```
$ npm install typedoc --save-dev
$ npx typedoc --entryPoints src/index.ts --out docs
```
[Typedoc 공식문서])(https://typedoc.org/)
만들어진 문서는 투박한 스웨거 느낌.

## item 49 - 콜백에서 this에 대한 타입 제공하기
let, const는 렉시컬 스코프지만 this는 다이나믹 스코프를 띤다.
멋진 단어를 쓰지만 이 둘의 차이는 '정의된 방식'에서 스코프가 형성되는가, 호출 방식에 따라 스코프가 형성되는가의 차이다.

호출 위치에서 결정되다보면 특히 콜백 함수에서 this는 의도와 다르게 바인딩 될 수도 있다.
```ts
class Counter {
  count = 0;

  increment() {
    setTimeout(function () {
      this.count++;
      console.log(this.count); // Error: `this` is undefined or incorrect
    }, 1000);
  }
}
```
this는 대체로 객체의 인스턴스를 참조하는 클래스 문법에서 사용되는데, 위에 사례에서 setTimeout의 this는 Counter Class를 가리키지 않기에 undefined나 window를 가리키게 되며 타입 에러가 발생한다.

### 타입 스크립트는 this를 추론하지 않는다.
**명시적으로 타입 지정**
```ts
// this 지정
function callback(this: HTMLButtonElement, event: Event) {
  console.log(this.textContent); // `this`가 HTMLButtonElement임을 알 수 있음
}

const button = document.querySelector("button")!;
button.addEventListener("click", callback);
```

**클래스 내 콜백에서 this 타입 명시**
```ts
class Counter {
  count = 0;

  increment(this: Counter) {
    setTimeout(function (this: Counter) {
      this.count++;
      console.log(this.count);
    }.bind(this), 1000); // 명시적으로 `this` 바인딩
  }
}

const counter = new Counter();
counter.increment(); // 1
```

**화살표 함수 사용하기**
화살표 함수는 this를 외부 스코프에 고정시킨다. 화살표 함수의 this는 생성 시점에 상위 스코프로 this를 고정하기에 가장 간단한 방법이다.
```ts
class Counter {
  count = 0;

  increment() {
    setTimeout(() => {
      this.count++; // `this`는 클래스 인스턴스를 가리킴
      console.log(this.count);
    }, 1000);
  }
}

const counter = new Counter();
counter.increment(); // 1
```

### this에 타입을 제공하면서 얻는 점
타입 안정성 : 올바른 타입인지 컴파일에서도 검사할 수 있다.
문서화, 가독성 : 코드 읽기가 쉬워진다.

[Javascript 미세팁 - "function"은 아예 쓰지 마세요](https://www.youtube.com/watch?v=LPEwb5plEoU)

## item 50 - 오버로딩 타입보다는 조건부 타입을 사용하기
함수가 여러 입력 타입을 받을 수 있으며, 이에 따라 반환 타입도 달라질 수도 있다. 타입스크립트는 함수 구현체는 하나더라도 타입 선언은 몇개든 만들어낼 수 있다.
```ts
// 문자열을 입력받으면 길이를 반환하고, 배열을 입력받으면 요소의 개수를 반환
function getLength(x: string): number;
function getLength(x: any[]): number;
function getLength(x: any): number {
  return x.length;
}

```

위 함수처럼 만드는 시도는 너무 좋다. 제너릭을 사용한다면 더 간결(?)하게 만들 수도 있다.
타입을 너무 구체적으로 만들어보려는 시도는 너무 좋지만 과할 수 있다.

**오버로딩은 단점이 있다.**
1. 유지보수가 어렵다 : 케이스가 많아질수록 타입 정의가 복잡해지고 중복 코드가 발생한다.
2. 확장성이 부족하다 : 새로운 타입을 추가할 때마다 오버로드 정의를 수정해야한다. 
3. 타입 추론 제약이 있다 : 오버로드는 명시적으로 정의하는 타입 외에는 타입 추론에 혼동이 오면서 어려워진다.

### 조건부 타입
조건부 타입은 `T extends U ? X : Y` 형태를 이야기 한다.
조건부 타입보다 오버로딩이 작성하기 쉽지만 조건부는 개별 타입의 유니온으로 일반화할 수 있기 때문에 타입이 정확해지고 코드의 중복도 적다.

```ts
type GetLength<T> = T extends string | any[] ? number : never;

function getLength<T>(x: T): GetLength<T> {
  return (x as any).length;
}
```

### 조건부와 오버로딩 코드 비교
```ts
// 오버로딩
function processValue(x: string): string;
function processValue(x: number): number;
function processValue(x: boolean): boolean;
function processValue(x: any): any {
  return x;
}

// 조건부 타입
type ProcessedValue<T> = T extends string
  ? string
  : T extends number
  ? number
  : T extends boolean
  ? boolean
  : never;

function processValue<T>(x: T): ProcessedValue<T> {
  return x; // 타입에 따라 반환
}
```
유지보수의 관점에서 복잡한 로직의 시그니처가 깔끔해보인다.

### infer 문법
`infer`는 **조건부 타입(conditional types)** 내에서 사용되며, TypeScript의 타입 추론(type inference)을 활용하여 특정 타입의 구성 요소를 추출하거나 재활용할 때 사용. `infer`는 타입을 "유추"하도록 지시하는 역할.
`T extends SomeType<infer U> ? X : Y`
- `T extends SomeType<infer U>`: `T`가 `SomeType`의 형태를 가지면 `U`라는 변수에 타입 정보를 "추론".
- `U`: 추론된 타입이 담기는 자리.
- `X`: 조건이 참일 경우 사용되는 타입.
- `Y`: 조건이 거짓일 경우 사용되는 타입.

**예를 들어 배열의 요소 타입 추출**
```ts
type ElementType<T> = T extends (infer U)[] ? U : never;

type StringArray = ElementType<string[]>; // string
type NumberArray = ElementType<number[]>; // number
type NonArray = ElementType<string>; // never
```


## item 51 - 의존성 분리를 위해 미러 타입을 사용하기
### CSV 파일을 파싱하는 라이브러리를 만든다는 가정
csv 파일을 매개변수로 받고, 열 이름을 값으로 매핑하는 객체를 생성하여 배열로 반환하는 로직이 될 것.

그런데 NodeJS 개발자들이 끌어다 쓸 경우 매개변수로 Buffer 타입을 사용할 수 있도록 만들고 싶다고 가정.

```ts
function paresCSV(contents: string | Buffer): { [column: string]: string}[] {
	if (typeof content === 'object') {
		// 버퍼인 경우
	}
}
```

위처럼 Buffer를 사용하고 싶다면 Node 타입을 설치하면 된다.
`$ npm install -D @types/node`

그러나 문제는 2가지다.
타입스크립트가 아닌 자바스크립트 개발자, Buffer가 필요하지 않은 타입스크립트 개발자.

### 구조적 타이핑 사용으로 해결하기
`@types/node`를 사용하지 않고, 필요한 매소드와 속성만 별도 작성하기
```ts
interface CsvBuffer {
	toString(encoding: string): string;
}

function parseCSV(contents: string | CsvBuffer)...
```

CsvBuffer를 Buffer와 호환되도록 짧게 만들어주면 된다.
따라서 호출이 가능해진다.


### 미러타입이 뭔가요?
미러 타입은 **원본 타입(original type)을 직접 사용하지 않고, 그것을 반영(reflect)한 별도의 타입**. 이를 통해, 코드가 특정 라이브러리나 구현 세부사항에 강하게 결합되지 않도록 만든다.
- 미러 타입은 특정 의존성에 대한 간접 참조 역할.
- 코드 작성 시 라이브러리나 외부 API에 강하게 결합되는 문제를 완화할 수 있다.

### 왜 미러 타입을 사용해야 하나?
- **결합도 감소**: 특정 라이브러리나 API와의 직접적인 의존성을 줄여 코드의 재사용성과 변경 용이성을 준다.
- **유연성 확보**: 외부 라이브러리 변경에 따른 영향을 최소화할 수 있다.
- **테스트 가능성 향상**: 의존성을 직접적으로 사용하지 않으므로 목(mock) 객체를 사용한 테스트가 더 쉬워진다.
