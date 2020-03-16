# vue-soonspace

## 安装
```bash
npm install vue-soonspace soonspacejs -save
# or
yarn add vue-soonspace soonspacejs -save
```

> 安装 `vue-soonspace` 插件时，要同时安装 [soonspacejs](http://www.xwbuilders.com:9018/soonspacejs/Docs/)，但是注册 **前者** 组件时不必手动引入**后者**，内部自动引入。这样做是为了保证 **后者** 版本最新，不受版本依赖限制。
>

## 使用方式

### main.js

```js
import Vue from 'vue'
import App from './App.vue'
import VueSoonspace from 'vue-soonspace'

Vue.config.productionTip = false

Vue.use(VueSoonspace)

new Vue({
  render: h => h(App),
}).$mount('#app')
```

### App.vue
```vue
<template>
   <vue-soonspace
      customId="selfId"
      customClass="selfClass"
      :customStyle="{
        width: '50vw',
        height: '50vh',
        position: 'fixed',
        top: '0',
        left: '0'
      }"
      :option="{
        showInfo: true,
        backgroundColor: 0x333300
      }"
      :globalSdk="true"
      @sceneReady="sceneReady"
      @modelClick="modelClick"
      @selectPosition="selectPosition"
    />
</template>

<script>
export default {
  name: "app",
  methods: {
    sceneReady(ssp) {
      console.log('sceneReady', ssp);
      console.log('sceneReady', this.$ssp);

      this.$ssp.createGround({
        imgUrl: "https://unpkg.com/soonspacejs-demo-model/img/ground/001.jpg",
        width: 500,
        height: 300,
        position: { x: 0, y: 0, z: 0 }
      });

      this.$ssp.loadJson({
        id: 'car01',
        url: './hmCar.json'
      })
    },
    modelClick(param) {
      console.log("modelClick", param);
    },
    selectPosition(position) {
      console.log("selectPosition", position);
    }
  }
};
</script>

<style>
html,
body,
#app {
  margin: 0;
  width: 100%;
  height: 100%;
}
</style>

```

## 属性

### customId

  自定义 `id`

  - **类型：** string
  - **默认值：** `soonSpaceView${idIndex++}`

> `idIndex` 初始为 `0`

### customClass

  自定义类名

  - **类型：** string | object | array
  - **默认值：** `null`

### customStyle

  自定义样式

  - **类型：** object
  - **默认值：** `{ position: "relative", width: "100%", height: "100%" }`

### option

  `soonspace` [配置项]([http://www.xwbuilders.com:9018/soonspacejs/Docs/guide/start.html#%E9%85%8D%E7%BD%AE](http://www.xwbuilders.com:9018/soonspacejs/Docs/guide/start.html#配置))

  - **类型：** object
  - **默认值：** `{ showInfo: false }`

### globalSdk

  `soonspace` 实例是否声明为全局变量

  - **类型：** boolean
  - **默认值：** `true`

> `globalSdk` 为 `true` 时，`soonspace` 被实例化后绑定在 `Vue` 的原型 `$ssp` 上。
> 若 `globalSdk` 为 `false` 时，`soonspace` 被实例化后可通过方法 `sceneReady` 的参数接受。
>
> **--- 划重点 ---**
> <br>
> 在自行存储 `soonspace` 实例时，建议像上述一样绑定到 `Vue` 的原型上，**不要存储在组件的 `data` 内、或是`Vuex` 容器内**，否则 `soonspace` 实例下的全部空间实体对象都会被 `Vue` 数据劫持，造成页面 **卡死或奔溃**。

## 方法

### sceneReady

  场景准备完成时触发函数。

#### 回调参数

##### ssp 

  `soonspace` 实例

<br>
<br>

> 以下方法全部为 **soonspace 空间交互事件** 在 `vue-soonspace` 组件内的事件传递，方法名与回调参数完全一致。

  [soonspace 空间交互事件]([http://www.xwbuilders.com:9018/soonspacejs/Docs/guide/start.html#%E4%BA%A4%E4%BA%92%E4%BA%8B%E4%BB%B6](http://www.xwbuilders.com:9018/soonspacejs/Docs/guide/start.html#交互事件))
### modelClick
### modelDblClick
### modelHover
### poiClick
### poiDblClick
### poiHover
### selectPosition

## 更新记录

### 0.3.7

2020.3.15

- 首次完整测试、完成文档发布

## 源代码

> 如果你发现插件有 bug 或任何原因不想使用该插件，可以参考或在该插件的源代码基础上自行封装一个组件来满足需求。
> 以下是 `vue-soonspace` 的核心源代码。

```vue
<template>
  <div :id="id" :class="customClass" :style="assignStyle"></div>
</template>

<script>
import SoonSpace from "soonspacejs";

let idIndex = 0;

export default {
  name: "vue-soonspace",
  props: {
    customId: String,
    customClass: String | Array | Object,
    customStyle: Object,
    option: Object,
    globalSdk: {
      type: Boolean,
      default: true
    }
  },
  data() {
    return {
      defaultStyle: {
        position: "relative",
        width: "100%",
        height: "100%"
      }
    };
  },
  computed: {
    id: function() {
      return this.customId || `soonSpaceView${idIndex++}`
    },
    assignStyle: function() {
      return Object.assign(this.defaultStyle, this.customStyle);
    }
  },
  methods: {
    initScene() {
      const ssp = new SoonSpace({
        el: `#${this.id}`,
        option: Object.assign(
          {
            showInfo: false
          },
          this.option
        ),
        events: {
          modelClick: param => this.$emit("modelClick", param),
          modelDblClick: param => this.$emit("modelDblClick", param),
          modelHover: param => this.$emit("modelHover", param),
          poiClick: param => this.$emit("poiClick", param),
          poiHover: param => this.$emit("poiHover", param),
          poiDblClick: param => this.$emit("poiDblClick", param),
          selectPosition: param => this.$emit("selectPosition", param)
        }
      });

      if (this.globalSdk) this.$root.__proto__.$ssp = ssp;

      this.$emit("sceneReady", ssp);
    }
  },
  mounted() {
    this.initScene();
  }
};
</script>
```
