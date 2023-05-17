# :beginner: Vue #07

### 

### **Vue Style Guide**

**Priority A : Essential (필수)**

**컴포넌트 이름에 합성어 사용**

* root 컴포넌트인 App과 transition, component 등 Vue에서 제공되는 빌트인 컴포넌트를 제외하고 컴포넌트의 이름은 항상 합성어를 사용해야 한다. 

* 모든 HTML 요소의 이름은 한 단어이기 때문에 합성어를 사용하는 것은 기존 그리고 향후 HTML 요소와의 충돌을 방지해줍니다.



**컴포넌트 데이터**

* 컴포넌트의 data는 반드시 함수여야 합니다
* 컴포넌트 (new Vue를 제외한 모든 곳)의 data 프로퍼티의 값은 반드시 객체를 반환하는 함수여야함



**Props 정의**

* Prop은 가능한 상세하게 정의되어야 한다.
* 적어도 타입은 명시되도록 가능한 상세하게 정의



**v-for에 key 지정**

* 서브트리의 내부 컴포넌트 상태를 유지하기 위해 v-for는 항상 key와 함께 요구된다. 



**v-if와 v-for를 동시에 사용하지 말 것**

* 리스트 목록을 필터링하기 위해서 사용

  이 경우 users를 새로운 computed 속성으로 필터링 된 목록으로 대체할 것

* 숨기기 위해 리스트의 랜더링을 피할 때 사용

  이 경우 v-if를 컨테이너 엘리먼트로 옮기기



**컴포넌트 스타일 스코프**

* <sttle> -> <style scoped> (적용 범위의 차이)



**Private 속성 이름**

* 플러그인, mixin 등에서 커스텀 사용자 private 프로퍼티에는 항상 접두사 $_를 사용하라
* 다른 사람의 코드와 충돌을 피하려면 named scope를 포함하라



**Priority B : Strongly Recommended (매우 추천)**

**컴포넌트 파일**

* 다른 파일을 만들어서 사용해라

![image](https://github.com/ESilverStone/TIL/assets/68459918/155f14ab-8fdd-45da-a9fb-90ba88fb9d06)



**싱글 파일 컴포넌트 이름 규칙 지정**

* 파스칼이나 케밥 케이스 사용



**기본 컴포넌트 이름**

* 합성어 사용



**단일 인스턴스 컴포넌트 이름**

* 활성 인스턴스가 하나만 있어야 하는 구성 요소는 The 접두사로 시작



**강한 연관성을 가진 컴포넌트 이름**

* 파일이름을 최대한 세부적으로 지을 것

![image](https://github.com/ESilverStone/TIL/assets/68459918/b5ea84d3-d3c0-4534-b598-7c54b7a5430f)



그외 

https://v2.vuejs.org/v2/style-guide/





### AJAX(Asynchronous JavaScript and XML)

* 비동기식 JavaScript와 XML
* 서버와 통신을 하기 위해 XMLHttpRequest 객체를 활용
* JSON, XML, HTML 그리고 일반 텍스트 형식 등을 포함한 다양한 포맷을 주고 받을 수 있음
* 페이지 전체를 '새로고침'하지 않고서도 수행되는 '비동기성'



### Axios

* 브라우저와 node.js.에서 사용할 수 있는 Promise 기반 HTTP 클라이언트 라이브러리
* Vue에서 권고하는 HTTP 통신 라이브러리
* 설치 : npm install axios
