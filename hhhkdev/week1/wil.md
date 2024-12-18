# 이펙티브 타입스크립트

플랫폼: 독서
기간: 2024년 11월 6일

1장: 타입스크립트 알아보기

타입스크립트와 자바스크립트의 관계를 이해해야 함 → 둘의 관계는 필연적.

### 아이템1. 타입스크립트와 자바스크립트의 관계 이해하기

> 타입스크립트는 (타입이 정의된) 자바스크립트의 상위 집합(superset)이다.

자바스크립트의 문법 오류가 없다면, 유효한 타입스크립트 코드. (문법의 유효성과 동작은 별개의 문제)

→ 하지만 모든 타입스크립트가 자바스크립트를 만족시킬 수는 없음. → **타입스크립트가 상위 집합.**

- 타입 시스템의 목표: 런타임에 오류를 발생시킬 코드를 미리 찾아냄.
  - 타입스크립트가 **“정적” 타입 시스템**인 이유
  - [ ] 이게 의미하는 게 뭐지? 정적 타입 시스템의 의미에 대해서 더 알아보기

타입 구문은 코드의 의도를 파악하게 해주고, 훨씬 더 많은 오류를 찾아낼 수 있음. → 의도를 명확히 해주기 위한 언어가 타입스크립트.

- 프로그램이 오류를 발생시키는 것과 잘못된 코드는 다른 문제.
  - 타입 체커를 통과하더라도 런타임에 오류가 발생할 수 있음. → 타입스크립트는 자바스크립트의 런타임 동작을 모델링함.
  - [ ] 자바스크립트의 런타임 동작을 모델링한다는 것은 어떤 의미인가?

### 아이템2. 타입스크립트 설정 이해하기

타입스크립트의 설정은 커맨드라인을 통해서도 가능함. → `tsconfig.json`에서도 가능함.

```bash
# CLI
**tsc --noImplicitAny program.ts**

# tsconfig.json
{
	"compilerOptions": {
		"noImplicitAny": true
	}
}

# 설정 파일을 생성하는 방법
**tsc --init**
```

- `noImplicitAny`: 모든 변수에 무조건 타입 선언을 해줘야 함. 즉 임의로 Any로 추론하지 않음.
- `strictNullChecks`: Null이나 undefined 같은 값이 타입 선언이 된 변수에 들어가도 되는가.
  - 애초에 타입 선언할 때 Null을 포함하여 의도를 드러낸다면 상관없음.

### 아이템3. 코드 생성과 타입이 관계없음을 이해하기

<aside>

타입스크립트 컴파일러의 두 가지 역할

---

- 최신 타입스크립트/자바스크립트를 브라우저에서 동작할 수 있도록 구버전의 자바스크립트로 트랜스파일함.
- 코드의 타임 오류를 체크함.
</aside>

위 두가지는 완전히 독립적임. 따라서 가능해지는 것들이 몇가지 있음.

- 타입 오류가 있는 코드도 컴파일이 가능함. 코드를 생성하는 과정은 컴파일 과정이고, 이를 실행시키는 것은 다른 문제.
- 런타임에는 타입 체크가 불가능함.
  - 사실 당연한 얘기. 타입스크립트는 실제 구동 시 자바스크립트로 변경되므로 실행 과정에서는 영향을 줄 수 없음.
  - 따라서 런타임에도 타입 정보를 이용하기 위해서는 다른 방법을 이용해야 함. 태그된 유니온과 타입 체크하는 방식
  - 타입을 클래스로 만들어서 런타임에 타입과 값을 모두 접근할 수 있음.
- 타입 연산은 런타임에 영향을 주지 않음. 런타임에서는 모든 타입이 사라진 형태.
- 런타임 타입은 선언된 타입과 다를 수 있음.
  - [ ] 솔직히 무슨 얘긴지 잘 이해가 안됨. 우선 선언된 타입이 언제든 달라질 수 있음.
- 타입스크립트의 타입은 런타임 성능에 영향을 주지 않음.

### 아이템4. 구조적 타이핑에 익숙해지기

(덕타이핑: 객체가 어떤 타입에 부합하는 변수와 메서드를 가지면 객체를 해당 타입으로 간주함.)

