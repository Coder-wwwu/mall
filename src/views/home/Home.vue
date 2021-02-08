<!--  -->
<template>
  <div id="home">
    <nav-bar class="home-nav">
      <div slot="center">购物街</div>
    </nav-bar>

    <scroll
      ref="scroll"
      class="scroll"
      :probeType="3"
      @scroll="contentClick"
      :pullUpload="true"
      @pillingUp="loadMore"
    >
      <div class="content">
        <home-swiper :banners="banners"></home-swiper>
        <recommend-view :recommends="recommends"></recommend-view>
        <feature-view></feature-view>
        <tab-control
          :titles="['流行', '新款', '精选']"
          class="tab-control"
          @tabClick="tabClick"
        >
        </tab-control>
        <good-list :goods="showGoods"></good-list>
      </div>
    </scroll>
    <back-top class="back-top" v-show="isShowTopBack" @backTop="backTopClick">
      <img src="~assets/img/common/top.png" />
    </back-top>
  </div>
</template>

<script>
import NavBar from "components/common/navbar/NavBar.vue";
import TabControl from "components/content/tabControl/TabControl.vue";
import GoodList from "components/content/goods/GoodList.vue";
import Scroll from "../../components/common/scroll/Scroll.vue";
import BackTop from "../../components/content/topBack/BackTop.vue";

import { getHomeMultidata, getHomeGoods } from "network/home.js";

import HomeSwiper from "./childComps/HomeSwiper";
import RecommendView from "./childComps/RecommendView";
import FeatureView from "./childComps/FeatureView";

export default {
  data() {
    return {
      banners: [],
      recommends: [],
      goods: {
        pop: { page: 1, list: [] },
        new: { page: 1, list: [] },
        sell: { page: 1, list: [] }
      },
      currentType: "pop",
      isShowTopBack: false
    };
  },
  created() {
    this.getHomeMultidata();
    this.getHomeGoods("pop");
    this.getHomeGoods("new");
    this.getHomeGoods("sell");
  },
  computed: {
    showGoods() {
      return this.goods[this.currentType].list;
    }
  },
  methods: {
    // 事件监听相关方法
    tabClick(index) {
      switch (index) {
        case 0:
          this.currentType = "pop";
          break;
        case 1:
          this.currentType = "new";
          break;
        case 2:
          this.currentType = "sell";
          break;
      }
    },
    backTopClick() {
      console.log("回到顶部");
      this.$refs.scroll.scrollTo(0, 0);
      console.log(this.$refs.scroll.scroll);
    },
    contentClick(position) {
      // console.log(position);
      this.isShowTopBack = position.y < -1000;
      // console.log(position.y < -1000);
    },

    loadMore() {
      console.log("向上加载更多");

      /* this.$refs.scroll.scroll.finishPullUp(); */
      this.getHomeGoods(this.currentType);
      // this.$refs.scroll.scroll.refresh();
    },
    // 网络请求相关方法
    getHomeMultidata() {
      getHomeMultidata().then(res => {
        console.log(res);
        this.banners = res.data.banner.list;
        this.recommends = res.data.recommend.list;
      });
    },
    getHomeGoods(type) {
      getHomeGoods(type, this.goods[type].page).then(res => {
        //...语法会将数组中的数据一个个的push进list数组中
        this.goods[type].list.push(...res.data.list);
        this.goods[type].page += 1;
        console.log(this.goods[type].page);
        this.finishPullUp();
      });
    },
    finishPullUp() {
      return this.$refs.scroll.scroll.finishPullUp();
    }
  },
  components: {
    NavBar,
    TabControl,
    HomeSwiper,
    RecommendView,
    FeatureView,
    GoodList,
    Scroll,
    BackTop
  }
};
</script>

<style scoped>
#home {
  /* padding-top: 44px; */
  height: 100vh;
}
.home-nav {
  background-color: var(--color-tint);
  color: white;
  position: fixed;
  left: 0;
  right: 0;
  top: 0;
  z-index: 9;
}
.tab-control {
  /* position: sticky; */
  top: 44px;
  z-index: 9;
}

.scroll {
  height: 100px;
}
.content {
  /* height: calc(100% - 93px);  */
  height: 4300px;
}

.back-top {
  position: fixed;
  right: 20px;
  bottom: 60px;
}
</style>
