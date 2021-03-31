# NUTX_TIL

## SSR(server side rendering)

* 장점
  1. SPA에 비해 SEO가 용이함. SPA는 검색 엔진 봇이 빈 페이지를 받는다. 하지만 SSR의 경우 렌더링 된 페이지를 받기 때문
  2. 서버단에서 처리하기에 페이지를 받는 속도가 빨라져 느린 디바이스나 네트환경에 있어도 빠르게 받아볼 수 있음

* 단점
  1. 제한된 개발 조건, 외부 라이브러리를 사용할 때 특정 라이프사이클 훅 안에서만 작동할 경우 따로 관리해줘야한다.
  2. 세팅과 개발에 많은 노력이 필요. static file server로 배포 가능한  SPA와 달리 SSR에선 서버가 app을 렌더링하고 Node.js server runtime 환경에서 
  3. 서버사이드 부하 측면에서 보면 Node.js에서 완전한 앱을 만들어서 주기 때문에 CPU 자원이 더 많이 든다.(SPA의 경우 정적인 파일만 제공해주기에 가볍다) 그렇기에 많은 트래픽을 대응해야 한다면 적절한 서버 부하와 현명한 캐시 전략을 구성할 것을 명심  



## 프로젝트 디렉토리 구조

#### :scroll:[공식 문서](https://ko.nuxtjs.org/docs/2.x/directory-structure/nuxt/)

* assets
  * css, image, font와 같은 리소스를 모아둔다.
* components
  * 사용될 컴포넌트들을 모아두며 해당 경로에 있는 컴포넌트들은 Nuxt.js의 비동기 데이터 함수인  `asyncData`나 `fetch`를 사용할 수 없다.
* content
  * `@nuxt/content` 모듈을 사용하여 애플리케이션을 확장할 수 있다. Markdown, JSON, XML, CSV와 같은 파일을 가져오고 관리할 수 있다.
