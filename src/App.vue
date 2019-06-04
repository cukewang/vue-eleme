<template>
  <div id="app">
    <!-- // 第三步，在HTML结构里用羊肉串表示法表示，因为HTML标签不支持大小写，所以一律用小写，然后用签子穿起来
    // 第四步，等一会儿传入一些props给子组件，然后能让他在里面渲染出数据来。
    // 至于为什么要在App.vue页面里传入而不是直接在组件里获取是因为一会这个页面上其他的页面也需要被传入seller数据进去，省了重复太多次获取了。-->
    <v-header :seller="seller"></v-header>
    <!-- <header-detail :seller="seller"></header-detail> -->
    <div class="tap-wapper">
      <tab :tabs="tabs"></tab>
    </div>
  </div>
</template>

<script>
// 第一步 引入组件， 注意组件变量最好用驼峰表示法表示
import VHeader from 'components/v-header/v-header'
// import HeaderDetail from 'components/header-detail/header-detail'
import Goods from 'components/goods/goods'
import Seller from 'components/seller/seller'
import Ratings from 'components/ratings/ratings'
import { getSeller } from 'api'
import Tab from 'components/tab/tab'

export default {
  name: 'app',
  data () {
    return {
      seller: {}
    }
  },
  computed: {
    tabs () {
      return [
        {
          label: '商品',
          component: Goods,
          data: {
            seller: this.seller
          }
        },
        {
          label: '评价',
          component: Ratings,
          data: {
            seller: this.seller
          }
        },
        {
          label: '商家',
          component: Seller,
          data: {
            seller: this.seller
          }
        }
      ]
    }
  },
  created () {
    this._getGoods()
  },
  methods: {
    _getGoods () {
      getSeller().then(seller => {
        this.seller = seller
      })
    }
  },
  components: {
    // 第二步，在组件里声明一下，然后这个组件就可以在app里用了
    VHeader,
    // HeaderDetail
    Tab
  }
}
</script>
<style lang="stylus">
#app
  .tap-wapper
    position: fixed
    top: 136px
    bottom: 0
    left: 0
    right: 0
</style>
