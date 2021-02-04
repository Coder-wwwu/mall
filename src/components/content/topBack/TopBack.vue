<!--  -->
<template>
  <div class="back-top">
    <slot></slot>
  </div>
</template>

<script>
export default {
  data() {
    return {};
  },
  methods: {
    btnClick() {
      // this.$emit("backTop");
    }
  }
};
</script>

<style scoped>
.back-top img {
  width: 43px;
  height: 43px;
}
</style>
