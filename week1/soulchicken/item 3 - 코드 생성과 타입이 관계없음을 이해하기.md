
## 타입스크립트의 2가지 역할
- TS / JS 코드를 브라우저에서 동작할 수 있도록 구버전 JS로 트랜스파일 (transpile <=> translate + compile)
- 코드의 타입 오류 체크
	-> 이 2가지 역할은 완벽히 독립적

즉, 두 특성이 독립적이라는 이야기는 타입체크가 실패해도 트랜스파일을 할 수 있다는 이야기
코드의 실행 시점에서도 타입은 영향을 미치지 않는다는 이야기

### 타입 오류가 있는 코드도 컴파일 가능
C, Java처럼 타입 체크와 컴파일이 동시에 이뤄지는 것이 익숙하면 당황스러운 상황
타입스크립트의 오류는 Warning에 가까움. 문제를 알려주지만 빌드를 멈추지 않음

만약 오류가 있을 때 컴파일하고 싶지 않다면 tsconfig에서 `noEmitOnError` 옵션을 설정

### 런타임에서는 타입 체크 불가능
**타입스크립트에서 타입은 '제거 가능(erasable)'**
-> 실제로 자바스크립트로 컴파일되는 과정에서 모든 인터페이스, 타입구문, 타입은 그냥 제거 됨

```ts
interface Square {
	width: number;
}

interface Rectangle extends Square {
	height: number;
}

type Shape = Square | Rectangle;

function calculateArea(shape: Shape) {
	if (shape instanceof Rectangle) {
		// 'Rectangle'은 형식만 참조헀지만
		// 값으로 사용해버림!
		return shape.height * shape.width;
		// ~~~~~~ 속성 'height' 은 'Shape'에 없음
	} else {
		return shape.width * shape.width;
	}
}
```

위의 코드를 명확하게 하려면 height 속성을 If문으로 체크하는 방법이 있음.
`if ('height' in shape)` <- 이런 느낌
신기하게도 `type Shape = Square | Rectangle`은 `Rectangle`을 타입으로 참조하지만 `shape instanceof Rentangle` 부분은 값으로 참조된다.

아니면 명시적으로 런타임에 접근 가능한 타입 정보를 명시적으로 저장하는 **'태그'기법**이 있음.
```ts
interface Square {
	kind: "square";
	width: number;
}

interface Rectangle {
	kind: "rectangle";
	height: number;
	width: number;
}

type Shape = Square | Rectangle;
function calculateArea(shape: Shape) {
	if (shape.kind === "rectangle") {
		return shape.width * shape.height;
		// ^? (parameter) shape: Rectangle
	} else {
		return shape.width * shape.width;
		// ^? (parameter) shape: Square
	}
}
```
`kind`라는 속성을 **tag**로 하는 방식임.

더 타이트하게 잡는다면 아예 **class로 선언**하면 타입과 값 모두 사용할 수 있기에 오류가 없음.

## 타입 연산은 런타임에 영향을 주지 않음
아래의 코드는 타입 체커는 통과했지만 잘못된 방법 -> 코드에 아무런 정제 과정이 없음
`as number` <- **타입 단언문**으로 **타입 연산**이며 런타임에 아무런 영향을 미치지 못함
```ts
// 타입스크립트 코드
function asNumber(val: number | string): number {
	return val as number;
}


// 자바스크립트로 코드 변환하면
function asNumber(val) {
	return val;
}

// 이렇게 명시하면 런타임에서 수행할 수 있음
function asNumber(val: number | string): number {
	return typeof(val) === 'string' ? Number(val) : val;
}
```

코드에 정제하기 위해서는 런타임의 타입을 체크해야하고 '**자바스크립트** **연산**'을 통해 변환해야한다.

## 런타임 타입은 선언된 타입과 다를 수 있음
만약 `boolean` 타입을 인자로 받는 함수에서 API로 받아온 데이터를 넣는다고 가정해보자.
API를 잘못파악해서 응답이 `string` 타입이라면 어딘가에선 실행이 되겠지만 <u>혼란스러운 상황</u>이 일어날 수 있다.

## 타입스크립트 타입으로는 함수를 오버로드할 수 없음
**함수 오버로딩** : 매개변수만 다른 여러 버전의 함수를 만드는 것
-> 타입스크립트에서는 오버로딩을 지원하지만 타입 수준에서만 동작하고 런타임의 동작과는 무관함. (근본적인 오버로딩이 불가능)
하나의 함수에 여러 개의 선언문을 작성할 수 있지만, 구현체(implementation)은 오직 하나!

## 타입스크립트 타입은 런타임 성능에 영향을 주지 않음
타입과 타입 연산자는 자바스크립트 변환 시점에서 제거되기 때문.
-> 성능에 영향을 미치지 않는다면 '성능'상에서도 영향을 주지 않음.

따라서 **런타임 오버헤드를 감수하며 타입 체크를 하지 않음**.
- 런타임 오버헤드가 없는 대신, 타입 스크립트 컴파일러는 '**빌드타임**' 오버헤드 사용
  타입스크립트 팀은 컴파일러 성능을 중요하게 생각하는 편. 특히 incremental Build (증분 컴파일)시 더욱 체감됨. 오버헤드가 커지면 빌드 툴에서 'transpile only' 설정으로 타입 체크를 건너뛸 수도 있음.
	- **증분 컴파일** : 반복된 빌드 과정에서 변경된 소스코드의 의존성만 다시 빌드하는 기능
- 타입스크립트 컴파일시 구형 런타임 환경을 지원하며 호환성을 높이며 성능 오버헤드를 감안할지, 호환성을 내려놓고 성능 중심의 네이티브 구현체를선택할 지 고민할 수 있음. 이는 컴파일 타깃과 언어 레벨의 문제일 뿐, 타입과는 무관함.
