<template>
  <div id="app">
    <img src="./assets/logo.png">
    <div>
      <p>
        If vue-soonspace is successfully added to this project, you'll see an a 3D space below !
      </p>
      <vue-soonspace
        customId="vue-soonspace-customId"
        customClass="vue-soonspace-customClass"
        :customStyle="{
          margin: '0 auto',
          width: '500px',
          height: '300px'
        }"
        :option="{
          showInfo: true,
          showGrid: true,
          backgroundColor: 0x333300
        }"
        :globalSdk="false"
        @sceneReady="sceneReady"
        @modelClick="modelClick"
        @selectPosition="selectPosition"
      ></vue-soonspace>
    </div>
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'app',
  components: {
    HelloWorld
  },
  methods: {
    sceneReady(ssp) {
      console.log("soonspace: ", ssp);
    },
    modelClick(param) {
      console.log("soonspace modelClick", param);
    },
    selectPosition(position) {
      console.log("soonspace selectPosition", position);
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
