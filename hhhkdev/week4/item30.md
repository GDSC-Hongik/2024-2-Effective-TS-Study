> 코드와 주석이 맞지 않는다면, 둘 다 잘못된 것이다!

- 주석과 타입스크립트 코드의 차이점
  - 주석은 코드와 동기화되지 않음. (타입 정보와 모순될 수도 있음.)
  - 타입 구문은 타입 체커가 타입 정보를 동기화하도록 강제함. → 코드가 변경돼도 정보가 정확히 동기화됨.

**주석을 개선하는 방법**

1. 특정 매개변수를 설명하고 싶을 때는 JSDoc의 @param 구문을 사용함. <아이템48>
   - JSDoc에서 @param을 어떻게 사용하는지 알아보기
     단순히 주석을 작성하는 것을 넘어, 매개변수/리턴타입 힌트를 주어 자동완성을 지원하게 함.
     강력한 타입 체크는 안되지만 컴파일에 영향을 주지 않기에 번거롭지 않음.
     ```tsx
     /**
      * @param number
      * @param string
      *
      * @returns {number}
      */
     function jsdoc(number, string) {}
     ```
     ```tsx
     /**
      * 상품 등록
      * @param parameters 상품등록 파라미터
      * @param { string } parameters.name 상품명
      * @param { number } parameters.stock 상품 갯수
      * @param { number } [parameters.size] 상품 사이즈
      *
      * @returns {*}
      */
     ```
2. 값을 변경하거나/변경하지 않는다는 주석 ⇒ `readonly`로 선언하여 규칙을 강제함.
3. 변수명에 타입 정보를 넣지 않음.

   단위가 있는 숫자의 경우에는 단위를 명확하게 표현하기 위해 표기함.
   [아이템37. 공식 명칭에는 상표 붙이기]
