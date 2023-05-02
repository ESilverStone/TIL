# :beginner: Vue #01

### Vue Directive



**Directive**

* v-접두사가 있는 특수 속성
* 속성값은 단일 JavaScripte 표현식이 됨
* 역할은 표현식의 값이 변경될 때 사이드 이펙트를 반응적으로 DOM에 적용



**v-text**

* 엘리먼트의 textContent를 업데이트
* 일부를 갱신해야 한다면 {{}}를 사용해야함

```html
<div id="app">
  <!-- 보간법을 사용해보자. (콧수염 방식)-->
  <h2>{{msg}}</h2>
  <!-- v-html : 실제로 코드를 HTML로 해석해버린다. -->
  <h2 v-html="msg"></h2>
  <!-- v-text -->
  <h2 v-text="msg"></h2>
</div>
<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        msg: '<h2 style="color:red">이글은 빨간색입니다.</h2>',
      };
    },
  });
</script>
```

```html
<div id="app">
  <!-- javascript 표현식을 사용해보자. -->
  <div>{{num+1}}</div>
  <div>{{msg+'하하'}}</div>
  <div>{{msg.split("").reverse().join("")}}</div>
  <div>{{num > 10 ? num : num**2}}</div>
  <!-- 콧수염방식말고 똑같지만 v-directive -->
  <div v-text="msg + '이거된다'"></div>
</div>
<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        num: 7,
        msg: "안녕하세요 후후",
      };
    },
  });
</script>
```



**v-bind**

* HTML 요소의 속성에 Vue 상태 데이터를 값으로 할당
* Object 형태로 사용하면 value가 true인 key가 class 바인딩 값으로 할당
* 약어 가능 ex. v-bind:href  > :href

```html
<div id="app">
  <!-- v-bind -->
  <div id="idValue">v-bind연습1</div>
  <div v-bind:id="idValue">v-bind연습2</div>
  <div :id="idValue">v-bind연습3</div>

  <button v-bind:[key]="btnId">버튼</button>
  <button :[key]="btnId">버튼</button>
</div>
<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        idValue: "test-id",
        key: "class",
        btnId: "test-btn",
      };
    },
  });
</script>
```



**v-model**

* HTML form 요소의 input 엘리먼트 또는 컴포넌트에 양방향 바인딩 처리
* 수식어
  * .lazy : input 대신 change 이벤트 이후에 동기화
  * .number : 문자열을 숫자로 변경
  * .trim : 입력에 대한 trim 진행
* form 엘리먼트 초기 'value'와 'checked', 'selected' 속성을 무시함

```html
<div id="app">
  <!-- v-model : 중국어, 일본어, 한국어 .... 바로 업뎃이 안됨
       해결방법 : input 이벤트를 이용해서 처리를 해야함...
  -->
  <!-- value 속성 / checked / selected 무시됨. -->
  <input type="text" value="초기값" v-model="msg" />
  <div>{{msg}}</div>
</div>
<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        msg: "초기값이라네",
      };
    },
  });
</script>
```





**v-show**

* 조건에 따라 엘리먼트를 화면에 표시
* 항상 렌더링되고 DOM에 남아있음
* 단순히 엘리먼트에 display CSS속성을 토글하는 것
* 조건이 바뀌면 트랜지션 호출

```html
<div id="app">
  <!-- v-show : 항상 렌더링 되어있음. CSS display 속성 -->
  <h1 v-show="ok">하위</h1>
  <input type="text" v-model="msg" />
  <button v-show="msg.trim().length !== 0">검색</button>
</div>
<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        ok: false,
        msg: "hello",
      };
    },
  });
</script>
```



**v-if, v-else-if, v-else**

* 표현식 값의 참 거짓을 기반으로 엘리먼트를 조건부 렌더링 함
* 엘리먼트 및 포함된 디렉티브/ 컴포넌트는 토글하는 동안 삭제되고 다시 작성됨
* `<template>` 엘리먼트를 이용하여 v-if 사용가능, 최종 결과에는 `<template>` 엘리먼트는 포함 X

```html
<div id="app">
  <!-- v-if/else -->
  <h2>범퍼카</h2>
  나이 : <input type="number" v-model="age" /> <br />
  <div>
    요금 :
    <span v-if="age < 10">10만원</span>
    <span v-else-if="age < 20">50만원</span>
    <span v-else-if="age == 27">공쨔</span>
    <span v-else>만원</span>
  </div>
</div>
<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        age: 10,
      };
    },
  });
</script>
```



