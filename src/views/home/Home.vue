<template>
  <div id="home">
    <nav-bar class="home-bar">
      <div slot="center">购物街</div>
    </nav-bar>
    <scroll class="content">
      <home-swiper :banner="banner"></home-swiper>
      <recommend-view :recommend="recommend"></recommend-view>
      <feature-view></feature-view>
      <tab-control :titles="['流行','新款','精选']" class="tab-control" @tabClick="tabClick"></tab-control>
      <good-list :goods="showGoods"></good-list>
    </scroll>
    <back-top @click.native="backClick"></back-top>
  </div>
</template>

<script>
  import NavBar from "../../components/common/navbar/NavBar";
  import HomeSwiper from "./childComps/HomeSwiper";
  import FeatureView from "./childComps/FeatureView";
  import RecommendView from "./childComps/RecommendView";
  import GoodList from "../../components/content/goods/GoodList";
  import BackTop from "../../components/content/backtop/BackTop";

  import TabControl from "../../components/content/tabControl/TabControl";
  import Scroll from "../../components/common/scroll/Scroll";

  import {getHomeMultidata, getHomeGoods} from "network/home"


  export default {
    name: "Home",
    components: {
      TabControl,
      NavBar,
      HomeSwiper,
      RecommendView,
      FeatureView,
      GoodList,
      Scroll,
      BackTop

    },
    computed: {
      showGoods() {
        return this.goods[this.goodsName].list
      }
    },
    data() {
      return {
        banner: {},
        dKeyword: {},
        keywords: {},
        recommend: {},
        goods: {
          'pop': {page: 0, list: []},
          'new': {page: 0, list: []},
          'sell': {page: 0, list: []},
        },
        goodsName: 'pop',
      }
    },
    created() {
      // 1.请求多个数据
      this.getHomeMultidata()

      // 2.请求商品数据
      this.getHomeGoods('pop')
      this.getHomeGoods('new')
      this.getHomeGoods('sell')
    },
    mounted() {

    },
    methods: {
      backClick() {
        this.$refs.scroll.scrollTo(0, 0)
      },
      getHomeMultidata() {
        getHomeMultidata().then(res => {
          // this.result = res;
          this.banner = res.data.banner.list;
          this.recommend = res.data.recommend.list;
        })
      },
      getHomeGoods(type) {
        const page = this.goods[type].page + 1
        getHomeGoods(type, page).then(res => {
          this.goods[type].list.push(...res.data.list)
          this.goods[type].page += 1

        })
      },
      tabClick(index) {
        switch (index) {
          case 0:
            this.goodsName = 'pop'
            break
          case 1:
            this.goodsName = 'new'
            break
          case 2:
            this.goodsName = 'sell'
            break
        }
      }
    }
  }
</script>

<style scoped>
  #home {
    /*padding-top: 44px;*/
    height: 100vh;
    position: relative;
  }

  .content {
    overflow: hidden;

    position: absolute;
    top: 44px;
    bottom: 49px;
    left: 0;
    right: 0;
  }

  .home-bar {
    background-color: var(--color-tint);
    color: #f6f6f6;

    position: fixed;
    left: 0;
    right: 0;
    top: 0;
    z-index: 9;
  }

  .tab-control {
    position: sticky;
    top: 44px;
    z-index: 9;
  }
</style>
