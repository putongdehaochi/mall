<template>
  <div id="home" class="wrapper">
    <nav-bar class="home-nav">
      <div slot="center">购物街</div>
    </nav-bar>
    <scroll class="content" ref="scroll" :probe-type="3" :pull-up-load="true" @scroll="contentScroll"
      @pullingUp="loadMore">
      <home-swiper :banners="banners"></home-swiper>
      <home-recommend-view :recommends="recommends"></home-recommend-view>
      <home-feature-view></home-feature-view>
      <tab-control :titles="['流行','新款','精选']" class="tab-control" @tabClick="tabClick"></tab-control>
      <goods-list :goods="showGoods"></goods-list>
    </scroll>
    <back-top @click.native="backTop" v-show="isShowBackTop" />

  </div>
</template>

<script>
  import NavBar from "components/common/navbar/NavBar"
  import Scroll from "components/common/scroll/Scroll"


  import TabControl from "components/content/tabControl/TabControl"
  import GoodsList from "components/content/goods/GoodsList"
  import BackTop from "components/content/backtop/BackTop"


  import HomeSwiper from "./childComps/HomeSwiper"
  import HomeRecommendView from "./childComps/HomeRecommendView"
  import HomeFeatureView from "./childComps/HomeFeatureView"


  import {
    getHomeMultidata,
    getHomeGoods
  } from "network/home"
  export default {
    name: "Home",
    components: {
      NavBar,
      HomeSwiper,
      HomeRecommendView,
      HomeFeatureView,
      TabControl,
      GoodsList,
      Scroll,
      BackTop,
    },
    computed: {
      showGoods() {
        return this.goods[this.currentType].list
      }
    },
    data() {
      return {
        banners: [],
        recommends: [],
        goods: {
          'pop': {
            page: 0,
            list: []
          },
          'new': {
            page: 0,
            list: []
          },
          'sell': {
            page: 0,
            list: []
          },
        },
        currentType: "pop",
        isShowBackTop: false
      }
    },
    created() {
      this.getHomeMultidata()
      this.getHomeGoods('pop')
      this.getHomeGoods('new')
      this.getHomeGoods('sell')
    },
    methods: {
      // 事件
      tabClick(index) {
        switch (index) {
          case 0:
            this.currentType = 'pop'
            break;
          case 1:
            this.currentType = 'new'
            break;
          case 2:
            this.currentType = 'sell'
            break;
          default:
            break;
        }
      },
      backTop() {
        this.$refs.scroll.scrollTo(0, 0, 500)
      },
      contentScroll(pos) {
        -pos.y > 1000 ? this.isShowBackTop = true : this.isShowBackTop = false
      },
      loadMore() {
        this.getHomeGoods(this.currentType)
        this.$refs.scroll.finishPullUp()
      },
      // 网络请求
      getHomeMultidata() {
        getHomeMultidata().then(res => {
          this.banners = res.data.banner.list;
          this.recommends = res.data.recommend.list;
        })
      },
      getHomeGoods(type) {
        let page = this.goods[type].page + 1
        getHomeGoods(type, page).then(res => {
          this.goods[type].list.push(...res.data.list)
          this.goods[type].page += 1
        })
      }
    },
  }
</script>


<style scoped>
  #home {
    height: 100vh;
  }

  .home-nav {
    background-color: var(--color-tint);
    color: #fff;
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

  .content {
    height: calc(100% - 93px);
    margin-top: 44px;
  }
</style>