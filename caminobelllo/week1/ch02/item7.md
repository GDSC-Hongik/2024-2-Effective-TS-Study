# [아이템 7] 타입이 값들의 집합이라고 생각하기

자바스크립트(ES6)에서 타입이란 데이터 타입을 이야기 하며, 총 7개이다. (원시 타입인 number, string, boolean, undefiend, null, symbol와 객체 타입으로 구성)

타입스크립트에서 타입이란 **할당 가능한 값들의 집합**이다.
그리고 그 타입은 프로그래머가 정의할 수 있으며 그 수는 무한히 많고, 타입 공간에 존재한다.

### never 타입

가장 작은 집합은 `never` 타입으로 선언된다. 이는 공집합이다. (never 타입으로 선언이 되면 아무런 값도 할당할 수 없다.)

### unknown 타입

가장 큰 집합은 `unknown`이며 어떠한 값이든 할당할 수 있는 집합이다. 전체집합이라 생각하면 된다.
any와 유사하다고 느껴질 수 있는데 차이점이 존재한다. 예를 들어 아래의 코드에서

```
function doSomethingWithNumber(value: number) {}

doSomethingWithNumber(unknownValue)     // error!
doSomethingWithNumber(unknownValue as any)      // ok
```

함수 doSomethingWithNumber의 파라미터 value는 number 타입이다. number 타입은 전체 집합인 unknown 타입의 부분집합이라고 할 수 있다. unknownValue에는 number 타입뿐만 아니라 string 타입의 값도 할당될 수 있기 때문에 타입 체커가 에러를 나타낸다.
하지만 any는 타입 체크 자체를 무시하기 때문에 에러를 발생시키지 않는다.

> [참고](https://junghyunkim.tistory.com/entry/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B87-%ED%83%80%EC%9E%85%EC%9D%B4-%EA%B0%92%EB%93%A4%EC%9D%98-%EC%A7%91%ED%95%A9%EC%9D%B4%EB%9D%BC%EA%B3%A0-%EC%83%9D%EA%B0%81)

### unit 타입 (literal 타입)

원소 하나로만 이루어진 집합

```
type A = 'A'
type B = 'B'
type Twelve = 12
```

### union 타입 (합집합, |)

### intersection 타입 (교집합, &)

interface나 type에 keyof + | or &를 적용하면 생각한 것과 조금 다르게 결과가 나올 수 있다.

```
interface Person {
  name: string;
}
interface Life {
  birth: Date;
  death?: Date;
}

// never
type KeyUnion = keyof (Person | Life);

// "name" | "birth" | "death"
type KeyIntersection = keyof (Person & Life);

// "name" | "birth" | "death"
type SameKeyUnion = keyof Person | keyof Life;

// never
type SameKeyIntersection = keyof Person & keyof Life;
```

> [참고](https://1-blue.github.io/posts/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-2%EC%9E%A5/)

<br />

위의 코드처럼 인터섹션이 object 타입에 적용될 때를 유의해야한다. 다음 예시와 같이 Creature 타입과 Person 타입이 정의되어 있을 때 이들의 인터섹션은 프로퍼티의 교집합으로 생각하여 Creature 타입으로 생각하기 쉽다.

```
interface Creature {
    name: string;
    birth: number;
    gender: "M" | "F";
}

interface Person {
    name: string;
    birth: number;
    gender: "M" | "F";
    nationality: string;
}
```

하지만 이들의 인터섹션은 **Person 타입** 이다.
오브젝트는 프로퍼티를 제약 조건으로 생각하면 된다. 값의 공간에 무수히 많은 오브젝트들이 갖고 있다고 가정해보았을 때, 그 오브젝트는 다양한 모습을 지닌다. 이처럼 **무수히 많은 오브젝트들 중에서 Person 타입의 오브젝트가 될 수 있는 오브젝트는 동시에 Creature 라는 오브젝트**가 된다.
<br />
반면에 Creature 타입의 오브젝트들은 항상 Person의 오브젝트가 된다고 할 수 없다. 그 이유는 nationality라는 제약 조건이 존재하기 때문이다.
따라서, Creature는 상위 집합이고 Person은 부분집합이 된다.

이러한 관계에 따라 **Creatrue와 Person의 인터섹션(교집합)은 Person**이 된다.

[참고](https://junghyunkim.tistory.com/entry/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B87-%ED%83%80%EC%9E%85%EC%9D%B4-%EA%B0%92%EB%93%A4%EC%9D%98-%EC%A7%91%ED%95%A9%EC%9D%B4%EB%9D%BC%EA%B3%A0-%EC%83%9D%EA%B0%81)
