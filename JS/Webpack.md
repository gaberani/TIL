# Webpack

## 웹팩이란?

> 최신 프런트엔드 프레임워크에서 가장 많이 사용되는 **모듈 번들러(Module Bundler)**

* **모듈 번들러**란 웹 애플리케이션을 구성하는 자원(HTML, CSS, Javscript, Images 등)을 모두 각각의 모듈로 보고 이를 조합해서 병합된 하나의 결과물을 만드는 도구!



## 모듈이 뭐고 모듈 번들링은 뭘 한다는 거야?

> **모듈**이란 프로그래밍 관점에서 **특정 기능을 갖는 작은 코드 단위**를 의미

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
* ES6 문법인 export와 관련해서는 [여기](https://babeljs.io/docs/en/learn#modules) 를 참고

> **웹팩에서 지칭하는 모듈**이란 자바스크립트 모듈에만 국한되지 않고 **웹 애플리케이션을 구성하는 모든 자원**을 의미
>
> 다시 말해, HTML, CSS, Javascript, Images, Font 등 많은 파일들 하나하나가 모듈이다!

---

> **모듈 번들링**이란 아래 그림과 같이 웹 애플리케이션을 구성하는 몇십, 몇백개의 자원들을 하나의 파일로 병합 및 압축 해주는 동작을 뜻한다

빌드, 번들링, 변환 이 세 단어 모두 같은 의미한다.

![image-20210815233705491](typora-images/image-20210815233705491.png)



## 웹팩의 등장 배경

1. **파일 단위의 자바스크립 모듈 관리의 필요성**

   자바스크립트의 변수 유효 범위는 기본적으로는 전역 범위를 갖기에 어디서든 접근하기 편리하다. 하지만, 이러한 장점이 실제로 웹 애플리케이션을 개발할 때는 아래와 같은 문제를 일으킨다.

   ```javascript
   // app.js
   var num = 10;
   function getNum() {
     console.log(num);
   }
   
   // main.js
   var num = 20;
   function getNum() {
     console.log(num);
   }
   ```

   ```html
   <!--
   	위에 두 개의 스크립트를 단순히 불러와서 보여줄 경우
   -->
   
   <!-- index.html -->
   <html>
     <head>
       <!-- ... -->
     </head>
     <body>
       <!-- ... -->
       <script src="./app.js"></script>
       <script src="./main.js"></script>
       <script>
         getNum(); // 20
       </script>
     </body>
   </html>
   ```

   스크립트 순서에 따라 10을 선언하며 할당하고 이후에 20을 선언하고 할당했기에 콘솔에는 20이 나온다.

   이러한 문제점은 실제로 복잡한 애플리케이션을 개발할 때 발생하기 쉽다. 변수의 이름을 모두 기억하지 않는 이상 중복 선언하거나 의도치 않은 값을 할당할 수 있다. 파일 단위로 변수를 관리하고 싶은 욕구, 자바스크립트 모듈화에 대한 욕구를 예전까진 [AMD](https://joshua1988.github.io/webpack-guide/motivation/why-webpack.html), [Common.js](https://joshua1988.github.io/webpack-guide/motivation/why-webpack.html)와 같은 라이브러리로 풀어왔다.

   

2. **웹 개발 작업 자동화 도구 (Web Task Manager)**

   이전부터 프런트엔드 개발 업무를 할 때 가장 많이 반복하는 작업은 텍스트 편집기에서 코드를 수정하고 저장한 뒤 브라우저에서 새로 고침을 누르는 것이었습니다. 그래야 화면에 변경된 내용을 볼 수 있었죠.

   이외에도 웹 서비스를 개발하고 웹 서버에 배포할 때 아래와 같은 작업들을 해야 했습니다.

   - HTML, CSS, JS 압축
   - 이미지 압축
   - CSS 전처리기 변환

   이러한 일들을 자동화 해주는 도구들이 필요했습니다. 그래서 Grunt와 Gulp 같은 도구들이 등장했습니다.

   요즘엔 

   

3. **웹 애플리케이션의 빠른 로딩 속도와 높은 성능**

   일반적으로 특정 웹 사이트를 접근할 때 **5초 이내**로 웹 사이트가 표시되지 않으면 대부분의 사용자들은 해당 사이트를 벗어나거나 집중력을 잃게 된다. 

   그래서 웹 사이트의 로딩 속도를 높이기 위해 많은 노력들이 있었습니다. 그 중 대표적인 노력이 브라우저에서 서버로 요청하는 파일 숫자를 줄이는 것입니다. 이를 위해 앞에서 살펴본 **웹 태스크 매니저**를 이용해 파일들을 압축하고 병합하는 작업들을 진행했습니다.

   뿐만 아니라 초기 페이지 로딩 속도를 높이기 위해 나중에 필요한 자원들은 나중에 요청하는 레이지 로딩(Lazy Loading)이 등장했죠.

   웹팩은 기본적으로 **필요한 자원은 미리 로딩하는게 아니라 그 때 그 때 요청하자는 철학**을 갖고 있습니다.



## 웹팩으로 해결하려는 문제

등장 배경에서도 살펴봤지만 다음 4가지로 정리할 수 있다.

- 자바스크립트 변수 유효 범위
- 브라우저별 HTTP 요청 숫자의 제약
- 사용하지 않는 코드의 관리
- Dynamic Loading & Lazy Loading 미지원

하나하나 자세히 살펴보자!

1. **자바스크립트 변수 유효 범위**

   웹팩은 변수 유효 범위의 문제점을 [ES6의 Modules](https://babeljs.io/docs/en/learn#modules) 문법과 **웹팩의 모듈 번들링**으로 해결하고 있다

   

2. **브라우저별 HTTP 요청 숫자의 제약**

   TCP 스펙에 따라 **브라우저에서 한 번에** 서버로 보낼 수 있는 **HTTP 요청 숫자는 제약**되어 있습니다. 아래의 표는 최신 브라우저 별 최대 HTTP 요청 횟수입니다.

   |   **브라우저**    | **최대 연결 횟수** |
   | :---------------: | :----------------: |
   |   익스플로러 7    |         2          |
   | 익스플로러 8 ~ 9  |         6          |
   | 익스플로러 10, 11 |       8, 13        |
   |       크롬        |         6          |
   |      사파리       |         6          |
   |    파이어폭스     |         6          |
   |      오페라       |         6          |
   |  안드로이드, iOS  |         6          |

   따라서, HTTP 요청 숫자를 줄이는 것이 웹 애플리케이션의 성능을 높여줄 뿐만 아니라 사용자가 사이트를 조작하는 시간을 앞당겨 줄 수 있죠.

   > ⚠️ 알아두면 좋아요!
   > 클라이언트에서 서버에 HTTP 요청을 보내기 위해서는 먼저 TCP/IP가 연결되어야 합니다.

   웹팩을 이용해 여러 개의 파일을 하나로 합치면 위와 같은 브라우저별 HTTP 요청 숫자 제약을 피할 수 있습니다.

   

3. **Dynamic Loading & Lazy Loading 미지원**

   [Require.js](https://requirejs.org/)와 같은 라이브러리를 쓰지 않으면 동적으로 원하는 순간에 모듈을 로딩하는 것이 불가능 했었다. 그러나 이젠 웹팩의 [Code Splitting](https://joshua1988.github.io/webpack-guide/motivation/problem-to-solve.html) 기능을 이용하여 원하는 모듈을 원하는 타이밍에 로딩할 수 있습니다.



## 웹팩의 4가지 주요 속성

웹팩의 빌드 (파일 변환) 과정을 이해하기 위해서는 아래 4가지 주요 속성에 대해 알고 있어야 한다

* entry
* output
* loader
* plugin

각 속성에 대해 자세히 살펴보자

1. **Entry**

   `Entry`속성은 웹팩에서 웹 자원을 변환하기 위해 필요한 최초 진입점이자 JS 파일 경로다

   ```js
   // SPA 서비스에 주로 쓰이는 webpack.config.js
   module.exports = {
     entry: './src/index.js'
   }
   
   // MPA 서비스에 적합한 webpack.config.js 
   module.exports = {
     entry: {
       login: './src/LoginView.js',
       main: './src/MainView.js'
     }
   }
   ```

   위 코드는 웹팩을 실행했을 때 src 폴더 및의 index.js 파일을 대상으로 웹팩이 빌드를 수행한다

   엔트리 포인트는 1개로 한정된 것이 아니라 여러 개가 될 수도 있다.

   다시 말해, `Entry`속성에 지정된 파일에는 웹 애플리케이션의 전반적인 구조와 내용이 담겨져 있어야 한다. 웹팩이 해당 속성에 지정된 파일을 가지고 웹 애플리케이션에서 사용되는 모듈들의 연관 관계를 이해하고 분석하기 때문에 어플리케이션을 동작시킬 수 있는 내용들이 담겨야 하기 때문

   예를 들어, 블로그 서비스를 웹팩으로 빌드한다고 했을 때 코드의 모양은 아래와 같을 수 있다

   ```js
   // index.js
   import LoginView from './LoginView.js';
   import HomeView from './HomeView.js';
   import PostView from './PostView.js';
   
   function initApp() {
     LoginView.init();
     HomeView.init();
     PostView.init();
   }
   
   initApp();
   ```

   위 코드는 블로그 서비스가 SPA임을 가정하고 작성한 코드다. 사용자의 로그인 화면, 로그인 후 진입하는 메인 화면, 그리고 게시글을 작성하는 화면 등 웹 서비스에 필요한 화면들이 모두 index.js 파일에서 불려져 사용되고 있기 때문에 웹팩을 실행하면 해당 파일들의 내용까지 해석하여 파일을 빌드해줄 것입니다.

   ![image-20211014234948096](/Users/hyeonsung/Desktop/GB/TIL/JS/Webpack.assets/image-20211014234948096.png)

   위와 같이 모듈 간의 의존 관계가 생기는 구조를 **디펜던시 그래프** 라고 한다

2. **Output**

   `output` 속성은 웹팩을 돌리고 난 결과물을 저장할 파일 경로를 의미한다. 앞에서 배운 `entry` 속성과는 다르게 객체 형태로 옵션들을 추가해야 한다.

   최소한 `filename`은 지정해줘야 하며 일반적으로 아래와 같이 `path` 속성을 함께 정의한다.

   ```js
   // webpack.config.js
   var path = require('path');
   
   module.exports = {
     output: {
       filename: 'bundle.js',
       path: path.resolve(__dirname, './dist')
     }
   }
   ```

   

3. **Loader**

   

4. **Plugin**

   
