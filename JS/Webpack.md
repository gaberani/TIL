# Webpack

## 웹팩이란?

> 최신 프런트엔드 프레임워크에서 가장 많이 사용되는 **모듈 번들러(Module Bundler)**

* **모듈 번들러**란 웹 애플리케이션을 구성하는 자원(HTML, CSS, Javscript, Images 등)을 모두 각각의 모듈로 보고 이를 조합해서 병합된 하나의 결과물을 만드는 도구!



## 모듈이 뭐고 모듈 번들링은 뭘 한다는 거야?

> 모듈이란 프로그래밍 관점에서 **특정 기능을 갖는 작은 코드 단위**를 의미

* JS에선 아래와 같은 예시를 들 수 있다

```javascript
// math.js
// 합을 구하는 첫 번째 함수
function sum(a, b) {
  return a + b;
}

// 차이를 구하는 두 번째 함수
function substract(a, b) {
  return a - b;
}

// 원주율 값을 갖는 상수
const pi = 3.14;

export { sum, substract, pi }
```

* 이처럼 성격이 비슷한 기능들을 하나의 의미 있는 파일로 관리하면 모듈이라고 한다.
* ES6 문법인 export와 관련해서는  [여기](https://babeljs.io/docs/en/learn#modules) 를 참고

 