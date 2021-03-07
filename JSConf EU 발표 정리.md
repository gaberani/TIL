# JSConf EU 발표

### 발표 주제 : 어쨌든 이벤트 루프는 무엇입니까? 

### 발표자 : Philip Roberts

---

* 자바스크립트 런타임 단순화 그림

![image-20210307143617792](/Users/hyeonsung/Library/Application Support/typora-user-images/image-20210307143617792.png)

메모리 할당이 일어나는 힙과 콜 스택이 보인다. 재미있는 점은 V8 프로젝트를 클로닝해서 코드 베이스를 들여다 보면 SetTimeout 이나 DOM, HTTP 요청을 관리하는 코드들은 찾아볼 수 없다.



* 발표자가 알려주고자 하는 아키텍쳐

![image-20210307143805628](/Users/hyeonsung/Library/Application Support/typora-user-images/image-20210307143805628.png)

그림을 보면 V8 런타임과 브라우저가 제공하는 웹 API가 있다. 브라우저는 DOM, AJAX, timeout 등과 함께 `event loop`와 `CallBack Que` 를 가지고 있다. 이것들이 어떤식으로 연결되어 움직이는지 정확하게 이해하는 분들은 많지 않을 것이다. 이것에 대해 발표하고자 한다.

---

* Call Stack 테스트 코드

```javascript
function multiply(a, b) {
  return a * b;
}

function squear(n) {
  return multiply(n, n);
}

function printSquare(n) {
  var squared = squared(n);
  console.log(squared);
}

printSquare(4);
```

 함수를 실행하려면 Call Stack에 해당하는 함수를 집어 넣는다. 함수에서 `return`이 일어나면 스택의 가장 위쪽에서 해당 함수를 꺼내게 된다. 이것이 `Call Stack`이 하는 일의 전부다.



 테스트 코드를 실행하면 실행되는 코드 자체를 말하는 메인 함수를 스택에 집어넣게 된다. 그러면 이제 함수들을 정의하게 되고 마지막에 가서 printSqure를 만나게 되는데. printSqure(4)는 함수 호출이니 스택에 함수를 추가한다. 



* Async Callbacks & The Call Stack

```javascript
console.log('hi');

setTimeout(function () {
  console.log('there');
}, 5000);

console.log('JSConfEU');

// Call Stack
//				|					|				 |
//				| console |				 | setT		|
// main()	| main()	| main() | main() |
```

setTimeout이 콜 스택에서 사라지고 다음 콘솔(JSConfEU)가 실행된 것은 이벤트 루프와 동시성이 도와주는 것!

JavaScript 런타임은 한번에 하나만 할 수 있다. 하지만 브라우저가 Web API 와 같은 것들을 제공하기에 가능하다.

SetTimeout이 Call Stack에 들어가면 Web API를 통해 브라우저가 타이머를 실행시키고 카운트 다운을 시작한다. SetTimeout은 브라우저에서 제공하는 API이면서 V8 소스코드에 존재하지 않는걸 생각해보면 이것으로 SetTimeout 호출 자체가 완료되었고 스택에서 함수를 지운다.

이제 Web API 에서 실행하고 있는 타이머가 남아있데 5초 뒤에 종료될 것이다. 하지만 Web API는 작성된 코드에 갑자기 끼여들 순 없다. 이 때 `Task Queue`와 `CallBack Queue`가 활약한다.

모든 Web API는 작동이 완료되면 콜백을 태스크 큐에 밀어넣는다.

이제 이벤트 루프에 대해 설명할 수 있다. 이벤트 루프란 이 전체 시스템에서 아주 단순한 일을 하는 작은 파트다. 이벤트 루프의 역할은 `CallStack`과 `Task Queue`를 주시하는 것이다. 

스택이 비어있으면, 큐의 첫번째 콜백을 스택에 쌓아 효과적으로 실행할 수 있게 해준다.

![image-20210307150911391](/Users/hyeonsung/Library/Application Support/typora-user-images/image-20210307150911391.png)

> 스택이 비어있고 태스크 큐에 콜백이 하나 있다

![image-20210307150949180](/Users/hyeonsung/Library/Application Support/typora-user-images/image-20210307150949180.png)

> 스택은 Javascript의 부분임을 기억하자

이벤트 루프가 스택에 넣어줬으므로 V8 엔진으로 돌아가 console.log('there')을 실행한다.



이제 우리는 Javascript 비동기 함수가 어떤 식으로 동작하는 알게 되었다. 특히 무엇인가 알수없는 문제가 생겼을 때 SetTimeout 0 를 사용하면 해결될거야 라고 말하던 상황이 함수를 0초 후에 실행하라는 뜻이 아니라 스택에 비어있을 때까지 기다리게 하기 위해서라는 것을 알 수 있다.



---

* 동기, 비동기에 따른 차이

```javascript
// synchronous
[1,2,3,4].forEach(function(i) {
	// delay();
  console.log(i);
});

// Asynchronous
function asyncForEach(array, cb) {
  array.forEach(function() {
    setTimeout(cb, 0);
  })
}

asyncForEach([1,2,3,4], function(i) {
  // delay();
  console.log(i);
})
```

* 동기 버전

 forEach 함수의 경우, 함수를 실행시키기는 하지만 비동기적으로 실행하지 않고 자신의 자체적 스택에서 실행시킨다.

![image-20210307161350591](/Users/hyeonsung/Library/Application Support/typora-user-images/image-20210307161350591.png)



* 비동기 버전

![image-20210307161431881](/Users/hyeonsung/Library/Application Support/typora-user-images/image-20210307161431881.png)

  콜백을 큐에 쌓고 스택이 비워지면 쌓인 콜백들이 실행되게 된다. 이 예시에서는 콘솔 함수가 금방 실행되서 async의 이점이 잘 드러나지 않지만, 각 배열 요소에 대해 오래 걸리는 처리를 해야 한다고 하면(delay를 걸어서 확인 가능)



* 렌더링과 어떠한 관계가 있는가?

  브라우저는 우리가 자바스크립트로 하는 무언가로 인해 제약을 받는다. 브라우저는 기본적으로 화면을 매 16.6 ms, 1초에 60프레임을 repaint하는게 이상적이고 그게 제일 빠른 것이다. 위에서 말했듯이 우리로 인해 여러가지 제약을 받는데 스택에 코드가 있으면 렌더링을 못한다. 렌더도 하나의 콜백처럼 행동하기 때문에 스택이 비워질 때까지 기다려야 한다. 다른 점은 우리가 자바스크립트로 실행한 콜백에 비해 더 높은 우선순위를 갖는다.

  즉 스택에 필요없는 느린 코드를 쌓아서 `Event Loop`를 막아서 브라우저가 할 일을 못하게 만들지 말 것, 유동적인 UI를 만들 것을 명심하자.

  **예를 들면** 스크롤 핸들러를 이용해서 애니메이션을 넣거나 할 때, 수 많은 콜백을 쌓을 것이 아니라 매 몇초마다 혹은 유저가 스크롤을 멈출 때 까지로 작업량을 줄여서 브라우저가 할 일을 제대로 하게 만들자

