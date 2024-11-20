## item9
- 타입 단언(as Type)보다 타입 선언(: Type)를 사용해야 한다.
- 타입스크립트보다 타입 정보를 더 잘 아는 상황이라면 타입 단언문이나 null 아님 단언문을 사용합니다.

## item10
- 타입스크립트에서 객체 레퍼 타입은 지양하고, 대신 기본형 타입을 사용하자.

## item11
- 객체 리터럴을 변수에 할당하거나 함수에 매개변수로 전달 시 잉여 속성 체크가 수행된다.
- 잉여 속성 체크와 타입 체커의 일반적인 구조적 할당 가능성 체크는 다르다.
- 임시 변수를 도입하면 잉여 속성 체크가 건너뛰어질 수 있다.

## item12

### 함수 문장 vs 함수 표현식
- 함수 문장(Function Statement) : function 키워드로 함수 이름을 사용해 정의하는 형태
- 함수 표현식(Function Expression) : function 키워드가 값처럼 사용되어 변수에 할당되거나 다른 표현식의 일부로 사용됨

- 호이스팅(Hoisting): 변수와 함수의 선언이 코드의 상단으로 끌어올려지는 것처럼 동작하는 현상, 실제 코드가 재배치되는 것은 아니나 JavaScript 엔진이 코드 실행 전에 변수와 함수 선어부를 메모리 상단으로 이동시켜줌. 이를 통해 코드에서 선언 전에 변수나 함수를 참조할 수 있는 것처럼 보임.

- 자바스크립트 엔진은 코드를 실행하기 전에 전체 스크립트를 두 번 스캔하며 첫 번쨰 스캔에서는 변수와 함수 선언을 메모리에 등록하여 호이스팅하고 두 번쨰 스캔에서 코드를 실행한다.

- 함수 문장과 함수 표현식의 차이: 함수 문장은 코드 실행 전에 호이스팅되므로 코드의 어느 위치에서든 호출 가능하다.   
그러나 함수 표현식의 경우 let, const의 경우에는 선언은 호이스팅되지만 초기화 전에는 일시적 사각지대(TDZ)에 있어 선언 전에 참조할 경우 ReferenceError가 발생해 초기화 전에 변수에 접근하면 오류가 발생한다.   
var의 경우 선언만 호이스팅되고 초기화는 실행 시점에 이루어져 undefined로 출력된다.   
즉, **함수 표현식의 경우에는 함수가 할당된 이후에만 호출할 수 있다.**

### 타입스크립트에서의 권장 사용법
- 타입스크립트에서는 함수 표현식을 사용하는 것이 좋다.
- 함수 표현식을 사용하면 함수의 매개변수부터 반환값까지 전체를 함수 타입으로 선언하여 함수 표현식에 재사용할수 있기 때문이다.

'''
type BinaryFn = (a:number, b:number) => number;
const add: BinaryFn = (a, b) => a + b;
const sub: BinaryFn = (a, b) => a - b;
const mul: BinaryFn = (a, b) => a * b;
const div: BinaryFn = (a, b) => a / b;
'''

- 함수 표현식 전체에 타입 구문을 적용하는 방식을 사용하면 함수 타입 선언을 이용한 것보다 타입 구문을 적게 쓰고, 함수 구현부를 분리해 로직을 분명히 하고 함수 표현식의 반환 타입까지 선언할 수 있는 장점이 있다.

- 같은 타입 시그니처를 반복적으로 작성한 코드가 있다면 함수 타입을 분리해내거나 이미 존재하는 타입을 찾아서 적용하는 것이 좋다.

- 다른 함수의 시그니처를 참조하려면 typeof fn을 사용하면 된다.

### 함수 문장과 표현식에서 타입 체커 작동방식 차이gi

- 함수 표현식을 적용하고 함수 전체에 타입을 적용해보자.
타입 구문은 반환 타입을 보장하고 그 타입은 참조한 타입과 동일하다.
만약 잘못한 경우 타입스크립트는 그 실수를 구현체에서 잡아낸다.

그러나 함수 문장으로 사용한 경우에는 함수를 호출한 위치에서 오류가 발생한다.

- 함수 표현식에서는 함수 구현부에서 타입이 변수에 할당된 타입과 일치하지 않으면 즉시 오류가 발생한다.
함수 문장에서는 함수 본체에서 자동으로 타입을 추론하므로, 함수 본체에서 오류가 표시되지 않더라도, 호출 시점에서 반환타입과 기대타입이 불일치하면 오류가 나타날 수 있다.

## item13
- 복잡한 타입이라면 타입 별칭을, 타입과 인터페이스 두 가지 방법으로 표현가능한 간단한 타입의 경우 일관성에 따라 선택해준다.

## item14
- DRY(Don't Repeat Yourself) 원칙을 타입에도 최대한 적용하자.
- 타입에 이름을 붙여 반복을 피하자.
- 타입들 간의 매핑에 타입스크립트가 제공하는 keyof, typeof, 인덱싱, 제네릭 타입 등의 도구들을 사용하자.

## item15
- 런타임 때까지 객체의 속성을 알 수 없다면 인덱스 시그니처를 사용한다.
- 안전한 접근을 위해 인덱스 시그니처 값 타입에 undefined를 추가할 수 있다.
- 가능하다면 인터페이스, Record, 매핑된 타입과 같은 인덱스 시그니처보다 정확한 타입을 사용하는 것이 좋다.

## item16
- 배열은 객체이므로 키는 숫자가 아니라 문자열이다.
- 인덱스 시그니처로 사용된 number타입은 버그를 잡기 위한 순수 타입스크립트 코드이다.
- 인덱스 시그니처에 number보다는 Array, 튜플, ArrayLike 타입을 사용하는 것이 좋다.

## item17
- 함수가 매개변수를 수정하지 않는다면 readonly로 선언하는 것이 좋다.
- readonly 매개변수는 인터페이스를 명확하게 하고, 매개변수 변경을 방지한다.
- readonly는 얕게 동작한다.
- readonly와 const의 차이를 이해해야 한다.

## item18
- 매핑된 타입을 사용해 관련된 값과 타입을 동기화한다.
- 인터페이스에 새로운 속성을 추가할 때, 선택을 강제하도록 매핑된 타입을 고려한다.