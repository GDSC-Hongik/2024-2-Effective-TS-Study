# 5주차 스터디

# 6장: 타입 선언과 @types

### 아이템 52.

테스트 코드를 작성해야 하지만, 타입스크립트를 테스트하기는 어려움

→ dtslint나 타입 시스템 외부에서 검사하는 도구를 이용해야 함.

타입 선언 파일을 테스트 → 단순히 함수를 실행만 하는 방식으로 일반적으로 적용함.

- 타입을 테스트할 때는 특히 함수의 동일성/할당 가능성의 차이점을 알고 있어야 함.
  - 테스팅을 위해 할당할 때는 두 가지 근본적인 문제
    - 불필요한 변수를 만들어야 함. (미사용 변수 경고를 비활성화 해야 함.)
    - 두 타입이 동일한지 체크하는 것이 아닌, 할당 가능성을 체크함.
- 콜백 함수 → 매개변수의 추론된 타입을 체크해야 함.
- any에 관련하여 주의해야 함. → dtslint같은 도구를 사용해야 함.

# 7장: 코드를 작성하고 실행하기

### 아이템53. 타입스크립트 기능보다는 ECMAScript 기능을 사용하기

타입스크립트는 별도로 자체적인 기능들을 도입했지만, 자바스크립트의 발전과 신규 기능들과의 호환성 문제

→ 런타임 기능보다는 **타입 시스템의 발전**에 집중

**1. 열거형(enum)**

타입스크립트에서는 열거형을 지원하지만, 문제점 때문에 사용을 지양합니다.

•**숫자 열거형**

```tsx
enum Flavor {
	VANILLA = 0,
	CHOCOLATE = 1,
	STRAWBERRY = 2
}
let flavor = Flavor.CHOCOLATE; *// 1*
```

**문제점**

- 다른 숫자를 할당할 경우 런타임 에러를 유발할 가능성이 높음.
- 상수 열거형(const enum)은 컴파일 후 값으로 대체되므로 디버깅이 어려움.

**문자열 열거형**

```tsx
enum Flavor {
  VANILLA = "vanilla",
  CHOCOLATE = "chocolate",
  STRAWBERRY = "strawberry",
}
```

**문제점**

- 구조적 타이핑 대신 명목적 타이핑(Nominal Typing)을 사용하여, 런타임에서 자바스크립트와 호환이 어려움.
- scoop('vanilla')와 같이 문자열을 직접 사용하는 자바스크립트 코드와 충돌 가능.

**대안**

문자열 리터럴 타입 유니온을 사용하는 것이 권장됩니다.

```tsx
type Flavor = 'vanilla' | 'chocolate' | 'strawberry';
let flavor: Flavor = 'chocolate'; *// 정상*
```

**2. 매개변수 속성(Parameter Properties)**

**매개변수 속성 사용 예**

```tsx
class Person {
  constructor(public name: string) {}
}
```

**문제점**

- 간결한 문법이지만, 클래스 속성의 명확성이 떨어져 코드가 혼란스러울 수 있음.
- 속성 정의와 초기화가 혼재되어 가독성을 해침.

**대안**

명시적으로 속성을 선언하고 초기화하는 방식 사용.

```tsx
class Person {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
}
```

**3. 네임스페이스와 트리플 슬래시 임포트**

```tsx
namespace Foo {
	export function bar() {}
}

*///<reference path="other.ts"/>*
Foo.bar();
```

**문제점**

- ES6 모듈(import와 export)이 도입된 이후, 네임스페이스와 트리플 슬래시 임포트는 구식으로 간주.
- 자바스크립트와의 호환성이 낮음.

**대안**

ES6 모듈 사용.

```tsx
import { bar } from "./Foo";
bar();
```

**4. 데코레이터(Decorators)**

**사용 예**

```tsx
@decorator Class {
	method() {}
}
```

**문제점**

- 데코레이터는 아직 ECMAScript 표준이 아니며, 동작이 변경될 가능성이 있음.
- 현재 앵귤러와 같은 특정 프레임워크에서만 주로 사용.

**대안**

- 표준화가 완료될 때까지 사용을 최소화하거나, 표준 방식을 대신 사용할 것.

### 아이템54. 객체를 순회하는 노하우

**객체 순회에서의 타입스크립트 이슈와 해결 방법**

