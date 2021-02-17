<!--  -->
<template>
  <div id="home">
    <nav-bar class="home-nav">
      <div slot="center">购物街</div>
    </nav-bar>
    <tab-control
      :titles="['流行', '新款', '精选']"
      @tabClick="tabClick"
      ref="tabControl1"
      class="fixed"
      v-show="isTabFixed"
    >
    </tab-control>
    <scroll
      ref="scroll"
      class="content"
      :probeType="3"
      @scroll="contentClick"
      :pullUpload="true"
      @pillingUp="loadMore"
    >
      <div>
        <home-swiper
          :banners="banners"
          @swiperImageLoad="swiperImageLoad"
        ></home-swiper>
        <recommend-view :recommends="recommends"></recommend-view>
        <feature-view></feature-view>
        <tab-control
          :titles="['流行', '新款', '精选']"
          @tabClick="tabClick"
          ref="tabControl2"
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
import { debounce } from "components/common/utils.js";
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
        pop: { page: 0, list: [] },
        new: { page: 0, list: [] },
        sell: { page: 0, list: [] }
      },
      currentType: "pop",
      isShowTopBack: false,
      tabOffsetTop: 0,
      isTabFixed: false,
      saveY: 0
    };
  },
  created() {
    this.getHomeMultidata();
    this.getHomeGoods("pop");
    this.getHomeGoods("new");
    this.getHomeGoods("sell");
  },
  destroyed() {
    console.log("home destroyed");
  },
  mounted() {
    console.log(this.$refs.scroll.scroll);
    //监听goodListItem中图片加载完成
    const refresh = debounce(this.$refs.scroll.refresh, 50);
    this.$bus.$on("itemImageLoad", () => {
      refresh();
    });
  },
  activated() {
    this.$refs.scroll.scrollTo(0, this.saveY, 0);
    this.$refs.scroll.refresh();
  },
  deactivated() {
    this.saveY = this.$refs.scroll.getSaveY();
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
      this.$refs.tabControl1.currentIndex = index;
      this.$refs.tabControl2.currentIndex = index;
    },
    backTopClick() {
      console.log("回到顶部");
      this.$refs.scroll.scrollTo(0, 0);
    },
    contentClick(position) {
      //1.判断BackTop是否显示
      this.isShowTopBack = position.y < -1000;
      //2.决定TabControl是否吸顶
      this.isTabFixed = -position.y > this.tabOffsetTop;
    },

    loadMore() {
      console.log("向上加载更多");
      this.getHomeGoods(this.currentType);
    },
    swiperImageLoad() {
      //获取tabControl的offsetTop
      //所有的vue组件都有个属性$el,用于获取组件内的元素
      console.log(this.$refs.tabControl2.$el.offsetTop);
      this.tabOffsetTop = this.$refs.tabControl2.$el.offsetTop;
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
      const page = this.goods[type].page + 1;
      getHomeGoods(type, page).then(res => {
        //...语法会将数组中的数据一个个的push进list数组中
        this.goods[type].list.push(...res.data.list);
        this.goods[type].page += 1;
        //完成向上加载更多
        this.$refs.scroll.finishPullUp();
      });
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
  /*  以下属性是为了在浏览器原生滚动时,导航不跟随一起滚动所设置 */
  position: fixed;
  left: 0;
  right: 0;
  top: 0;
  z-index: 9;
}

.content {
  /* height: calc(100% - 93px);
  overflow: hidden; */
  /* height: 4300px; */
  position: absolute;
  top: 44px;
  bottom: 49px;
  left: 0;
  right: 0;
}

.back-top {
  position: fixed;
  right: 20px;
  bottom: 60px;
}
.tab-control {
  position: relative;
  z-index: 9;
}
.fixed {
  position: fixed;
  top: 44px;
  left: 0;
  right: 0;
}
</style>
