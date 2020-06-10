<template>
  <div id="detail" class="wrapper">
    <detail-nav-bar class="detail-nav-bar" @itemClick="itemClick"></detail-nav-bar>
    <scroll class="content" ref="scroll" :probe-type="3" @scroll="scrollPosListener">
      <detail-swiper :banners="banners"></detail-swiper>
      <detail-base-info :goods="goods"></detail-base-info>
      <detail-shop-info :shop="shop"></detail-shop-info>
      <detail-goods-info :detailInfo="detailInfo" @imageLoad="imageLoad">
      </detail-goods-info>
      <detail-param-info :paramInfo="paramInfo" ref="params"></detail-param-info>
      <detail-comment-info :commentInfo="commentInfo" ref="comments"></detail-comment-info>
      <goods-list :goods="recommends" ref="recommends"></goods-list>
    </scroll>
    <back-top v-show="isShowBackTop" @click.native="backTop" class="back-top"></back-top>
  </div>
</template>

<script>
  import Swiper from "components/common/swiper/Swiper"
  import Scroll from "components/common/scroll/Scroll"
  import {
    debounce
  } from 'common/utils'

  import DetailSwiper from "./childComps/DetailSwiper"
  import DetailNavBar from "./childComps/DetailNavBar"
  import DetailBaseInfo from "./childComps/DetailBaseInfo"
  import DetailShopInfo from "./childComps/DetailShopInfo"
  import DetailGoodsInfo from "./childComps/DetailGoodsInfo"
  import DetailParamInfo from "./childComps/DetailParamInfo"
  import DetailCommentInfo from "./childComps/DetailCommentInfo"

  import GoodsList from "components/content/goods/GoodsList"
  import BackTop from "components/content/backtop/BackTop"


  import {
    getGoods,
    Goods,
    GoodsParam,
    Shop,
    getRecommend,
  } from "network/detail"

  export default {
    name: "Detail",
    components: {
      Swiper,
      Scroll,
      DetailSwiper,
      DetailNavBar,
      DetailBaseInfo,
      DetailShopInfo,
      DetailGoodsInfo,
      DetailParamInfo,
      DetailCommentInfo,
      GoodsList,
      BackTop,
    },
    data() {
      return {
        iid: null,
        banners: [],
        goods: {},
        paramInfo: {},
        shop: {},
        detailInfo: {},
        data: {},
        commentInfo: {},
        recommends: [],
        isShowBackTop: false,
        itemRefresh: null,
        navValueY: [],
        getNavValueY: null
      }
    },
    created() {
      this.iid = this.$route.params.iid
      console.log("2222" + this.iid);
      this.getGoods(this.iid)
      this.getRecommend()
      this.getNavValueY = debounce(() => {
        this.navValueY.push(0)
        this.navValueY.push(this.$refs.params.$el.offsetTop)
        this.navValueY.push(this.$refs.comments.$el.offsetTop)
        this.navValueY.push(this.$refs.recommends.$el.offsetTop)
        console.log(this.navValueY);

      })
    },
    methods: {
      itemClick(index) {
        this.$refs.scroll.scrollTo(0, -this.navValueY[index], 200)
      },
      imageLoad() {
        console.log("detailImageLoad");
        this.$refs.scroll.refresh()
        this.getNavValueY()
      },
      getRecommend() {
        getRecommend().then(res => {
          this.recommends = res.data.list
        })
      },
      getGoods(iid) {
        getGoods(iid).then(res => {
          const data = res.result
          this.data = res.result
          this.banners = data.itemInfo.topImages
          this.goods = new Goods(data.itemInfo, data.columns, data.shopInfo.services);
          this.shop = new Shop(data.shopInfo);
          this.detailInfo = data.detailInfo
          this.paramInfo = new GoodsParam(data.itemParams.info, data.itemParams.rule)
          if (data.rate.list) {
            this.commentInfo = data.rate.list[0];
          }
        })
      },
      backTop() {
        this.$refs.scroll.scrollTo(0, 0, 500)
      },
      scrollPosListener(pos) {
        -pos.y > 1000 ? this.isShowBackTop = true : this.isShowBackTop = false
      }
    },
    mounted() {
      console.log("detailItemImageLoad");
      
      this.itemRefresh = debounce(this.$refs.scroll.refresh, 100)
      this.$bus.$on('detailItemImageLoad', () => {
        this.itemRefresh()
      })
    },
  }
</script>

<style scoped>
  #detail {
    height: 100vh;
    position: relative;
    z-index: 1;
    background-color: #fff;
  }


  .content {
    /* height: calc(100% - 93px);
    overflow: hidden;
    position: absolute; */
    overflow: hidden;
    position: absolute;
    top: 44px;
    bottom: 0;
    left: 0;
    right: 0;
  }

  .back-top {
    right: 20px;
    bottom: 20px;
  }
</style>