<!--  -->
<template>
  <div>
    <swiper class="detail-swiper">
      <swiper-item v-for="item in topImages">
        <img :src="item" />
      </swiper-item>
    </swiper>
  </div>
</template>

<script>
import { Swiper, SwiperItem } from "components/common/swiper";
export default {
  data() {
    return {};
  },
  props: {
    topImages: {
      type: Array,
      defult() {
        return [];
      }
    }
  },
  components: {
    Swiper,
    SwiperItem
  }
};
</script>

<style scoped>
.detail-swiper {
  height: 300px;
}
</style>
