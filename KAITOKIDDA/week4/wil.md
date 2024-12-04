## item36 해당 분야의 용어로 타입 이름 짓기

- 타입의 설계에서 타입의 이름 짓기 역시 중요하다.  
  타입, 속성, 변수의 이름을 명확히 하여 코드와 타입의 추상화 수준을 높이자.

- 가독성과 추상화를 위해 해당 분야의 용어를 정확하게 사용하자.

- 동일한 의미라면 같은 용어를 사용해야 한다.

- data, info, thing, item, object, entity 등 모호하고 의미 없는 이름은 피하자.

- 계산 방식, 일부 포함된 내용이 아닌 그 데이터 자체가 무엇인지 잘 알 수 있는 이름을 사용하자.

## item37 공식 명칭에는 상표를 붙이기

### 상표

- 타입에 추가적인 식별자를 붙여서 해당 타입이 특정한 제약을 만족한다는 것을 나타내는 방식, 객체 리터럴 타입으로 구현

- 예시

```typescript
type AbsolutePath = string & { _brand: "abs" };
```

- string 타입에 &\_brand: 'abs'라는 속성을 추가해 AbsolutePath라는 새로운 타입을 정의
- **\_brand 속성**은 **실제 런타임**에는 **존재하지 않으며**, **타입 시스템**에서만 사용됨

- 런타임에는 상표가 없는 이유:  
  상표는 타입스크립트의 타입 시스템에서만 존재하고, 컴파일 후에는 제거됨. 따라서 런타임 객체에는 \_brand 속성이 추가되지 않으며, 런타임 성능에 영향을 미치지 않음.

- 타입 시스템이기 때문에 런타임 오버헤드(붙인 상표의 타입을 검사하기 위한 추가 작업)를 없앨 수 있고, string이나 number와 같이 추가 속성을 붙일 수 없는 내장 타입의 상표화가 가능해짐.

### 사용 예시1 연산 차원 제한

- 아래 코드는 구조적 타이핑 관점에서는 문제가 없지만 수학적으로는 2차원 벡터 연산만 가능하게 해주어야 한다.
- 구조적 타이핑에 의해 같은 구조를 가진 타입은 동일하게 취급되지만 상표 기법을 통해 타입 간 구분을 한다.

```typescript
interface Vector2D {
  x: number;
  y: number;
}
function calculateNorm(p: Vector2D) {
  return Math.sqrt(p.x * p.x + p.y * p.y);
}
calculateNorm({ x: 3, y: 4 });
calculateNorm({ x: 3, y: 4, z: 1 });
```

- 상표를 붙이면 이 문제를 해결할 있다.

```typescript
interface Vector2D {
  _brand: "2d";
  x: number;
  y: number;
}
function vec2D(x: number, y: number): Vector2D {
  return { x, y, _brand: "2d" };
}
function calculateNorm(p: Vector2D) {
  return Math.sqrt(p.x * p.x + p.y * p.y);
}

calculateNorm(vex2D(3, 4));
const vec3D = { x: 3, y: 4, z: 1 };
calculateNorm(vec3D);
//'_brand' 속성이 ... 형식에 없습니다.
```

- 물론 vec3D값에 \_brand: '2d'라고 일부러 추가해 사용하는 것까지 막을 수는 없다.

### 사용 예시2 내장 타입 상표화

- 다음은 절대경로('/')로 시작하는지 판단하는 것은 런타임에서는 쉽지만 타입 시스템에서는 어렵다. 이 때 상표 기법을 활용해 타입 시스템에서 구현한다.

```typescript
type AbsolutePath = string & { _brand: "abs" };
function listAbsolutePath(path: AbsolutePath) {
  //..
}
function isAbsolutePath(path: string): path is AbsolutePath {
  return path.startsWith("/");
}
```

- 타입 가드를 활용해 오류를 방지

```typescript
function f(path: string) {
  if (isAbsolutePath(path)) {
    listAbsolutePath(path);
  }

  listAbsolutePath(path);
  // ~'string' 형식의 인수는 'AbsolutePath' 형식의
  // 매개변수에 할당될 수 없다.
}
```

- if문을 벗어나면 string 타입
- 단언문을 쓰지 않는다면 AbsolutePath 타입을 매개변수로 받거나 isAbsolutePath()를 통해 타입을 좁혀야 함

### 사용 예시3

- 기존 이진탐색 코드
- 이미 정렬된 상태를 가정한 코드라서 목록이 정렬되어 있지 않은 경우 잘못된 결과가 도출됨

```typescript
function binarySearch<T>(xs: T[], x: T): boolean {
  let low = 0,
    height = xs.length - 1;

  while (high >= low) {
    const mid = low + Math.floor((high - low) / 2);
    const v = xs[mid];

    if (v == x) return true;
    [low, high] = x > v ? [mid + 1, high] : [low, mid - 1];
  }

  return false;
}
```

- 정렬되었다는 상표가 붙은 SortedList 타입의 값을 사용하거나 isSorted를 호출해서 상표값 받아야 호출 가능

```typescript
type SortedList<T> = T[] & { _brand: "sorted" };

function isSorted<T>(xs: T[]): xs is SortedList<T> {
  for (let i = 1; i < xs.length; i++) {
    if (xs[i] < xs[i - 1]) {
      return false;
    }
  }

  return true;
}

function binarySearch<T>(xs: SortedList<T>, x: T): boolean {
  // ...
}
```

### 사용 예시4 단위 구분

```typescript
type Meters = number & { _brand: "meters" };
type Seconds = number & { _brand: "seconds" };

const meters = (m: number) => m as Meters;
const seconds = (s: number) => s as Seconds;

const oneKm = meters(1000); // 타입이 Meters
const oneMin = seconds(60); // 타입이 Seconds
```

```typescript
const tenKm = oneKm * 10; // 타입이 number
const v = oneKm / oneMin; // 타입이 number
```

- 산술 연산 이후에는 상표가 사라지는 문제가 있다.

## 요약

- 구조적 타이핑때문에 값을 세밀하게 구별하지 못할 때는 상표 기법을 사용한다.
- 상표 기법은 타입 시스템에서만 존재하지만 런타임에 상표를 검사하는 효과를 지닌다.
