# :beginner: Vue #03

### Vue Component

**Vue Component(전역)**

* 전역 컴포넌트를 등록하려면 Vue.Component(tagName, options)를 사용
* 권장하는 컴포넌트 이름 : 케밥 케이스
* template 용어 중요하다.

```html
<div id="app">
  <my-global></my-global>
</div>

<div id="app2">
  <my-global></my-global>
  <my-global></my-global>
</div>

<script>
  //Vue.component('comp-name', {옵션})
  Vue.component('my-global', {
    template: '<h2>전역 컴포넌트</h2>',
  });

  const app = new Vue({
    el: '#app',
  });
  const app2 = new Vue({
    el: '#app2',
  });
</script>
```



**Vue Component(지역)**

* 모든 컴포넌트를 전역으로 등록할 필요 X
* 컴포넌트를 components 인스턴스 옵션으로 등록함으로써 다른 인스턴스/컴포넌트의 범위에서만 사용할 수 있는 컴포넌트 생성 가능

```html
<!-- 지역 컴포넌트 -->
<div id="app">
  <my-local></my-local>
  <my-local></my-local>
</div>

<!-- 인식할 수 없다. -->
<div id="app2">
  <my-local></my-local>
</div>

<script>
  const app = new Vue({
    el: '#app',
    components: {
      'my-local': {
          template: '<h2>지역컴포넌트</h2>',
      },
    },
  });

  const app2 = new Vue({
    el: '#app2',
  });
</script>
```



**Vue Component data**

* data는 반드시 함수여야 한다. 
* data 객체를 공유하는 문제를 막고 새로운 데이터 객체를 반환해서 해결한다. 

```html
<div id="app">
  <my-comp></my-comp>
</div>

<template id="myComponent">
  <div>
    <h2>{{msg}}</h2>
    <div>얍</div>
  </div>
  <!-- <div>얍</div> 이거 안돼 최상위 태그가 하나 있어야한다.  -->
</template>

<script>
  // 컴포넌트에서 객체형태로는 data를 사용하면 안된다. 
  // Vue.component('my-comp', {
  //   template: '#myComponent',
  //   data: {
  //     msg: "hello",
  //   },
  // });

  const app = new Vue({
    el: '#app',
  });

</script>
```

* 컴포넌트의 template은 반드시 하나의 최상위 태그로 둘러싸여 있어야한다. 

 

**Vue Component Template**

* DOM을 템플릿으로 사용할 때, Vue는 템플릿 콘텐츠만 가져올 수 있기 때문에 HTML이 작동하는 방식에 고유한 몇 가지 제한 사항이 적용됨
* 따라서 가능한 경우 항상 문자열 템플릿을 사용하는 것이 좋음

```html
...
<script>
  Vue.component('my-comp', {
    template: '#myComponent',
    data() {
      return {
        msg: 'hello',
      };
    },
  });
    
  const app = new Vue({
    el: '#app',
  });
</script>
```

```html
<div id="app">
  <count-view></count-view>
  <count-view></count-view>
</div>

<template id="count-view">
  <div>
    <h2>{{count}}</h2>
    <button @click="count++">클릭</button>
  </div>
</template>

<script>
  let data = {
    count: 0,
  };

  Vue.component('count-view', {
    template: '#count-view',
    data() {
      return data;
    },
  });
    
  const app = new Vue({
    el: '#app',
  });

</script>
```

```html
<div id="app">
  <count-view></count-view>
  <count-view></count-view>
</div>

<template id="count-view">
  <div>
    <h2>{{count}}</h2>
    <button @click="count++">클릭</button>
  </div>
</template>

<script>
  Vue.component('count-view', {
    template: '#count-view',
    data() {
      return{
        count: 0,
      };
    },
  });
    
  const app = new Vue({
    el: '#app',
  });
</script>
```





### Vue Component 통신

**Vue Component 통신**

