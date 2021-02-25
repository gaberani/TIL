# VUE.js 퀵스타트

## 밑줄 우선 정리

---

### CH.01



---

### CH.02

- v-model 디렉티브의 수식어
    - lazy

        입력폼에서 이벤트가 발생할 때 입력한 값을 데이터와 동기화합니다.

        텍스트 박스에서라면 입력 후 포커스가 이동하거나 할 때 데이터 옵션값이 변경됩니다.

        > 옵션에 따라 계속 상호작용 시켜 보여주는 것이 아닌 입력한 뒤에 보여주는 것이 유용한 선택이라면 고려할만한 수식어라고 생각

    - **number**

        이 수식어를 지정하면 숫자가 입력될 경우 number 타입의 값으로 자동 형변환되어 데이터 옵션값으로 반영됩니다.

        > 종종 자료형을 맞추기 위해 직접 변환할 필요없이 이것을 쓰기

    - trim

        이 수식어를 지정하면 문자열의 앞뒤 공백을 자동으로 제거해줌



- **v-show와 v-if 차이점**

    실제 렌더링 여부에 따라 다르다. v-if는 조건에 부합되지 않으면 렌더링을 하지 않는 반면 v-show는 일단 HTML 요소를 렌더링한 후에 display 스타일 속성으로 화면에 보여줄지 여부를 결정.

    따라서, 화면이 자주 변경되는 부분은 v-if보다 v-show를 사용하는 것이 바람직하다.



- **v-for, v-if 디렉티브 작동 순서**

    **v-for가 먼저 수행되고 난 뒤에 v-if가 실행된다.**



- v-pre, v-once, v-cloak

  - **v-pre**

    HTML 요소에 대한 컴파일을 수행하지 않음.

    템플릿 문자열을 컴파일하지 않고 그대로 내보내기 위해서 사용

  - v-once

    HTML 요소를 단 한 번만 렌더링함. 이후에 Vue 인스턴스의 데이터를 변경하더라도 다시 렌더링을 하지 않기때문에 초기값만 보인다.

  - v-cloak

    가끔 v-for를 사용할 때 많은 데이터를 출력하면 화면에 일부 보간법 표현식이 그대로 나타나는 경우가 있다. 이는 Vue 인스턴스의 el 옵션의 템플릿을 컴파일할 때 발생하는 시간 때문에 일어나는 현상이다. **복잡한 UI의 경우 이런 경우가 빈번한데 이를 해결하기 위해 v-cloak을 쓴다.**

    v-cloak을 설정하면 화면 초기에 컴파일되지 않은 템플릿은 나타나지 않도록 할 수 있다.



* v-model과 v-on의 차이 알아보기

  **(EX.02-16)**

  ```vue
  // html
  <div id="exmaple">
      <p>
      국가명 : <input type="text" v-model="countryname" placeholder="국가명" />
      </p>
  	<!-- 나라 테이블 생략 -->
  </div>
  
  // js
  <script type="text/javascript">
  	var model = {
          countryname : "",
          countries : [
              // 나라 데이터 16개
     	    ]
      }
      
      var clist = new Vue({
          el : "#exmaple",
          data : model,
          computed : {
              filtered : function() {
                  var cname = this.countryname.trim();
                  return this.countries.filter(function(item,index) {
                      if (item.name.indexOf(cname) > -1) {
                          return true;
                      }
                  });
              }
          }
      });
  </script>
  ```
  
   이 예제의 경우 주의해야 할 사항으로 Computed에 대해 생각해야 한다. 보면 this.countryname 속성값을 cname이라는 변수에 할당하고 있는데 그 이유는 배열 객체의 filter 함수에 의해 호출되는 콜백 함수 안에서의 this는 바깥쪽의 this와 다르기 때문이다. **콜백 함수 안쪽에서의 this**는 Vue 객체가 아니고, 전역 객체(Global Object, 브라우저의 경우 window 객체)를 참조한다. 따라서 별도로 변수로 받아둘 필요가 있다.
  
  
  
  **(EX.02-17)**
  
  ```vue
  // html
  <div id="exmaple">
      <p>
      국가명 : <input type="text" :value="countryname" placeholder="국가명" @input="nameChanged" />
      </p>
      <!-- 나라 테이블 생략 -->
  </div>
  
  // js
  <script type="text/javascript">
  	var model = {
          countryname : "",
          countries : [
              // 나라 데이터 16개
     	    ]
      }
      
      var clist = new Vue({
          el : "#exmaple",
          data : model,
          computed : {
              filtered : function() {
                  var cname = this.countryname.trim();
                  return this.countries.filter(function(item,index) {
                      if (item.name.indexOf(cname) > -1) {
                          return true;
                      }
                  });
              }
          },
          methods : {
              nameChanged : function(e) {
                  this.countryname = e.target.value;
              }
          }
      });
      </script>
  ```
  
  예제 02-16에서는 v-model 디렉티브를 이용해 양방향 바인딩으로 사용자의 입력값을 받아내고 있다. 하지만 **v-model 디렉티브는 한글 입력 시에 이벤트가 발생하거나 엔터키를 입력해주어야 바인딩이 일어나는 문제가 있다.**
  
  이 문제를 해결하기 위해서는 **v-on 디렉티브를 이용해 input 이벤트나 keyup 이벤트 처리를 수행하면 된다.**
  
  예제에서 보면 @input마다 이벤트를 처리하는 식으로 동기화하여 사용하고 있다.






