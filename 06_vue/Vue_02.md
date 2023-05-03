# :beginner: Vue #02

### Vue Event



### Vue Life Cycle

**Instance Life Cycle Hooks**

* 각 Vue 인스턴스는 생성될 때 일련의 초기화 단계를 걸친다. 
  * 데이터 관찰 설정이 필요한 경우
  * 템플릿을 컴파일 하는 경우
  * 인스턴스를 DOM에 마운트 하는 경우
  * 데이터가 변경되어 DOM을 업데이트 하는 경우 등
* 그 과정에서 사용자 정의 로직을 실행할 수 있는 Life Cycle Hooks도 호출
* 크게 4개의 파트 (생성, 부착,갱신,소멸)로 나뉜다. 
* 모든 Life Cycle Hooks은 자동으로 this 컨텍스트를 인스턴스에 바인딩하므로 데이터, 계산된 속성 및 메소드에 접근할 수 있다. 



![image](https://user-images.githubusercontent.com/68459918/235822660-d71b81c8-9940-4eca-85ff-3de76e7967c4.png)



| Life Cycle Hooks  | 설명                                                         |
| :---------------: | :----------------------------------------------------------- |
| **beforeCreate**  | 인스턴스가 방금 초기화 된 후 데이터 관찰 및 이벤트 / 감시자 설정 전에 동기적으로 호출 |
|    **created**    | 인스턴스가 작성된 후 동기적으로 호출. <br />데이터 처리, 계산된 속성, 메서드, 감시/이벤트 콜백 등과 같은 옵션 처리 완료<br />마운트가 시작되지 않았으므로 DOM 요소 접근 X |
|  **beforeMount**  | 마운트가 시작되기 전에 호출                                  |
|    **mounted**    | 지정된 엘리먼트에 Vue 인스턴스 데이터가 마운트 된 후에 호출  |
| **beforeUpdate**  | 데이터 변경될 때 virtual DOM이 렌더링, 패치 되기 전에 호출   |
|    **updated**    | Vue에서 관리되는 데이터가 변경되어 DOM이 업데이트 된 상태    |
| **beforeDestory** | Vue 인스턴스가 제거되기 전에 호출                            |
|   **destoryed**   | Vue 인스턴스가 제거된 후에 호출                              |

```html
<!DOCTYPE html>
<html lang="ko">
  <head>
    <meta charset="UTF-8" />
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <title>Vue</title>
  </head>
  <body>
    <div id="app">
      <div>{{count}}</div>
      <button @click="count++">증가</button>
    </div>

    <script>
      const app = new Vue({
        el: "#app",
        data() {
          return {
            count: 0,
          };
        },
        // beforeCreate() {
        //   console.log('beforeCreate count : ' + this.count);
        // },
        // created() {
        //   console.log('created count : ' + this.count);
        //   console.log('created에 연결된 DOM : ' + this.$el);
        // },
        // beforeMount() {
        //   console.log('beforeMount count : ' + this.count);
        //   console.log('beforeMount에 연결된 DOM : ' + this.$el);
        // },
        // mounted() {
        //   console.log('mounted count : ' + this.count);
        //   console.log('mounted에 연결된 DOM : ' + this.$el);
        // },
        // beforeUpdate() {
        //   console.log('beforUpdate 호출');
        // },
        // updated() {
        //   console.log('updated 호출');
        //   console.log('updated count : ' + this.count);
        // },
        // 파괴는 따로 하지는 않겠다.
      });
    </script>
  </body>
</html>
```



### Vue Event Handling

**Vue Listening to Event**

* v-on 디렉티브를 사용하여 DOM 이벤트를 듣고 트리거 될 때 JavaScript를 실행할 수 있음

```html
<div id="app">
  <button v-on:click="count+=1">증가1</button>
  <button @click="count+=1">증가2</button>
  <div>{{count}}</div>
</div>

<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        count: 0,
      };
    },
  });
</script>
```



**Vue Method Event Handlers**

* 많은 이벤트 핸들러의 로직은 복잡하여 v-on 속성 값으로 작성하기는 간단하지 않음
* 때문에 v-on이 호출하고자 하는 method의 이름을 받는 이유

```html
<div id="app">
  <button @click="greet">인사</button>
</div>

<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        name: "ESS",
      };
    },
    methods: {
        // event라고 하는 것은 넘기지 않아도 괜찮다
        greet: function(event) {
            alert(`${this.name}님 하이요`);
            event.target.innerText = "얍";
            console.log(event);
        },
    },
  });
</script>
```



**Vue Method in Inline Handlers**

* 메소드 이름을 직접 바인딩하는 대신 인라인 JavaScript 구문에 메서드를 사용할 수도 있음
* 때로 인라인 명령문 핸들러에서 원본 DOM 이벤트에 엑세스 해야할 수도 있음. $event 변수를 사용해 메소드에 전달할 수 있음

```html
<div id="app">
    <!-- 이벤트 메소드 인자 넘겨보기 -->
  <button @click="greet('봉쥬르')">인사</button>
</div>

<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        name: "ESS",
      };
    },
    methods: {
        // event라고 하는 것은 넘기지 않아도 괜찮다
        greet: function(msg) {
            alert(`${this.name}님 ${msg}`);
            event.target.innerText = msg;
            console.log(event);
        },
    },
  });
</script>
```



**Vue Event Modifiers**

* 이벤트 핸들러 내부에서 event.preventDefault() 등을 호출하는 것은 보편적인 일이다. 
* 메소드 내에서 쉽게 작업을 할 수 있지만, methods는 DOM의 이벤트를 처리하는 것보다 data 처리를 위한 로직만 작업하는 것이 좋음
* v-on 이벤트에 이벤트 수식어를 제공(.으로 표시된 접미사)
* 체이닝 가능
* .stop : event.stopPropagation() 호출 - 클릭 이벤트 전파 X
* .prevent : event.preventDefault() 호출 - 제출 이벤트가 페이지를 다시 로드 X
* .capture : 캡처 모드에서 이벤트 리스너를 추가함
* .self : 이벤트가 이 엘리먼트에서 전달된 경우에만 처리 됨
* .once : 단 한번만 처리
* .passive : DOM 이벤트를 {passive : true}와 연결함

```html
<div id="app">
    <!-- 이벤트 수식어 -->
    <h2>페이지 이동</h2>
    <a href="test03.html" @click="sendMsg">페이지 이동막기1</a><br />
    <a href="test03.html" @click="sendMsg2">페이지 이동막기2</a><br />
    <a href="test03.html" @click.prevent="sendMsg">페이지 이동막기3</a>
</div>

<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        name: "ESS",
      };
    },
    methods: {
        sendMsg() {
            alert("저놈 막아!");
        },
        sendMsg2() {
            alert("막으라고 좀!");
            event.preventDefault();
        },    
    },
  });
</script>
```



**Vue Key Modifier**

* Vue는 키 이벤트를 수신할 때 v-on에 대한 키 수식어를 추가할 수 있음
* .enter (.13)
* .tab
* .delate
* .esc
* .space
* .up / .down / .left / .right
* 전역 config.keyCodes 객체를 통해 사용자 지정 키 수식어 별칭 지정 가능



### Vue Bindings

**ref, $refs**

* $refs : ref 속성이 등록된 자식 컴포넌트와 DOM 엘리먼트 객체. 템플릿이나 계산된 속성에서 사용 X
* ref : 엘리먼트 또는 자식 컴포넌트에 대한 참조를 등록하는데 사용

```html
<div id="app">
    <!-- refs, ref -->
    <h2>이름 등록</h2>
    <input type="text" v-model="name" placeholder="이름 입력1" ref="name" />
    <button @click="check">중복췍</button><br/>
    <button @click="send">전송</button><br/>
</div>

<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        name: "ESS",
      };
    },
    methods: {
        send() {
            alert(`${this.name} 등록`);
        },  
        check() {
            // 만약에 통과하지 못했다면 다시 input 태그에 바로 작성이 가능하도록 활성화가 되면 좋겠다.
            // this.name : 요걸 이용해서 우리의 back 서버로 요청을 날리겠다.
            // 입력을 하지 않았을 때만 판단 / 실제로는 서버로 요청을 날려서 그 여부 또한 확인해주는게 좋음
            if(this.name.length === 0){
                alert("아이디를 입력해주세요");
                this.$refs.name.focus();
                console.log(this.$refs);
                console.log(this.$refs.name);
                return;
            }
            alert("아이디 중복췍 성공");
        },  
    },
  });
</script>
```



**Class Bindings**

* 데이터 바인딩은 엘리먼트의 클래스 목록과 인라인 스타일을 조작하기 위해 일반적으로 사용됨
* v-bind를 사용하여 처리 가능
* 문자열 이외에 객체 또는 배열을 이용할 수 있음

```html
<style>
    div {
        width: 200px;
        height: 200px;
        border: 1px solid black;
    }
    .dark {
        background-color: black;
        color: white;
    }
</style>

<div id="app">
    <div v-bind:class="myclass">얍</div>
    <div :class="{dark: isDark}">얍</div>
    <button @click="toggle">다크모드전환</button>
</div>

<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        myclass: {
            dark: true,
        },
        isDark: false,
      };
    },
    methods: {
        toggle() {
            this.isDark = !this.isDark;
        },
    },
  });
</script>
```



**Form Input Bindings**

* v-model 디렉티브를 사용하여 form input과 textarea 엘리먼트에 양방향 데이터 바인딩을 생성할 수 있음
* text와 textarea : value, input 이벤트 사용
* checkbox, radio : checked, change 이벤트 사용
* select : value, change 이벤트 사용
* v-model은 모든 form 엘리먼트의 초기 value와 checked 그리고 selected 속성을 무시함

**text, textarea**

```html
<div id="app">
    <input type="text" v-model="name" placeholder="이름 입력" /><br/>
    <textarea v-model="msg"></textarea><br/>
    <!-- <textarea>{{msg}}</textarea><br/> 요런식으론 작동하지 않는다-->

    <div>{{name}}님에게 보내는 메시지는 {{msg}}와 같습니다.</div>
</div>

<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        name: "",
        msg:"",
      };
    },
  });
</script>
```

**checkbox**

```html
<div id="app">
    <div>
        이메일 수신 동의?
        <input type="checkbox" v-model="email">
    </div>
    <div>
        로봇? 삐-빅
        <input type="checkbox" v-model="robot" true-value="Y" false-value="N"/>
    </div>
</div>

<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        email: false,
      };
    }, 
  });
</script>
```

```html
<div id="app"> 
    <div>
        <h2>내가 가고 싶은 여행지~~ 체크해주세요</h2>
        <input type="checkbox" id="busan" v-model="checkAreas" value="부산">
        <label for="busan">부산</label>
        <input type="checkbox" id="japan" v-model="checkAreas" value="일본">
        <label for="japan">제페니즈</label>

        <p>여기어때~ 죠기어때~ {{checkAreas}}어때</p>
    </div>
</div>

<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        checkAreas: [],
      };
    },
  });
</script>
```

**radio**

```html
<div id="app"> 
    <div>
        <h2>현재 당신이 속해있는 캠퍼스를 고르세요</h2>
        <input type="radio" id="seoul" v-model="campus" value="seoul">
        <label for="seoul">서울</label>

        <input type="radio" id="daejeon" v-model="campus" value="daejeon">
        <label for="daejeon">대전</label>

        <input type="radio" id="buk" v-model="campus" value="buk">
        <label for="buk">부울경</label>

        <input type="radio" id="gumi" v-model="campus" value="gumi">
        <label for="gumi">구미가 싸악</label>

        <input type="radio" id="gwangju" v-model="campus" value="gwangju">
        <label for="gwangju">광쥬르</label>

        <p>나의 캠퍼스는 {{campus}}</p>

    </div>
</div>

<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        campus: "",
      };
    }, 
  });
</script>
```

**select(단일)**

```html
<div id="app"> 
    <div>
        <h2>현재 당신이 속해있는 캠퍼스를 고르세요</h2>
        <select v-model="campus">
            <option value="" disabled>선택하세요~</option>
            <option value="seoul" >서울</option>
            <option value="daejeon" >대전</option>
            <option value="buk" >부울경</option>
            <option value="gumi" >구미</option>
            <option value="gwangju" >광주</option>
        </select>

        <div v-if="campus">
            <p>나의 캠퍼스는 {{campus}}</p>
        </div>
    </div>
</div>

<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        campus: "",
      };
    }, 
  });
</script>
```

**select(다중)**

```html
<div id="app"> 
    <div>
        <h2>지망하는 캠퍼스를 골라보세요</h2>
        <select v-model="campus" multiple>
            <option value="" disabled>선택하세요~</option>
            <option value="seoul" >서울</option>
            <option value="daejeon" >대전</option>
            <option value="buk" >부울경</option>
            <option value="gumi" >구미</option>
            <option value="gwangju" >광주</option>
        </select>

        <div v-if="campus">
            <p>나의 캠퍼스는 {{campus}}</p>
        </div>
    </div>
</div>

<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        campus: [],
      };
    }, 
  });
</script>
```

```html
<div id="app"> 
    <div>
        <h2>지망하는 캠퍼스를 골라보세요</h2>
        <select v-model="campus" name="campus">
           <option v-for="area in areas" :value="area.value">{{area.name}}</option>
        </select>

        <div v-if="campus">
            <p>나의 캠퍼스는 {{campus}}</p>
        </div>
    </div>
</div>

<script>
  const app = new Vue({
    el: "#app",
    data() {
      return {
        campus: "",
        areas: [
            {
                name: '서울',
                value: 'seoul',
            },
            {
                name: '대전',
                value: 'daejeon',
            },
            {
                name: '부울경',
                value: 'buk',
            },
            {
                name: '구미',
                value: 'gumi',
            },
            {
                name: '광주',
                value: 'gwangju',
            },
        ],
      };
    }, 
  });
</script>
```

