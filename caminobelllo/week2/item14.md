## [아이템 14] 타입 연산과 제너릭 사용으로 반복 줄이기

### DRY 원칙

코드에서만이 아니라 타입에서도 불필요한 중복을 없애고 재활용하는 것이 중요하다.

### 타입 재활용

1. 타입 선언을 통해 중복을 제거할 수 있다.
   예를 들어 특정 함수의 매개변수 타입이 중복되어 나타나면 이를 type으로 선언하여 중복된 타입을 제거한다,

2. 타입 연산자를 활용해 타입을 확장할 수 있다.
   에를 들어 다음처럼 두 타입이 중복된 타입을 공유하는 경우에,

```
type Point2D = {
    x: number;
    y: number;
}

type Point3D = {
    x: number;
    y: number;
    z: number;
}
```

타입에 인터섹션을 이용하거나, 인터페이스의 extends 를 통해 중복을 제거할 수 있다.

```
type Point3D = Point2D & {
    z: number;
}

interface IPoint3D extends Point2D {
    z: number;
}
```

위와 같은 예시에서는 불필요한 타입의 중복을 제거하기 위해 타입을 확장했다.

하지만, 경우에 따라서는 전체 어플리케이션의 부분만 표현해야 하는 경우가 있다. 책의 내용을 읽을 때 처음엔 와닿지 않는다고 느꼈는데, 그냥 간단하게 타입을 축소하는 경우라고 생각하자.

예를 들어 다음과 같이 State라는 타입과 TopNavState이라는 두 타입이 있다고 가정하자.

```
interface State {
    userId : string;
    pageTitle: string;
    recentFiles: string[];
    pageContents: string;
}

interface TopNavState {
    userId : string;
    pageTitle: string;
    recentFiles: string[];
}
```

TopNavState을 State로 확장하여 사용할 수도 있지만, 의미적으로 State라는 일반적인 인터페이스의 부분집합으로 TopNavState을 정의하는 것이 더 낫다.

이런 경우에는 다음과 같이 State를 인덱싱하여 속성의 타입에서 중복을 제거할 수 있다.

```
type TopNavState = {
    userID: State['userId'];
    pageTitle: State['pageTitle'];
    recentFiles: State['recentFiles'];
}
```

근데 이 코드에서도 중복되는 코드가 많이 보인다. 이를 Mapped Types를 통해 더 간단하게 만들어보면

```
type TopNavState = {
    [k in 'userId' | 'pageTitle' | 'recentFiles']: State[k]
}
```

로 더욱 추상화된 상태로 표현할 수 있다.