**객체 순회 중 에러 발생 이유**

아래와 같은 코드에서 에러가 발생하는 이유는 for...in 루프에서의 키(k) 타입이 string으로 추론되기 때문.

```tsx
const obj = {
	one: 'uno',
	two: 'dos',
	three: 'tres',
};
for (const k in obj) {
	const v = obj[k]; *// Error 발생*
}
```

**왜 에러가 발생할까?**

- k는 string으로 추론되지만, 실제 객체의 키는 'one' | 'two' | 'three'
- 이 불일치 때문에 타입스크립트가 에러를 발생

**2. 해결 방법**

**키 타입 명시하기**

keyof typeof obj를 사용해 키의 타입을 명확히 지정하면 에러가 해결됩니다.

```tsx
let k: keyof typeof obj; *// 'one' | 'two' | 'three'*
for (k in obj) {
	const v = obj[k]; *// 정상*
}
```

**3. for...in의 타입 추론 제한**

for...in은 객체의 키를 순회하지만, 타입스크립트는 이를 항상 string으로 추론합니다.

```tsx
interface ABC {
  a: string;
  b: string;
  c: number;
}
function foo(abc: ABC) {
  for (const k in abc) {
    const v = abc[k]; // v는 암시적으로 'any' 타입
  }
}
const x = { a: "a", b: "b", c: 2, d: new Date() };
foo(x); // 구조적 타이핑에 의해 정상 호출
```

**문제점**

- abc는 ABC 인터페이스를 따르지만, for...in 키(k)는 항상 string으로 간주됨.
- 구조적 타이핑 때문에 abc에 없는 키(예: d)도 정상적으로 순회.

**4. Object.entries 사용하기**

Object.entries는 객체의 키-값 쌍을 배열로 반환하므로, 더 명확한 타입 추론이 가능합니다.

```tsx
function foo(abc: ABC) {
	for (const [k, v] of Object.entries(abc)) {
		k; *// string 타입*
		v; *// any 타입*
	}
}
```

**장점**

- 타입스크립트가 k를 string, v를 any로 추론
- 객체의 키-값 쌍을 명시적으로 다룰 수 있음.

**5. Object.entries와 다른 활용법**

- forEach**를 사용한 순회**:

```tsx
Object.entries(obj).forEach(([key, value]) => {
  console.log(`${key}: ${value}`); // "one: uno", "two: dos", "three: tres"
});
```

- **Map 객체로 변환**:

```tsx
const obj = { foo: 'bar', baz: 42 };
const map = new Map(Object.entries(obj));
console.log(map); *// Map { 'foo' => 'bar', 'baz' => 42 }*
```

### 아이템55. DOM 계층 구조 이해하기

DOM 조작은 타입스크립트에서 자주 마주치는 영역으로, **정확한 타입 명시**가 중요.

DOM 요소의 계층 구조와 이벤트 타입을 이해하면 발생할 수 있는 오류를 줄일 수 있음.

**DOM 계층의 타입**

- EventTarget: window, XMLHttpRequest와 같은 타입.
- Node: 텍스트 노드, 주석 노드까지 포함.
- Element: 모든 HTML 요소를 포함하는 타입.
- HTMLElement: <i> 같은 표준 요소.
- HTMLXXXElement: 특정 요소에 대한 구체적 타입 (HTMLInputElement, HTMLImageElement 등).

예시

```tsx
<p>
	AND <i>yet</i> it moves
	*<!--quote from Galileo -->*
</p>

p.children; // HTMLCollection [i]
p.childNodes; // NodeList(5) [text, i, text, comment, text]
```

**디버깅 방법**

**1. 정확한 타입 명시**

currentTarget은 기본적으로 EventTarget | null 타입이므로, 이를 특정 요소로 명시해야 함.

**2. 이벤트 타입 구체화**

기본 Event 타입은 추상화되어 있으므로, 특정 이벤트 타입을 지정하면 오류를 줄일 수 있습니다.

- **주요 이벤트 타입**:
  - UIEvent: 사용자 인터페이스 이벤트.
  - MouseEvent: 마우스 클릭, 이동 등.
  - TouchEvent: 터치 스크린 이벤트.
  - WheelEvent: 스크롤 휠 이벤트.
  - KeyboardEvent: 키보드 입력 이벤트.

