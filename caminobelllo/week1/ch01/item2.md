# [아이템 2] 타입스크립트 설정 이해하기

**tsConfig.json**

- 타입스크립트 컴파일러 옵션 설정 파일

**noImplicitAny**

- true인 경우 암묵적으로 any 타입을 가지는 것을 허락하지 않는다. (any는 타입 체커를 무력화하기 때문)

**strictNullChecks**

- false로 설정했을 경우, 엄격하게 타입 체크를 진행하지 않는다. 예를 들어 number 타입의 변수에 null과 undefined를 할당하여도 오류없이 정상적인 코드임을 알 수 있다.
- 해당 옵션을 true로 설정하여 안전한 코드를 작성할 수 있도록 하자.