---

### CH.03

* this와 arrow function

1. js에서의 this?
    : 현재 호출중인 메서드를 보유한 객체를 가리킴 (default)

   ```javascript
   var obj = { result: 0 };
   obj.add = function(x,y) {  
      this.result = x+y;
   }
   obj.add(3,4)
   console.log(obj)
   ```



2. this가 바인딩되는 시점 : 메서드, 함수가 호출될 때마다!!!
   
    메서드를 호출할 때 this를 직접 지정하여 호출할 수 있음
    
    * apply, call 메서드 : 지정하여 호출까지
    * bind 메서드 : this를 강제로 지정한 새로운 함수를 리턴함.
    
    ```javascript
    var add = function(x,y) {  
       this.result = x+y;
    }
    //add(4,5);
    
    var obj = {};
    //add.apply(obj, [4,5])
    var add2 = add.bind(obj);
    add2(4,5)
    ```




3. 함수가 중첩되었을 때의 문제(전통적인 함수에서의...)

   ```javascript
   var obj = { result:0 };
   obj.add = function(x,y) {
     function inner() {
        this.result = x+y;
     }
     //inner();
     //inner.apply(this);
     inner = inner.bind(this);
     inner();
   }
   obj.add(4,5)
   ```




4. 화살표 함수는 lexical binding(X)

   ```javascript
   var obj = { result:0 };
   obj.add = function(x,y) {
     var inner = () => {
        this.result = x+y;
     }
     inner()
   }
   obj.add(4,5)
   ```



- **계산형 속성(Computed)**

  연산 로직이 필요한 경우  Computed라는 속성과 함께 함수를 등록해두면 마치 속성처럼 사용 가능하다.

  계산형 속성을 좀더 알아보기 위해 메서드로 작성한 경우와 비교해보자 **(EX.03-04)**
  
  - 메서드로 작성한 경우
  
  ```jsx
  // html
  <input type="text" v-model="num" /><br />
      1부터 입력된 수까지의 합 : <span>{{sum()}}</span>
  // js
  methods : {
          sum : function() {
              console.log(Date.now());
              var n = Number(this.num);
              if (Number.isNaN(n) || n < 1)  return 0;
              return ((1+n) * n) / 2;
          }
      }
  ```
  
  메서드로 작성 시 HTML 안에서 보간법을 이용해 함수 자체를 호출하는 것을 볼 수 있다. 콘솔창에서 vmSum.sum()과 같이 실행하기에 매번 Date.now()가 보인다.
  
  
  
  - Computed로 작성한 경우
  
  ```jsx
  // html
  <input type="text" v-model="num" /><br />
      1부터 입력된 수까지의 합 : <span>{{sum}}</span>
  
  // js
  computed : {
          sum : function() {
              var n = Number(this.num);
              if (Number.isNaN(n) || n < 1)  return 0;
              return ((1+n) * n) / 2;
          }
      }
  ```
  
  콘솔창에서 vmSum.sum로 computed로 작성한 sum을 확인하면 한번 캐싱된 이후이기에 Date.now()와 같이 아무 곳에도 의존하지 않는 것은 값이 변하지 않는 것을 확인할 수 있다.
  
  
  
  - **캐싱이 필요한 이유**
  
  계산에 시간이 많이 걸리는 computed 속성인 A가 있다고 가정해보면 이 속성을 계산하려면 거대한 배열을 반복해서 다루고 많은 계산을 해야한다. 그런데 A 에 의존하는 다른 computed 속성값도 있을 수 있습니다. 캐싱을 하지 않으면 A 의 getter 함수를 꼭 필요한 것보다 더 많이 실행하게 된다!!



