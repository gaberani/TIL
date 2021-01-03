# VUE_TIL

## 밑줄 우선 정리

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

    실제 렌더링 여부에 따라 다르다. v-if는 조건에 부합되지 ㅇ낳으면 렌더링을 하지 않는 반면 v-show는 일단 HTML 요소를 렌더링한 후에 display 스타일 속성으로 화면에 보여줄지 여부를 결정.

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

  Watch 옵션에 등록되는 것은 속성의 이름과 해당 속성이 변경되었을 때 호출할 함수로서 함수는 인자를 전달받는데 이것은 변경된 속성의 값이다. 기존의 속성 값 또한 사용이 가능한 데 이는 공식 문서를 통해 확인하자.







---

