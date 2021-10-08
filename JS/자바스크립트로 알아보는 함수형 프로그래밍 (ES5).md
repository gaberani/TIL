# 자바스크립트로 알아보는 함수형 프로그래밍 (ES5)

## Section 1. 함수형 프로그래밍 개요

### 함수형 프로그래밍

* 정의

  성공적인 프로그래밍을 위해 **부수 효과**를 미워하고 **조합성**을 강조하는 프로그래밍 패러다임

* 상세 조건

  1. 부수 효과를 미워한다 => 순수 함수로 만든다 => (외부 변수에 의한) 오류를 줄이고 안정성을 높인다
  2. 조합성을 강조한다 => 모듈화 수준을 높인다 => (반복 작업이 줄어) 생산성을 높인다

* 코드 예시

  ```js
  /* 순수 함수 */
  // 1. 동일한 인자에 항상 동일한 결과값을 도출해준다
  /* 2. 
  	부수 효과가 없다 => 함수가 결과값을 만드는 것 외에 외부 상태에 영향을 미치는 것을 부수 효과라 한다
  	순수 함수란, 부수 효과가 없는 함수를 말한다
  */
  
  function add(a, b) {
    return a + b;
  }
  console.log( add(10, 5) );
  console.log( add(10, 5) );
  console.log( add(10, 5) );
  
  // 만약 c가 변하는 값이라면 add2 함수를 순수 함수로 볼 수 없으나
  // c를 상수로 사용하고 있다면 add2 함수를 순수 함수라고 할 수 있다
  var c = 10;
  function add2(a, b) {
    return a + b + c;
  }
  console.log( add2(10, 2) ); // 22
  console.log( add2(10, 3) ); // 23
  console.log( add2(10, 4) ); // 24
  
  // 아래와 같이 변화가 일어나는 값이라면 순수 함수가 아닌 것!
  c = 20;
  console.log( add2(10, 2) ); // 32
  console.log( add2(10, 3) ); // 33
  console.log( add2(10, 4) ); // 34
  
  // 부수 효과가 있는 함수이자 순수 함수가 아닌 함수
  var c = 20;
  function add3(a, b) {
    // 외부 상태에 관여
    c = b;
    return a + b;
  }
  
  console.log('c: ', c); // c: 20
  console.log( add(20, 30) );
  console.log('c: ', c); // c: 30
  
  // 순수 함수 X, 리턴 값 없음, 직접 인자로 들어온 값의 상태를 변경시킴
  var obj1 = { val: 10 };
  function add4(obj, b) {
    obj.val += b;
  }
  console.log( obj1.val ); // 10
  add4(obj1, 20);
  console.log( obj1.val ); // 30
  // 이렇게 코딩하는 것이 문제라고 말하는 것이 아니라
  // 이런 함수가 순수 함수가 아니라고 말하고 있는 것이다
  // 객체를 다루는 것은 필수이다, 그렇기에 
  // 함수형 프로그래밍은 객체의 값을 변형하는 데 있어서 약간 다른 방식을 취한다
  
  // 다시 순수 함수
  var obj1 = { val: 10 };
  function add5(obj, b) {
    // obj1의 val값만 참조해서 obj1(외부 변수)를 변형시키지 않고
    // 새로운 객체로 만들어 반환해 준다
    return { val: obj1.val + b }
  }
  
  console.log( obj1.val ); // 10
  var obj2 = add5(obj1, 20);
  console.log( obj1.val ); // 10
  console.log( obj2.val ); // 30
  
  
  ```

  

