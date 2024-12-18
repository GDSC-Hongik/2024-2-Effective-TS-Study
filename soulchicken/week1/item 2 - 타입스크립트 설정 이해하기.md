
## ts.config
설정 파일을 통해서 관리한다. 설정 파일 생성은 `tsc --init`
타입스크립트에서 설정은 대부분 언어에서는 허용하지 않는 정도의 고수준 제어도 가능하다.
어디서 소스 파일을 찾을지, 어떤 종류의 출력을 생성하고 제어할 지 등이 있다.

설정에 따라서 오류가 나올 수도 있고, 아닐 수 있으니 **컴파일러 설정이 동일**한지 확인해야한다.

### 암묵적 any와 NoImplicitAny 설정
`any` 타입을 코드에 넣지 않았는데 간주된 경우를 칭한다.
코드 설정에서 `NoImplicitAny`가 `No`로 되어 있다면 코드를 명시하게 되기에 권장된다.
JS에서 TS로 **마이그레이션** 시에는 활용하면 유용한 설정이다.
Implicit : 암시적, 암묵적 <=> Explicit : 명시적

### StrictNullChecks
모든 타입에 null, undefined가 허용되는 지 확인하는 설정
만약 해제되어 있다면 `const x: number = null;`이 가능하다.
이렇게 암묵적으로 하는 경우 의도대로 코드가 작동하지 않을 수 있다.
따라서 이 설정을 하되, 의도를 코드에 명시하는 것이 좋다.
`const x: number | null = null;`

이 설정 또한 JS에서 TS로 **마이그레이션** 시 해제하는 것이 유용한 설정이다.

