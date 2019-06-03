<template>
  <div id="app">
    <!-- // 第三步，在HTML结构里用羊肉串表示法表示，因为HTML标签不支持大小写，所以一律用小写，然后用签子穿起来
    // 第四步，等一会儿传入一些props给子组件，然后能让他在里面渲染出数据来。
    // 至于为什么要在App.vue页面里传入而不是直接在组件里获取是因为一会这个页面上其他的页面也需要被传入seller数据进去，省了重复太多次获取了。-->
    <v-header :seller="seller"></v-header>
    <!-- <header-detail :seller="seller"></header-detail> -->
    <tab></tab>
  </div>
</template>

<script>
// 第一步 引入组件， 注意组件变量最好用驼峰表示法表示
import VHeader from 'components/v-header/v-header'
// import HeaderDetail from 'components/header-detail/header-detail'
import { getSeller } from 'api'
import Tab from 'components/tab/tab'

export default {
  name: 'app',
  data () {
    return {
      seller: {}
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
<style lang="stylus"></style>
