# :beginner: Vue #05

### Vue CLI

**Vue CLI(Command Line Interface)**

* Vue.js 개발을 위한 표준 도구
* Vue.js에서 공식으로 제공하는 CLI
* 개발의 필수는 아니지만 개발의 편리성을 위해 필수처럼 사용
* Vue 프로젝트를 빠르게 구성할 수 있는 스캐폴딩을 제공
* Vue와 관련된 오픈 소스들의 대부분이 CLI를 통해 구성이 가능하도록 구현



**NodeJS**

https://nodejs.org/ko 여기서 설치하셈



**NPM(Node Package Manager)**

* Command에서 third party 모듈을 설치하고 관리하는 툴

* Vue CLI 검색 > 코드 복사 > cdm에서 복붙 > 설치 지이이이이잉



**@vue/cli 프로젝트 생성**

* vue create <project-name>
* 자신의 뷰 버전에 맞는 걸로 설치
* 프로젝트 실행 : npm run serve
* 실행 중지 : ctrl + c



**Vue Project 구조**

* **node_modules**

  node.js 환경의 여러 의존성 모듈

* **public**

  index.html : Vue 앱의 뼈대가 되는 파일 / 단일 html 파일

  favicon.ico : Vue 아이콘 

* **src/assets**

  webpack에 의해 빌드 된 정적 파일

* **src/component**

  하위 컴포넌트들이 위치

* **src/App.vue**

  최상위 컴포넌트

* **src/main.js**

  webpack이 빌드를 시작할 때 가장 먼저 불러오는 entry point

  실제 단일 파일에서 DOM과 data를 연결했던 것과 동일한 작업이 이루어지는 곳

* **babel.config.js**

  babel 관련 걸정이 작성된 파일

* **package-json**

  프로젝트의 종속성 목록과 지원되는 브라우저에 대한 구성 옵션

* **package-lock.json**

  node_modules에 설치되는 모듈과 관련된 모든 의존성을 설정/관리

  팀원 및 배포 환경에서 정확히 동일한 종속성을 설치하도록 보장

  사용할 패키지의 버전을 고정

  개발 과정간의 의존성 패키지 충돌 방지



그냥 외워 

npm install (master에서 가능)