* 컴포넌트는 부모-자식 관계에서 가장 일반적으로 함께 사용하기 위한 것(트리 구조)
* 부모는 자식에게 데이터를 전달 (Pass Props)
* 자식은 부모에게 일어난 일을 알림 (Emit Event)
* 부모와 자식이 명확하게 정의된 인터페이스를 통해 격리된 상태 유지
* props는 아래로, events는 위로



**부모 컴포넌트 > 자식 컴포넌트**

* 상위 컴포넌트에서 하위 컴포넌트로 데이터 전달
* 하위 컴포넌트는 상위 컴포넌트의 값을 직접 참조 불가능
* data와 마찬가지로 props 속성의 값을 template에서 사용 가능



**Dynamic Props**

* v-bind를 사용하여 부모의 데이터에 props를 동적으로 바인딩할 수 있음
* 데이터가 상위에서 업데이트 될 때마다 하위 데이터로도 전달

```html
<div id="app">
  <h2>부모컴포넌트</h2>
  <!-- 정적 props -->
  <child-view propsdata="정적인메시지"></child-view>

  <!-- 동적 props -->
  <input type="text" v-model="msg">
  <child-view :propsdata="msg"></child-view>
</div>

<template id="child-view">
  <div>
    <h3>자식컴포넌트</h3>
    <div>{{propsdata}}</div>
  </div>
</template>

<script>
  Vue.component('child-view', {
    template: '#child-view',
    props: ['propsdata'],
  });
    
  const app = new Vue({
    el: '#app',
    data() {
      return {
        msg: '',
      };
    },
  });
</script>
```



Dynamic Props 객체의 속성 전달

* 객체의 모든 속성을 props로 전달할 경우 인자없이 v-bind를 사용

단방향 데이터 흐름

* 모든 props는 하위 속성과 상위 속성 사이의 단방향 바인딩을 형성
* 상위 속성이 업데이트되면 하위로 흐르게 되지만 반대로는 안됨
* 하위 컴포넌트가 실수로 부모의 상태를 변경하여 앱의 데이터 흐름을 이해하기 어렵게 만드는 일 방지
* 상위 컴포넌트가 업데이트 될 때마다 하위 컴포넌트의 모든 prop들이 최신 값으로 갱신됨

```html
<div id="app">
  <h2>부모컴포</h2>
  <child-view area="속초" :msg="msg[parseInt(Math.random()*5)]"></child-view>
  <child-view area="니뽄" :msg="msg[parseInt(Math.random()*5)]"></child-view>
  <child-view area="수유" :msg="msg[parseInt(Math.random()*5)]"></child-view>
  <child-view area="부산" :msg="msg[parseInt(Math.random()*5)]"></child-view>
  <child-view area="통영" :msg="msg[parseInt(Math.random()*5)]"></child-view>
</div>

<template id="child-view">
  <div>
    <h3>{{area}} 지역</h3>
    <div>{{msg}}</div>
  </div>
</template>

<script>
  Vue.component('child-view', {
    template: '#child-view',
    props: ['area', 'msg'],
  });
    
  const app = new Vue({
    el: '#app',
    data() {
      return {
        msg: ['오향족발먹으러', '고양이를 보러', '경섭이가 놀러가서', '라면', '예비군'],
      };
    },
  });
</script>
```

```html
<div id="app">
  <h2>부모컴포</h2>
  <child-view 
  v-for="area in areas" 
  :area2="area"
  :msg="msg[parseInt(Math.random()*5)]"></child-view>
</div>

<template id="child-view">
  <div>
    <h3>{{area2}} 지역</h3>
    <div>{{msg}}</div>
  </div>
</template>

<script>
  Vue.component('child-view', {
    template: '#child-view',
    props: ['area2', 'msg'],
  });
    
  const app = new Vue({
    el: '#app',
    data() {
      return {
        areas: ['속초', '일본', '수유', '부산', '통영'],
        msg: ['오향족발먹으러', '고양이를 보러', '경섭이가 놀러가서','라면','예비군'],
      };
    },
  });
</script>
```

