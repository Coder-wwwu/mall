<!--  -->
<template>
  <div ref="wrapper" class="wrapper">
    <div>
      <slot></slot>
    </div>
  </div>
</template>

<script>
import BScroll from "better-scroll";
export default {
  data() {
    return {
      scroll: null
    };
  },
  props: {
    probeType: {
      type: Number,
      default: 1
    },
    pullUpload: {
      tupe: Boolean,
      default: false
    }
  },
  mounted() {
    this.scroll = new BScroll(this.$refs.wrapper, {
      click: true,
      probeType: this.probeType,
      pullUpLoad: this.pullUpload
    });
    if (this.probeType == 2 || this.probeType == 3) {
      this.scroll.on("scroll", position => {
        /* console.log(position); */
        this.$emit("scroll", position);
      });
    }
    this.scroll.on("pullingUp", () => {
      this.$emit("pillingUp");
    });
  },
  methods: {
    scrollTo(x, y, time = 300) {
      this.scroll && this.scrollTo && this.scroll.scrollTo(x, y, time);
    },
    finishPullUp() {
      this.scroll && this.scroll.finishPullUp();
    },
    refresh() {
      console.log("重新刷新");
      this.scroll && this.scroll.refresh();
    },
    getSaveY() {
      return this.scroll ? this.scroll.y : 0;
    }
  }
};
</script>

<style scoped>
/* 有scoped则该样式只在当前组件生效 */
/* .content {
  height: 4500px;
  overflow: hidden;
  height: calc(100% - 93px);
} */
</style>