**v-for**

* 원본 데이터를 기반으로 엘리먼트 또는 템플릿 블록을 여러 번 렌더링
* 디렉티브의 값은 반복되는 현재 엘리먼트에 대한 별칭을 제공하기 위해 alias in expression을 사용
* v-for의 기본 동작은 엘리먼트를 이동하지 않고 그 자리에서 패치 시도, 강제로 엘리먼트의 순서를 바꾸려면 특수 속성 key 설정
* v-if와 함께 사용하는 경우, v-for는 v-if보다 높은 우선순위를 갖는다. 따라서, 되도록이면 같이 작성하는 것은 피하기

```html
<!-- v-for -->
<div>
  <h2>숫자를 돌려보자</h2>
  <span v-for="i in 10">{{i}}</span>
</div>
<div>
  <h2>이름을 돌려보자</h2>
  <ul>
    <li v-for="name in names">{{name}}</li>
  </ul>
</div>
<div>
  <h2>이름을 돌려보자2</h2>
  <ul>
    <!-- 순서중요 -->
    <li v-for="(name, index) in names">{{index+1}} : {{name}}</li>
  </ul>
</div>
<div>
  <h2>객체 배열을 돌려보자</h2>
  <ul>
    <li v-for="human in humans">{{human.name}} : {{human["age"]}}세</li>
  </ul>
</div>
</div>
<script>
const app = new Vue({
  el: "#app",
  data() {
    return {
      names: ["최주호", "이세훈", "한장민", "곽형석"],
      humans: [
        {
          name: "허재웅",
          age: 333,
        },
        {
          name: "곽형석",
          age: 60,
        },
        {
          name: "최규호",
          age: 12,
        },
        {
          name: "이세훈",
          age: 2,
        },
      ],
    };
  },
});
</script>
```



**v-on** 

* 엘리먼트에 이벤트 리스너를 연결
* 이벤트 유형은 전달인자로 표시
* 약어 제공 @ ex. v-on:click  > @click

```html
<div id="app">
  <!-- v-on -->
  <h2>{{count}}</h2>
  <button v-on:click="count+=1">버튼</button>
  <button v-on:click="plus">버튼</button>
  <button @click="plus">버튼</button>
</div>
<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        count: 1,
      };
    },
    methods: {
      plus() {
        this.count++;
        console.log("메소드 실행");
      },
    },
  });
</script>
```



**v-cloak**

* Vue Instance가 준비될 때까지 Mustache 바인딩을 숨기는데 사용
* [v-clock] {display : none} 과 같은 CSS 규칙과 함께 사용
* Vue Instance가 준비되면 v-cloak는 제거됨

```html
<div id="app">
  <!-- v-cloak -->
  <h1>1{{msg}}</h1>
  <h1 v-cloak>2{{msg}}</h1>
</div>
<script>
  setTimeout(function () {
    const app = new Vue({
      el: "#app",
      data() {
        return {
          msg: "짝수이다.",
        };
      },
    });
  }, 3000);
</script>
```





### Vue Instance Option

**vue Method**

* Vue Instance는 생성 관련된 데이터 및 메서드의 정의 가능
* method 안에 data를 this.데이터이름으로 접근

```html
<div id="app">
  <button @click="greet">인사</button>
</div>
<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        msg: "안뇽하세요~~",
        name: "OPP",
      };
    },
    methods: {
      greet() {
        // this : 뷰인스턴스를 가리킨다.
        alert(`${this.msg} ${this.name}님`);
      },
    },
  });
</script>
```



**Vue Filters**

* Vue는 텍스트 형식화를 적용할 수 있는 필터를 지원함
* filter를 이용하여 표현식에 새로운 결과 형식을 적용
* {{Mustache}} 구문(이중 중괄호) 또는 v-bind 속성에서 사용이 가능
* 자바스크립트 표현식 마지막에 "|" 심볼과 함께 추가되어야 함
* 필터는 체이닝이 가능

