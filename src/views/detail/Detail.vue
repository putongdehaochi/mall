<template>
  <div id="detail" class="wrapper">
    <detail-nav-bar class="detail-nav-bar"></detail-nav-bar>
    <scroll class="content" ref="scroll">
      <detail-swiper :banners="banners"></detail-swiper>
      <detail-base-info :goods="goods"></detail-base-info>
      <detail-shop-info :shop="shop"></detail-shop-info>
      <detail-goods-info :detailInfo="detailInfo" @imageLoad="imageLoad"></detail-goods-info>
      <detail-param-info :paramInfo="paramInfo"></detail-param-info>
      <detail-comment-info :commentInfo="commentInfo"></detail-comment-info>
      <goods-list :goods="recommends"></goods-list>
    </scroll>
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
      }
    },
    created() {
      this.iid = this.$route.params.iid
      this.getGoods(this.iid)
      this.getRecommend()
    },
    methods: {
      imageLoad() {
        this.$refs.scroll.refresh()
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
      }
    },
    mounted() {
      this.$bus.$on('detailItemImageLoad', () => {
        debounce(this.$refs.scroll.refresh, 100)
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
</style>