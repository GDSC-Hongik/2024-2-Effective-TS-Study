# week4

## item 28 - 유효한 상태만 표현하는 타입을 지향하기

유효한 상태라고 얘기하니까 조금 어려워보이지만 **유니온**이나 **태그** 등을 활용해서 타입을 선언하라고 권장하는 형태

### 네트워크 요청과 화면 랜더링 과정에서 state를 확인한다는 가정

**before**
```ts
interface State {
	pageText: string;
	isLoading: boolean;
	error?: string;
}
```

State 타입을 만들어놓고 그때그때 조건문을 통해 분기하여 Loading 여부, error 여부 등을 확인하다보면 휴먼에러를 마주하게 된다. 이런 상태를 해결하기 위해 태그를 활용하거나 타입간의 유니온을 활용하자고 주요 내용

**After**
```ts
interface RequestPending { tate:'pending'; }
interface RequestError { state:'error'; error: string; }
interface RequestSuccess { state: 'ok'; pageText: string; }

type RequestState = RequestPending | RequestError | RequestSuccess;

interface State {
	currentPage: string;
	requests: { [page: string]: RequestState };
}
```
경우의 수를 사람이 컨트롤하지 않고 타입을 정의하는 '명시적'인 모델링을 해놓으면 나중에 굉장히 편리하다.
경우의 수는 휴먼 에러를 낳고 모호함을 낳는다. 어떤 이유에서건 통제가 필요하기에 타입스크립트를 쓰기에 꼭 잘 활용하자.



### 태그를 사용하는 경우의 코드
`status`라는 태그로 관리하는 방법
```ts
type State =
  | { status: "idle" }
  | { status: "loading" }
  | { status: "success"; data: string }
  | { status: "error"; error: string };

function transition(state: State): State {
  switch (state.status) {
    case "idle":
      return { status: "loading" };
    case "loading":
      return { status: "success", data: "Loaded data" };
    case "success":
      return { status: "idle" };
    case "error":
      return { status: "idle" };
  }
}
```

### 사용하는 이유 정리
타입을 정의하는 고통이나 코드의 길이, 설계하는 고민은 커지기 마련이다.

그러나 타이트한 설계는 어떻게보면 깔끔한 코드를 만들 수 있다. 유지보수하기 좋아진다.
(그밖에 팀이 결속되고 지속가능한 프로덕트가 되고 탄소가 절약되고...)

빠르게 시작한다는 명목으로 너무 생각 없이 코드 작업을 시작하면 미래에 기술 부채를 떠안게 된다.
초반에 조금 느리게 가더라도 나중에 확실하고 빠르게 치고 나가도록 하는게 잘 짜여진 코드 설계다.

---

## item 29 - 사용할 때는 너그럽게, 생성할 때는 엄격하게
외유내강 느낌의 타이틀.

**input**은 너그럽게 **output**은 타이트하게라는 의미로 쓸 수 있다.
개떡같이 알려줘도 찰떡같이 알려준다고도 볼 수 있다. 

### formatDate라는 함수를 만든다면
이런 방법으로 사용해볼 수 있다. 입력값이 무엇이 됐든 `string`으로 반환한다.
```ts
function formatDate(date: string | number | Date): string {
  if (typeof date === "string") {
    return new Date(date).toISOString();
  } else if (typeof date === "number") {
    return new Date(date).toISOString();
  } else {
    return date.toISOString();
  }
}
```

**결과적으로 얻는 이점**
- 입력이 편하니까 위 함수를 끌어다 쓰기 편해졌다. (유연해졌다.)
- 반환할 타입이 명확하기에 신뢰할 수 있다. (협업하기에도 좋아졌다.)

### 반환 타입과 매개변수 타입의 차별화
반환용 타입과 매개변수용 타입을 따로 사용하는 방법
반환용은 타이트한 기본 형태, 매개변수용은 느슨하고 유연한 형태를 띈다.

```ts
type UserInput = { id: string; name: string } | { id: string; email: string };

function processUser(input: UserInput): { id: string; name: string; email: string } {
  const id = input.id;
  const name = "name" in input ? input.name : "Unknown";
  const email = "email" in input ? input.email : "Unknown@example.com";

  return { id, name, email };
}

processUser({ id: "123", name: "Alice" }); // 가능가능!
processUser({ id: "456", email: "bob@example.com" }); // 가능!!!
```

### API 요청과 응답에서 확인하는 원칙
- API 요청(Request) : 데이터 타입은 유연하게 설계.
- API 응답(Response) : 데이터 타입은 엄격하게 정의.

```ts
type RequestPayload = Record<string, any>; // 입력은 유연
interface ResponsePayload { // 출력은 명확
  success: boolean;
  data: { id: number; message: string };
}
```

### 주의사항
타이트한 입력은 유연함이 떨어지는 것은 맞지만 너무 유연하게 하면 타입스크립트를 쓰는 이유가 없기에 이런 부분은 사람이 직접 판단해야한다.