* [layouts](https://nuxtjs.org/docs/2.x/concepts/views#layouts)
  * 애플리케이션 전체에 대한 레이아웃을 포함한다. 기본으로 default.vue가 생성되고 상황에 맞게 레이아웃을 생성하고 이에 맞게 v-if와 같은 조건을 걸어 보여줄 여부를 결정할 수도 있다.
  * layout 값이 선언되어 있지 않은 모든 페이지는 layouts 폴더 안에 default.vue를 사용한다.
* middleware
  * 애플리케이션에 사용될 middleware를 포함한다. 
* module
  * Nuxt 프레임워크의 핵심 기능을 확장하고 통합 및 추가할 수 있다. 사용자가 직접 모듈을 작성할 수 있으며 [Nuxt 커뮤니티](https://github.com/nuxt-community/awesome-nuxt)에 많은 모듈이 이미 공개되어있다.
* pages
  * 실제 애플리케이션의 페이지 구성을 포함하며 해당 디렉토리의 구조에 따라 router가 자동 생성된다.
* plugins
  * 애플리케이션에 바인딩 될 외부 혹은 내부 plugins를 포함한다. plugins는 애플리케이션이 인스턴스 화 되기 전에 실행하며 전역적으로 구성 요소를 등록하고 함수 또는 상수를 삽입할 수 있다.
* static
  * 정적인 파일들을 포함한다. 구성에 따라 html, js 파일도 포함 시킬 수 있다.(정적 페이지 포함)
* store
  * 애플리케이션에 사용될 vuex store 파일들을 포함한다. 기본적으로 비활성화 상태이도 store 디렉토리에 index.js파일을 작성하면 store가 활성화된다. 구성에 따라 모듈화하여 사용할 수 있다.



## Nuxt.js의 라우팅

 Nuxt.js는 `vue-router`를 내장하고 있고 라우터가 `pages`의 vue 파일들 디렉토리 구조에 따라 자동적으로 매칭해 준다. 만약 라우팅에 대해 좀더 보고 싶다면 [여기](https://ko.nuxtjs.org/docs/2.x/features/file-system-routing/)서 볼 수 있다.

 NuxtLink 컴포넌트를 이용해 기존 Vue.js 프로젝트에서 RouterLink 컴포넌트를 썼던 것처럼 사용할 수 있다. 만약 다른 웹사이트로 보내주기 위해선 꼭 `<a>` 태그를 사용해야 한다. NuxtLink에 대한 자세한 설명은 [여기](https://ko.nuxtjs.org/docs/2.x/features/nuxt-components/#the-nuxtlink-component)서 볼 수 있다.

* NuxtLink는 Nuxt.js에 포함된 컴포넌트로 import하지 않고 쓸 수 있다.
* HTML의 `<a>` 태그와 유사하다.



#### :star: prefetchLinks :star:

 Nuxt.js는 `smart prefetching` 기능을 포함하고 있다. 2.4.0 버전부터 지원하기 시작해서 페이지 내의 `<nuxt-link>` 를 인식해 해당 페이지의 viewport 내에 보이고 있는 링크들의 js와 css를 미리 가져온다. 이것은 [quicklink](https://github.com/GoogleChromeLabs/quicklink)에서 영감을 받아 제작했다고 한다.



## serverMiddleware vs middleware

routes middleware와 달리 CSR, SSR 각 라우트 되기 전에 실행한다. serverMiddleware property에 있는 미들웨어들은 vue-server-renderer 전에 server-side에서 동작하기에 주로 API 요청들이나 정적인 자원들을 받는데 쓰일 수 있다.

```javascript
// 공식 문서 주의사항

Do not add serverMiddleware to the middleware/ directory.
// 서버 미들웨어를 미들웨어 하위 디렉토리에 넣지 말 것

```



- 등록하는 방법

  ```javascript
  // nuxt.config.js
  import serveStatic from 'serve-static'
  
  export default {
    serverMiddleware: [
      // Will register redirect-ssl npm package
      // node_modules에 있는 건 그냥 등록하면 됌
      'redirect-ssl',
  
      // Will register file from project server-middleware directory to handle /server-middleware/* requires
      // 파일을 등록하려면 특정 path에 저장해서 어떤 것으로 처리할지 명시해 줘야함
      { path: '/server-middleware', handler: '~/server-middleware/index.js' },
  
      // We can create custom instances too
      // 커스텀으로 만들어서 등록도 가능하다.
      { path: '/static2', handler: serveStatic(__dirname + '/static2') }
    ]
  }
  ```

  

* Cusotm Server Middleware

  커스텀으로 만드는 방법과 config에 등록 방법

  ```javascript
  // server-middleware/logger.js
  export default function (req, res, next) {
    // req is the Node.js http request object
    console.log(req.url)
  
    // res is the Node.js http response object
  
    // next is a function to call to invoke the next middleware
    // Don't forget to call next at the end if your middleware is not an endpoint!
    next()
  }
  
  
  // nuxt.config.js
  serverMiddleware: [
    { path: "/server-middleware", handler: "~/server-middleware/rest.js" },
  ],
  ```



* Custom API endpoint

  서버 요청 endpoint도 지정할 수 있다.

  링크 클릭 수 집계가 이 로직으로 하고있음.

  ```javascript
  // server-middleware/rest.js
  const bodyParser = require('body-parser')
  const app = require('express')()
  
  app.use(bodyParser.json())
  app.all('/getJSON', (req, res) => {
    res.json({ data: 'data' })
  })
  
  module.exports = app
  
  
  // nuxt.config.js
  serverMiddleware: [
    { path: "/server-middleware", handler: "~/server-middleware/rest.js" },
  ],
  ```

  



## 라이프사이클

* Nuxt ServerInit

  

* Route Middleware

  

* validate()

  

* asyncData()

  

* fetch(context)

  



## Nuxt에서 Vue의 유용한 기능들

* Vue.$set



* functional template



## Nuxt의 기능

* client-only

  