```html
<div id="app">
  <div>
    <input type="text" v-model="msg" />
  </div>
  <div>
    <!-- 적용하고 싶은 필더가 있다면 | 기호를 이용하자 -->
    <h3>{{msg | count1}}</h3>
    <!-- 필터는 체이닝도 가넝 -->
    <h3>{{msg | count2('입력바람') | count3}}</h3>
  </div>
</div>
<script>
  // 전역필터
  Vue.filter("count1", (val) => {
    if (val.length === 0) return;
    return `${val} : ${val.length}글자`;
  });

  const app = new Vue({
    el: "#app",
    data() {
      return {
        msg: "",
      };
    },
    filters: {
      //지역필터
      count2(val, alternative) {
        if (val.length === 0) return alternative;
        return `${val} : ${val.length}`;
      },
      count3(val) {
        return `${val} 글자입니다.`;
      },
    },
  });
</script>
```





**Vue computed**

* 특정 데이터의 변경사항을 실시간으로 처리할 수 있음
* 캐싱을 이용하여 데이터의 변경이 없을 경우 캐싱된 데이터를 반환
* Setter와 Getter를 직접 지정할 수 있음
* 작성은 method 형태로 정의하지만 Vue에서 프록시 처리하여 property처럼 사용
* 화살표 함수를 사용하면 안됨

```html
<div id="app">
  <!-- Computed를 이용하여 문자열 뒤집기 -->
  <div>
    <input type="text" v-model="msg" />
    <p>원본 : {{msg}}</p>
    <p>역순1 : {{reversedMsg}}</p>
    <p>역순2 : {{reversedMsg}}</p>
    <p>역순3 : {{reversedMsg}}</p>
    <p>역순4 : {{reversedMsg}}</p>
  </div>
</div>
<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        msg: "안녕하세요",
      };
    },
    // 딱 한번만 계산하고 변화가 없다면 해당값을 계속사용한다.
    computed: {
      reversedMsg() {
        console.log("거꾸로 뒤집기");
        return this.msg.split("").reverse().join("");
      },
    },
  });
</script>
```





**Vue computed & methods**

* computed 속성 대신 methods에 함수 정의하여 사용가능
* computed 속성은 종속 대상을 계산하여 저장해 놓는다. 즉, 종속된 대상이 변경되지 않는 한 computed에 작성된 함수를 여러 번 호출해도 계산을 다시 하지 않고, 계산되어 있는 결과를 반환
* methods를 호출하면 렌더링을 다시 할 때마다 항상 함수를 실행

```html
<div id="app">
  <!-- Method를 이용하여 문자열 뒤집기 -->
  <div>
    <input type="text" v-model="msg" />
    <p>원본 : {{msg}}</p>
    <p>역순1 : {{reversedMsg()}}</p>
    <p>역순2 : {{reversedMsg()}}</p>
    <p>역순3 : {{reversedMsg()}}</p>
    <p>역순4 : {{reversedMsg()}}</p>
  </div>
</div>
<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        msg: "안녕하세요",
      };
    },
    methods: {
      reversedMsg() {
        console.log("거꾸로 뒤집기");
        return this.msg.split("").reverse().join("");
      },
    },
  });
</script>
```



**Vue watch** 

* Vue Instance의 특정 property가 변경될 때 실행할 callback 함수 설정
* 데이터를 감시
* 화살표 함수 사용 X

```html
<div id="app">
  <div>
    <input type="text" v-model="msg" /> <br />
    <p>원본 : {{msg}}</p>
    <p>역순 : {{reversedMsg}}</p>
  </div>
</div>
<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        msg: "hello",
        reversedMsg: "",
      };
    },
    watch: {
      msg(newVal) {
        this.reversedMsg = newVal.split("").reverse().join("");
      },
    },
  });
</script>
```



**Vue computed & watch**

* **computed**

  특정 데이터를 직접적으로 사용/가공하여 다른 값으로 만들 때 사용

  계산해야하는 목표 데이터를 정의하는 방식으로 SW 공학에서 이야기하는 '선언형 프로그래밍' 방식

* **watch**

  특정 데이터의 변화 상황에서 맞추어 다른 data 등이 바뀌어야 할 때 주로 사용

  감시할 데이터를 지정하고 그 데이터가 바뀌면 이런 함수를 실행하라 라는 방식으로 SW 공학에서 이야기하는 '명령형 프로그래밍' 방식

