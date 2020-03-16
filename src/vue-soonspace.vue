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