```html
<div id="app">
  <h2>부모컴포</h2>
  <child-view :user="person"></child-view>
</div>

<template id="child-view">
  <div>
    <h3>{{user.name}} 님의 신상명세서입니다. 킬러님</h3>
    <div>나이 : {{user.name}}세</div>
    <div>이메일 : {{user.email}}</div>
  </div>
</template>

<script>
  Vue.component('child-view', {
    template: '#child-view',
    props: ['user'],
  });
    
  const app = new Vue({
    el: '#app',
    data() {
      return {
        person: {
          name: '유지나',
          age: 20,
          email: 'ssafy.com',
        },
        
      };
    },
  });
</script>
```



**사용자 정의 이벤트**

* 컴포넌트 및 props와는 달리, 이벤트는 자동 대소문자 변환 제공하지 않음
* 대소문자를 혼용하는 대신에 emit할 정확한 이벤트 이름을 작성하는 것을 권장
* DOM 템플릿의 v-on 이벤트 리스너는 항상 자동으로 소문자 변환
* 따라서 이벤트 이름에는 kebab-case를 사용하는 것을 권장



* $on(eventName) : 이벤트 수신
* $emit(eventName) : 이벤트 발생, 추가 인자는 리스너의 콜백 함수로 전달
* 부모 컴포넌트는 자식 컴포넌트가 사용되는 템플릿에서 v-on을 사용하여 자식 컴포넌트가 보낸 이벤트를 청취

```html
<div id="app">
  <button @click="doAction">메세지전송</button>
</div>

<script>
  const app = new Vue({
    el: '#app',
    data() {
      return {};
    },
    methods: {
      doAction() {
        this.$emit('sendMsg', '안녕하세요...');
      },
    },
    created() {
      this.$on('sendMsg', (msg) => {
        alert(msg);
      });
    },
  });
</script>
```



**자식 컴포넌트 > 부모 컴포넌트**

* 이벤트 발생과 수신을 이용
* 자식 컴포넌트에서 부모 컴포넌트가 지정한 이벤트를 발생 ($emit)
* 부모 컴포넌트는 자식 컴포넌트가 발생한 이벤트를 수신(on)하여 데이터 처리

```html
<!-- 총투표수 -->
<!-- 코딩 / 알고리즘 -->
<div id="app">
  <h2>여러분의 선호도는~~</h2>
  <h3>총 투표수는 : {{total}}</h3>
  <child-view @add-count="addTotalCount" title="코딩"></child-view>
  <child-view @add-count="addTotalCount" title="알고리즘"></child-view>
</div>

<!-- 자식 컴포넌트를 정의해보자 -->
<template id="child-view">
  <div>
    <h2>{{title}}의 득표수는 {{count}}</h2>
    <button @click="addCount">투표</button>
  </div>
</template>

<script>
  Vue.component('child-view', {
    template: '#child-view',
    data() {
      return{
        count : 0,
      };
    },
    props: ['title'],
    methods: {
      addCount() {
        this.count += 1;
        this.$emit('add-count');
      },
    },
  });

  const app = new Vue({
    el: '#app',
    data() {
      return {
        total: 0,
      };
    },
    methods: {
      addTotalCount() {
        this.total += 1;
      },
    },
  });
</script>
```



**Module 연결**

```html
<div id="app">
  <header-nav></header-nav>
  <main-content></main-content>
  <footer-bar></footer-bar>
</div>

<script type="module">
  import HeaderNav from './component/HeaderNav.js';
  import MainContent from './component/MainContent.js';
  import FooterBar from './component/FooterBar.js';

  const app = new Vue({
    el: '#app',
    components : {
      HeaderNav,
      MainContent,
      FooterBar,
    },
  });
</script>
```

