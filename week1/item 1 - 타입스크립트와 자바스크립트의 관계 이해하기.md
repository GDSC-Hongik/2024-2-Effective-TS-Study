
## 타입스크립트는 자바스크립트의 상위집합(superset)

문법의 유효성과 동작의 이슈는 독립적인 문제
문법이 틀리다면 당연히 이슈가 발생하지만, JS 문법에서 아무런 문제가 생기지 않아도 타입 체커에서 문제가 생길 수 있다.

JS, TS는 확장자가 다르지만 달라지는 것은 없다. 이 점이 마이그레이션에 엄청난 이점을 준다.

**JS** 파일에 아래에 있는 `: string`과 같은 타입구문이 들어가는 순간부터 **TS**가 시작된다.
아래 코드는 JS로 동작하지 않지만 TS로 동작한다, JS로 되는 코드는 TS에서는 동작한다. TS는 JS의 상위 집합이다.
```ts
function greet(who: string) {
	console.log('Hello', who);
}
```


## 타입 시스템의 목표는 런타임에 오류를 발생시킬 코드를 미리 찾는 것
**'정적' 타입 시스템에 대해서**
`let city = 'new york city'`라는 구문에는 타입 선언을 하지 않지만 문자열로 **타입을 추론**한다.
오류가 발생하지 않더라도 변수명, 타입에 대한 혼동은 **의도와 다른 코드 동작**을 야기한다.
(그렇지만 타입체커가 모든 오류를 잡아주진 않는다.)

```ts
const states = [
	{ name: "Alabama", capital: "Montgomery" },
	{ name: "Alaska", capital: "Juneau" },
	{ name: "Arizona", capital: "Phoenix" },
	// ...
];

for (const state of states) {
	console.log(state.capitol);
}
```

`state.capital`이 아닌 `capitol`로 오타가 난 경우에도 타입 추론으로 오류를 잡아낸다.

따라서 타입 체커를 통과한 타입 스크립트 프로그램이 '타입스크립트 프로그램'이다.

**TS > JS  & 타입 체커**

이와 같은 의미로 문자열과 숫자의 덧셈같은 연산이 JS에선 가능하지만 TS에선 안된다.
의도와 다른 코드가 될 수 있기 때문에 원천 차단하는 모습이다.