* **Computed VS Watch**

  하나의 데이터를 기반으로 다른 데이터를 변경할 필요가 있을 때 흔히 사용 할 수 있는 Computed 속성 외에도 Watch 속성이 있다. 주로 긴 시처리 시간이 필요한 비동기 처리에 적합하다는 특징을 갖고있다. 예제를 통해 확인해보자.

  **(EX.03-05)**

  ```javascript
  // html
  <div id="example">
      x : <input type='text' v-model="x" /><br />
      y : <input type='text' v-model="y" /><br />
      덧셈 결과 : {{sum}}
  </div>
  
  // js
  data : { x:0, y:0, sum:0}
  watch : {
          x : function(v) {
              console.log('## x 변경')
              var result = Number(v) + Number(this.y);
              if (isNaN(result)) this.sum = 0;
              else this.sum = result;
          },
          y : function(v) {
              console.log('## y 변경')
              this.y = v;
              var result = Number(this.x) + Number(v);
              if (isNaN(result)) this.sum = 0;
              else this.sum = result;
          }
      }
  ```
  
    Watch 옵션에 등록되는 것은 속성의 이름과 해당 속성이 변경되었을 때 호출할 함수로서 함수는 인자를 전달받는데 이것은 변경된 속성의 값이다. 위에서 x를 기존으로 보면 x는 x 속성, function(v)는 x 속성이 변경될 때 호출되는 함수, v는 변경된 x 속성의 값을 의미한다. 기존의 속성 값 또한 사용이 가능한 데 이는 공식 문서를 통해 확인하자.
  
    여기서 **Watch 속성의 한 가지 단점**을 발견할 수 있다. 값이 바뀔 때마다 매번 함수가 호출된다는 것인데 이 예제는 사실 Computed가 더 효과적이다.
  
  ```vue
    
  
    
    
    
    **(EX.03-06)**
    
    ```vue
    // html
    <div id="example">
        x : <input type='text' v-model="x" /><br />
        y : <input type='text' v-model="y" /><br />
        덧셈 결과 : {{sum}}
    </div>
    
    // js
    <script type="text/javascript">
    var vm = new Vue({
        el : "#example",
        data : { x:0, y:0 },
        beforeUpdate : function() {
            console.log("before update")
            console.log(this.x);
        },
        updated : function() {
            console.log("update")
            console.log(this.x);
        },
        computed : {
            sum : function() {
                var result = Number(this.x) + Number(this.y);
                if (isNaN(result)) return  0;
                else return result;
            }
        }
    })
    </script>
  ```
  
   이렇게 작성할 경우 sum을 data에 넣을 필요 없이 x, y만으로 작성이 가능하고 sum이 참조될 때만 해당 함수가 호출되기에 간단하고 편리하다. 하지만 이와 반대로 긴 시간이 필요한 비동기 처리가 필요할 때는 관찰 속성이 유용하다. 



 * 비동기 처리

    비동기 처리의 가장 대표적인 예로 외부 서버와의 통신 기능이 있다. 이러한 기능을 제공하는 라이브러리는 jQuery의 `AJAX`, Vue.js의 라이브러리 중 하나인 `vue-resource`, promise 기반의  HTTP Client 기능을 수행하는 `axios`, `fetch` 등 다양한 것들이 있다. 이 중 책의 예제에선 하위 브라우저와 IE를 지원하고자 `fetch polyfill`을 이용하기로 했다.

   

---

### CH.05

* 인라인 스타일

    기존 HTML에서 인라인 스타일의 사용을 권장하지 않고 Vue.js에서도 마찬가지이다. 하지만 인라인 스타일이 필요한 경우도 있으니 사용방법을 익혀둬야 한다.

    그 경우란 Vue 인스턴스의 데이터로 처리하려면 케밥 표기법을 사용할 수 없다. `자바스크립트(JS)`언어에선 변수명, 속성명으로 대쉬(-) 기호를 사용할 수 없기 때문이다. 

  **(EX.05-02)**

  ```html
  // html
  <div id="example">
    <button id="a" v-bind:style="style1" @mouseover.stop="overEvent" 
        @mouseout.stop="outEvent">테스트</button>
  </div>
  ```

  ```javascript
  // js
  var vm = new Vue({
    el : "#example",
    data : {
      style1 : { backgroundColor:"aqua", border:'solid 1px gray', 
        with:'100px', textAlign:'center' }
    },
    methods : {
      overEvent : function(e) {
        this.style1.backgroundColor = "purple";
        this.style1.color = "yellow";
      },
      outEvent : function(e) {
        this.style1.backgroundColor = "aqua";
        this.style1.color = "black";
      }
    }
  })
  ```



* 클래스 바인딩

  CSS 클래스를 바인딩 하기 위해서 :class를 사용한다. 개별적인 클래스 단위로 true일 경우 클래스가 적용되는 방식이다.

  ```html
  <div id="example">
      <button id="btn1" v-bind:class="{ set1:s1, set2:s2, set3:s3 }">버튼1</button>
      <p>
          <input type="checkbox" v-model="s1" value="true" />set1 디자인<br/>
          <input type="checkbox" v-model="s2" value="true" />set2 디자인<br/>
          <input type="checkbox" v-model="s3" value="true" />set3 디자인<br/>
      </P>
  </div>
  ```

  ```javascript
  <script type="text/javascript">
  var vm = new Vue({
    el : "#example",
    data : { s1 : false, s2 : false, s3 : false }
  })
  </script>
  ```

  

* Computed, Methods를 이용한 스타일 적용

  계산형 속성은 스타일을 적용할 때도 사용할 수 있다. 입력 값이 올바른 범위에 포함되지 않을 때 스타일을 적용하는 예제를 통해 알아보자.

  **(EX.05-07)**

  ```html
  // html
  <div id="example">
      <div>
          <p>1부터 100까지만 입력가능합니다.</p>
          <div>
              점수 : <input type="text" class="score" 
                  v-model.number="score" v-bind:class="info" />
              <img src="images/error.png" class="warnimage" v-show="info.warning" />
          </div>
      </div>
  </div>
  ```

  ```javascript
  // js
  var vm = new Vue({
  el : "#example",
    data : { 
      score : 0
    },
    computed : {
      info : function() {
          if (this.score >= 1 && this.score <= 100) 
              return { warning:false };
          else 
              return { warning:true };
      }
    }
  })
  ```
  
  예제에선 score가 1부터 100까지일 때만 유효한 값으로 판단하므로 그 여부에 따라 warning이 적용될 지 info가 적용될 지 달라진다.



* SFC(단일 파일 컴포넌트) 스타일 적용

  Vue.component()로 작성한 컴포넌트에 자신만의 클래스가 적용되어 있어도 추가로 클래스 바인딩할 수 있다.

  ```vue
  // html
  <body>
      <div id="example">
      	<center-box v-bind:class="boxstyle"></center-box>
      </div>
      // js
      <script type="text/javascript">
      Vue.component('center-box', {
          template : '<div class="center">중앙에 위치</div>'
      })
      var vm = new Vue({
          el : "#example",
          data : {
             boxstyle : { boxcolor : true }
          }
      })
      </script>
  </body>
  
  <style>
      .boxcolor { background-color:orange;   }
      .center {  width:200px; height:100px; line-height: 100px;
          text-align: center; border: 1px solid gray; }
  </style>
  ```

  콘솔창에서 vm.boxstyle.boxcolor=false를 실행하면 center-box 컴포넌트의 배경색이 바뀌는 것을 확인할 수 있다.

  

