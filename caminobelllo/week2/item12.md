## [아이템 12] 함수 표현식에 타입 적용하기

자바스크립트에는 함수를 만드는 두 가지 방법이 존재한다.

함수 문장, 즉 함수 선언문 (statement)과 함수 표현식(expression)이다.

```
function rollDice1() {}     // 문장(선언문)
const rollDice2 = function() {}     // 표현식
const rollDice3 = () => {}  // 표현식
```

**타입스크립트에서는 함수 표현식을 사용하는 것이 좋다.**

왜냐하면 함수의 매개변수부터 반환값까지 전체를 함수 타입으로 선언해 함수 표현식에 재사용할 수 있기 때문이다.

(함수 선언문 사용 시, 함수의 매개변수와 반환값의 타입을 따로 선언해야 한다.)

<br />

결론적으로는, 매개변수나 반환 값에 타입을 명시하기보다는 함수 표현식 전체에 타입 구문을 적용하는 것이 좋다.

만약에 같은 타입 시그니처가 반복적으로 작성된다면, 함수 타입을 분리하거나 이미 존재하는 타입을 찾아보자.