**구조적 타이핑**: 인자로 들어오는 객체의 타입이 완전 일치하지 않더라도 포함하고 있다면 인정함.

- C++, 자바와는 매우 다른 특징
  - 타입스크립트는 타입의 매개변수를 선언하여 같거나 서브클래스임을 보장하지 않음.
  - [ ] 대략적으로 객체 내의 조건들이 같기에 인정해주지만, 이게 다른 언어와의 어떤 차이인지 이해가 안됨.

유닛 테스트할 때 이용할 수 있음.

- [ ] 테스트에 대한 내용이 아직 완벽하게 공부되지 않아서 이해하기 힘듦. 테스트에 대한 공부는 빠른 시일 내에 선행되어야 함.

### 아이템5. any 타입 지양하기

any를 사용하는 건 타입스크립트의 장점을 많이 포기함. any의 위험성을 알아보자.

- any 타입에는 **타입 안전성**이 없음. (특히 as any를 사용할 경우)
- any 타입은 함수 시그니처를 무시해버림.
  - 함수에 인자로 들어가는 값을 any로 선언해버릴 경우, 함수 인자/반환값에 적힌 타입을 무시함.
- any 타입에는 언어 서비스가 적용되지 않음. (자동완성, 이름변경 등)

> 타입스크립트의 모토: **확장 가능한 자바스크립트**

- 코드 리팩토링 때 버그, 타입 설계를 감춤. → 타입의 의도를 파악할 수 없으며 타인과의 개발 경험도 나쁘게 할수 있음.

# 2장: 타입스크립트의 타입 시스템

### 아이템6. 편집기를 사용하여 타입 시스템 탐색하기

타입스크립트를 설치하면, 단독으로 실행할 수 있는 타입스크립트 서버를 열 수 있음.(`tsserver`)

- [ ] 조건문의 분기에서값의 타입이 어떻게 변하는지 알아보는 것은 좋은 방법.

### 아이템7. 타입이 값들의 집합이라고 생각하기

> **타입**: 할당 가능한 값들의 집합. (타입의 범위)

- 공집합, `never`: 네버 타입으로 선언된 변수의 범위가 공집합. → 아무것도 할당할 수 없음.
- 리터럴 타입(유닛 타입): 한 가지 값만을 포함함. (boolean은 두 가지 값)
- 유니온 타입: 타입들을 2개 이상 묶어서 사용할 수 있음.

> 타입 체커의 역할: 하나의 집합이 다른 집합의 **부분 집합**인지 검사함.

다이어그램에서 크기가 더 작을 경우 → 더 작은 타입이 **서브 타입**
→ 서브타입임을 선언하기 위해서는 `extends`를 이용하여 상속처럼 쓸 수 있음.
`extends`는 제네릭 타입에서 한정자로 사용되기도 함.

타입은 값의 집합. 동일한 값의 집합을 가진다는 건 두 타입이 같다는 의미.

### 아이템8. 타입 공간과 값 공간의 심벌 구분하기

- [x] 심벌이 자바스크립트 신규 문법 중 하나인 Symbol을 의미하는가?

심벌은 변수명 정도로 이해해볼 수 있을 듯.

심벌이 같은, 즉 같은 이름을 가진 타입과 변수/함수가 존재하더라도 오류가 발생하지 않음 → 타입과 값으로 혼용하여 사용할 수 있음.

- 타입 선언 콜론, `as` 뒤에는 무조건 타입이 오지만, `=` 뒤에는 값이 나옴.

특히 클래스의 경우에서 혼용되는 듯 → 클래스가 타입으로 쓰이면 형태(속성, 메서드)가 사용되지만 값으로 쓰일 때는 생성자가 이용됨.

<aside>

타입의 관점에서, `typeof`는 값을 읽어서 타입스크립트 타입으로 반환함.

---

값의 관점에서, `typeof`는 자바스크립트 런타임의 `typeof` 연산자가 됨.

</aside>

- [ ] typeof 연산자가 언어에 따라서 하는 역할이 달라지는가?

타입스크립트와 자바스크립트에서 혼용되지만 약간은 다른 역할을 하는 것들이 많이 존재함. → 타입 공간과 값 공간을 헷갈리지 않아야 함.
