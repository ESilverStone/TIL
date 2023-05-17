# :beginner: Vue #07

### **Vuex**

**Vuex**

* Vue.js 애플리케이션에 대한 상태관리 패턴 + 라이브러리
* 애플리케이션 모든 컴포넌트들의 중앙 저장소 역할
* 부모 자식 단계가 많이 복잡해 진다면 데이터의 전달하는 부분이 매우 복잡해ㅐ 짐
* 애플리케이션이 여러 구성 요소로 구성되고 더 커지는 경우 데이터를 공유하는 문제 발생
* devtools 확장 프로그램과 통합되어 고급 기능 제공

https://v3.vuex.vuejs.org/kr/



**비 부모-자식간 통신**

* 두 컴포넌트가 통신할 필요가 있지만 서로 부모/자식이 아닐 수도 있음
* 비어 있는 Vue Instance 객체를 Event Bus로 사용



**상태 관리 패턴**

- **상태** 는 앱을 작동하는 원본 소스 입니다.
- **뷰** 는 **상태의** 선언적 매핑입니다.
- **액션** 은 **뷰** 에서 사용자 입력에 대해 반응적으로 상태를 바꾸는 방법입니다.

```vue
new Vue({
  // 상태
  data () {
    return {
      count: 0
    }
  },
  // 뷰
  template: `
    <div>{{ count }}</div>
  `,
  // 액션
  methods: {
    increment () {
      this.count++
    }
  }
})
```



**Vuex 핵심 컨셉**

![vuex](https://v3.vuex.vuejs.org/vuex.png)

**Vuex 저장소 개념**

* **State** : 단일 상태 트리 사용, 애플리케이션 마다 하나의 저장소를 관리(data)
* **Getters** : Vue Instance의 computed와 같은 역할, State를 기반으로 계산(computed)
* **Mutations** : State의 상태를 변경하는 유일한 방법(methods)
* **Actions** : 상태를 변이 시키는 대신 액션으로 변이(Mutations)에 대한 커밋 처리(비동기 methods)

https://v3.vuex.vuejs.org/kr/guide/ 그냥 이거 봐





