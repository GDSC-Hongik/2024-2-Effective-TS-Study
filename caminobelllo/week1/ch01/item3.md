# [아이템 3] 코드 생성과 타입이 관계없음을 이해하기

타입스크립트 컴파일러는,

1. 최신 타입스크립트 (또는 JS)를 브라우저에서 동작하도록 구버전의 자바스크립트로 트랜스파일한다.
2. 코드의 타입 오류를 체크한다.

이 두가지는 서로 완벽히 독립적이기 때문에, 타입 오류가 있는 코드도 컴파일이 가능하다.
(+ 타입스크립트가 자바스크립트로 트랜스파일이 되면, 타입스크립트에서 정의된 interface, type 등의 타입 관련 구문들은 모두 제거된다.)

따라서 런타임에는 타입 체크가 불가능하다. 런타임 타입은 선언된 타입과 다를 수 있다.
그렇다면 런타임에 타입 체크를 한다는 것은 어떤 의미인가 하면, 코드를 작성할 때 특정 객체를 input으로 받아 처리하는 함수가 있다고 가정했을 때, 그 특정 객체의 타입이 정해지지 않았거나 변경될 수 있다는 의미이다.
또한 외부 서비스로부터 데이터를 받아오는 경우에는 fetching된 데이터의 타입이 변경될 수 있다. 이런 경우가 발생하는 것을 대비하는 방안으로 런타임에 타입을 비교하는 방안이 있는 것이다.

런타임에 타입을 비교하는 하나의 경우로는, 클래스를 이용하는 것이다. 그 이유는 무엇일까?
<br />

_**클래스는 자바스크립트의 구현되어 있는 실제 값이기도 하면서, 해당 클래스의 인스턴스는 타입스크립트 상의 타입으로도 존재할 수 있다.**_
<br />

이러한 특징이 있기 때문에 클래스는 트랜스파일 시에 기존 타입 관련 구문과 달리 사라지지 않아서 런타임에 비교가 가능하다.

아래 코드는 클래스를 타입으로 사용하는 예이다.

```
class Square {
    constructor(public width: number) {}
}

class Reactangle extends Square {
    construecor(public width: number, public height: number) {
        super(width)
    }
}

type Shape = Square | Rectangle

funcction calculateArea(shape: Shape) {
    if (shape instanseof Rectangle) {
        shape
        return shape.width * shape.height
    } else {
        shape
        return shape.width * shape.width
    }
}
```
