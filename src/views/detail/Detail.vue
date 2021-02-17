<!--  -->
<template>
  <div>
    <detail-nav-bar></detail-nav-bar>
    <detail-swiper :top-images="topImages"></detail-swiper>
  </div>
</template>

<script>
import DetailNavBar from "./childComps/DetailNavBar.vue";
import DetailSwiper from "./childComps/DetailSwiper";
import { getDetail } from "network/detail.js";

export default {
  components: { DetailNavBar },
  data() {
    return {
      iid: null,
      topImages: null
    };
  },
  created() {
    //1.保存传入的id值
    this.iid = this.$route.params.iid;
    //2.根据iid请求详情数据
    getDetail(this.iid).then(res => {
      console.log(res);
      //获取顶部轮播图的数据
      this.topImages = res.result.itemInfo.topImages;
    });
  },
  methods: {},
  components: {
    DetailNavBar,
    DetailSwiper
  }
};
</script>

<style scoped></style>
