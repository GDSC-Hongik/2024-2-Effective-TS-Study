## [아이템 17] 변경 관련된 오류 방지를 위해 readonly 사용하기

readonly는 타입스크립트에서 두 가지의 쓰임새가 있다.

### 1. 객체 타입 Property 앞에 붙는 readonly

재할당의 가능과 객체 속성 변경 가능은 서로 독립적이다. (예. const는 재할당이 불가능하지만 선언된 객체의 속성은 바꿀 수 있다.)

타입스크립트에서는 readonly 키워드를 통해 특정 속성의 변경을 막을 수 있다.

이 때 유의해야 할 점은 readonly 접근 제어자가 shallow 하게 동작하는 것이다.

유틸리티 타입의 Readonly은 기존 타입의 속성들 모두를 readonly로 만들어 준다.

```
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};
```

### 2. Array, 튜플 타입 앞에 붙는 readonly

number[]와 같은 Array 타입 혹은 튜플 타입 앞에도 readonly 키워드를 사용할 수 있다.
이는 배열 혹은 튜플을 수정할 수 없음을 의미한다.
