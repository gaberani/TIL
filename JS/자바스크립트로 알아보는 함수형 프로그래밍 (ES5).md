# 자바스크립트로 알아보는 함수형 프로그래밍 (ES5)

## Section 1. 함수형 프로그래밍 개요

### 함수형 프로그래밍

* 정의

  성공적인 프로그래밍을 위해 **부수 효과**를 미워하고 **조합성**을 강조하는 프로그래밍 패러다임

* 상세 조건

  1. 부수 효과를 미워한다 => 순수 함수로 만든다 => (외부 변수에 의한) 오류를 줄이고 안정성을 높인다
  2. 조합성을 강조한다 => 모듈화 수준을 높인다 => (반복 작업이 줄어) 생산성을 높인다

* 순수 함수

  ```js
  /* 순수 함수 */
  /* 
   * 1. 동일한 인자에 항상 동일한 결과값을 도출해준다
   * 2. 
   * 부수 효과가 없다 => 함수가 결과값을 만드는 것 외에 외부 상태에 영향을 미치는 것을 부수 효과라 한다
   * 순수 함수란, 부수 효과가 없는 함수를 말한다
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
  
  // 순수 함수가 아니면 연관된 변수가 언제 바뀌느냐(판단 시점)에 따라 다루는 것이 중요하다
  // 하지만 순수 함수로 작성된 모듈은 판단 시점에 관계 없이 항상 같은 값을 낸다
  // 따라서, 평가 시점이 코드에 묶여있는 것이 아니라 개발자가 원하는 때에 다룰 수 있게 된다
  ```

* 일급 함수

  ```js
  /* 일급 함수 */
  /* js는 함수가 일급 함수이다.
   * 1. 함수를 언어에서 값으로 다룰 수 있다
   * 2. 런타임에서 언제나 정의할 수 있고 언제나 들고 다닐 수 있고 언제나 인자로 보낼 수 있다
   * 3. 원할 때 평가할 수 있다
   */
  
  var f1 = function(a) { return a * a; };
  console.log(f1); // 함수 내용
  
  var f2 = add;
  console.log(f2); // 함수 내용
  
  function f3(f) {
    return f();
  }
  
  console.log( f3(function() { return 10; }) ); // 10
  console.log( f3(function() { return 20; }) ); // 20
  ```

  순수 함수와 일급 함수의 개념과 특징을 이용해 함수의 조합성을 높여나가는 것을 함수형 프로그래밍이라 한다. 다시 말하면, 언제 평가해도 상관없는 순수 함수들을 만들고 그 순수 함수들을 값으로 들고 다니며 필요한 시점에 평가를 하며 다양한 로직을 만들어 나가는 것이다.

* 코드 예시

  ```js
  /* add_maker */
  function add_maker(a) {
    // 아래 return된 함수는 클로져, 일급 함수라는 개념이 사용되었다.
    // a는 add_maker 내에서 사용되거나 변경되지 않고 리턴해주는 함수에서만 사용하고 있는데
    // 그 함수에서 a의 상태를 변경하고 있지 않으므로 a는 항상 동일한 값이고 들어온 b를 더하는 순수 함수가 된다
    return function(b) { // 함수를 값으로 다루면서 언제 평가하든 동일하게 사용할 수 있다
      return a + b;
    }
  }
  
  
  var add10 = add_maker(10);
  console.log( add10(20) );
  
  var add5 = add_maker(5);
  var add15 = add_maker(15);
  console.log( add5(10) );
  console.log( add15(10) );
  
  
  function f4(f1, f2, f3) {
    return f3(f1() + f2());
  }
  
  f4(
    function() { return 2; },
    function() { return 1; },
    function(a) { return a * a; }
  ); 
  // 순수한 함수들을 조합하여 최종적으로 결과를 만들면 평가 시점이나 방법이 어떤 로직 안에서 할지 자유로워서 좋다
  // 비동기가 일어나는 시점이나 동시성이 필요한 시점에서 
  ```

