## Vue에서 jwt에서 발급받은 token 값 관리
1. vuex store 에 저장후 vue-persistedstore로 관리.

> 위 방법을 사용할시 sessionStorage혹은 localStorge중에 사용하게 되는데  
sessionStorage의 경우 탭별로 로그인 상태 정보가 다르다. 따라서 적합하지 않다고 판단.

2. localStorage로 관리한다면?
> localStorage의 경우 영구적으로 값이 가져지기 때문에 클라이언트에서 가지고 있으면 서버에 계속해서 인증을 받을 수 있다.
하지만 토큰마다 수명이 있어서 수명을 다하면 다시 로그인을 해야한다.  
하지만 네이버의 경우 탭간 로그인 정보가 공유되면서도 브라우저 탭을 모두 닫고 즉 브라우저를 종료했다 재시작하면
로그인 정보가 풀려있다. 단순하게 localStorage에 저장하면 토큰이 유효하는 한 로그인 상태가 유지된다.


### 내 생각
따라서 브라우저가 종료될때 vue 에서 localStorage를 비워주는 코드를 작성한다.
하지만 okky.kr 같은 사이트의 경우 웹사이트를 종료해도 로그아웃이 되지 않는 것을 보아 상황별 요구사항에 맞게 하면 될 것 같다.
#### main.js
```js
import Vue from "vue";
import App from "./App.vue";

Vue.config.productionTip = false;

new Vue({
  beforeMount() {
    window.addEventListener("load", this.onLoad);
    window.addEventListener("beforeunload", this.onUnload);
  },
  beforeDestroy() {
    window.removeEventListener("load", this.onLoad);
    window.removeEventListener("beforeunload", this.onUnload);
  },
  methods: {
    onLoad(event) {
      window.localStorage.setItem("isMySessionActive", true);
    },
    onUnload(event) {
      window.localStorage.setItem("isMySessionActive", false);
    }
  },
  render: (h) => h(App)
}).$mount("#app");

```
#### App.vue
```js
<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" width="25%">
    <HelloWorld msg="Hello World!"/>
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld";

export default {
  name: "App",
  components: {
    HelloWorld
  },
  beforeMount() {
    window.addEventListener("load", this.onLoad);
    window.addEventListener("beforeunload", this.onUnload);
  },
  beforeDestroy() {
    window.removeEventListener("load", this.onLoad);
    window.removeEventListener("beforeunload", this.onUnload);
  },
  methods: {
    onLoad(event) {
      window.localStorage.setItem("isMySessionActive", true);
    },
    onUnload(event) {
      window.localStorage.setItem("isMySessionActive", false);
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

```
