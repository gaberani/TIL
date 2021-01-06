# NUTX_TIL

## 프로젝트 디렉토리 구조

* assets
  * css, image, font와 같은 리소스를 모아둔다.
* components
  * 사용될 컴포넌트들을 모아두며 해당 경로에 있는 컴포넌트들은 Nuxt.js의 비동기 데이터 함수인  `asyncData`나 `fetch`를 사용할 수 없다.
* content
  * `@nuxt/content` 모듈을 사용하여 애플리케이션을 확장할 수 있다. Markdown, JSON, XML, CSV와 같은 파일을 가져오고 관리할 수 있다.
* layouts
  * 애플리케이션 전체에 대한 레이아웃을 포함한다. 기본으로 default.vue가 생성되고 상황에 맞게 레이아웃을 생성하고 이에 맞게 v-if와 같은 조건을 걸어 보여줄 여부를 결정할 수도 있다.
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



## 라이프사이클





## Nuxt에서 Vue의 유용한 기능들

* Vue.$set



* functional template