```tsx
document.addEventListener("click", (event: MouseEvent) => {
  const target = event.currentTarget as HTMLDivElement;
  target.classList.add("clicked");
});
```

**3. Null 값 처리**

document.getElementById 같은 메서드에서 반환값이 null일 가능성을 반드시 고려해야 합니다.

```tsx
const input = document.getElementById("input") as HTMLInputElement | null;
if (input) {
  console.log(input.value);
}
```

### 아이템56. 정보를 감추는 목적으로 private 사용하지 않기

어째서 기껏 만들어놓은 private을 사용하면 안되는지에 대해 의문이 들었음.

**1. 비공개 속성은 런타임에서 완전히 은닉되지 않는다**

- **자바스크립트 클래스**에는 완전한 비공개 속성을 지원하는 기능이 없었음.
- 타입스크립트의 private, protected는 컴파일 시점에서만 동작하며, **컴파일 후에는 제거.**

예를 들어, private 키워드를 사용한 속성도 런타임에서는 접근 가능.

```tsx
class Diary {
  private secret = "cheated on my English test";
}

const diary = new Diary();
(diary as any).secret; // OK (런타임에서 접근 가능)
```

**2. private 접근 제어자는 런타임에 효과가 없다**

- 타입스크립트의 접근 제어자는 컴파일 타임에만 규칙을 강제하며, 컴파일된 결과물에서는 제거.
- 컴파일 후, 아래처럼 평범한 자바스크립트 코드로 변환됨.

```tsx
class Diary {
  constructor() {
    this.secret = "cheated on my English test";
  }
}
const diary = new Diary();

diary.secret; // 일반 속성처럼 접근 가능
```

**3. 정보를 은닉하는 방법: 클로저**

- 자바스크립트에서 정보를 진정으로 은닉하려면 **클로저**를 사용해야 함.
- 클로저를 사용하면 외부에서 접근할 수 없는 변수에 기반한 동작을 정의할 수 있음.

```tsx
declare function hash(text: string): number;

class PasswordChecker {
  checkPassword: (password: string) => boolean;

  constructor(passwordHash: number) {
    this.checkPassword = (password: string) => {
      return hash(password) === passwordHash;
    };
  }
}

const checker = new PasswordChecker(hash("s3cret"));

checker.checkPassword("s3cret"); // true
```

**클로저의 단점**

- 생성자 내부에서 메서드를 정의해야 하므로, **메모리 낭비** 발생.
- 인스턴스마다 메서드의 복사본이 생성됨.

**4. 비공개 필드: 최신 자바스크립트 표준**

- 자바스크립트에서 **비공개 필드**는 # 접두사를 사용하며, 타입스크립트에서도 이를 지원합니다.
- #를 붙인 속성은 런타임에서도 완전히 비공개로 유지됩니다:

```tsx
class Diary {
  #secret = "cheated on my English test";
  revealSecret() {
    return this.#secret;
  }
}

const diary = new Diary();

diary.#secret; // Error
console.log(diary.revealSecret()); // 'cheated on my English test'
```

### 아이템57. 소스맵을 사용하여 타입스크립트 디버깅하기

타입스크립트 코드는 직접 실행되지 않고 타입스크립트 컴파일러에 의해 **자바스크립트 코드로 변환.**

→ 변환된 자바스크립트 코드는 원본 코드와는 구조가 다를 수 있어 디버깅이 어려울 수 있음.

**소스맵(Source Map)의 역할**

- **소스맵**은 변환된 코드와 원본 코드 간의 매핑 정보를 제공함.
- 디버거는 소스맵을 이용해 **원본 코드**와 **변환된 자바스크립트 코드**의 **위치** 및 심벌(symbol)을 연결해 디버깅을 도움. → 대신 원본 코드로 디버깅할 수 있음.
- 보통 자바스크립트로 변환된 코드는 원본과 유사해 디버깅이 쉬운 편.
- 하지만 변환 과정이 복잡한 경우(최적화, 번들링 등)에는 디버깅이 매우 어려워지므로 소스맵이 필요.

tsconfig.json 파일에서 sourceMap 옵션을 설정하면 소스맵 파일을 생성할 수 있음.

```tsx
{
	"compilerOptions": {
		"sourceMap": true
	}
}
```

타입스크립트 컴파일러는 .js 파일과 함께 .js.map 파일을 생성함.
