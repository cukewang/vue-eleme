
<template>
  <div class="tab">
    <cube-tab-bar
      :showSlider="true"
      :useTranstion="false"
      v-model="selectedLabel"
      :data="tabs"
      ref="tabBar"
      class="border-bottom-1px"
    ></cube-tab-bar>
    <div class="slide-wrapper">
      <cube-slide
        :loop="false"
        :auto-play="false"
        :show-dots="false"
        :initial-index="index"
        ref="slide"
        @change="onChange"
        @scroll="onScroll"
        :options="scrollOptions"
      >
        <cube-slide-item v-for="(tab,index) in tabs" :key="index">
          <component :is="tab.component" :data="tab.data" ref="component"></component>
        </cube-slide-item>
      </cube-slide>
    </div>
  </div>
</template>

<script>
// import Goods from 'components/goods/goods'
// import Seller from 'components/seller/seller'
// import Ratings from 'components/ratings/ratings'
export default {
  name: 'tab',
  props: {
    tabs: {
      type: Array,
      defalut () {
        return []
      }
    },
    initialIndex: {
      type: Number,
      default: 0
    }
  },
  data () {
    return {
      index: this.initialIndex,
      // tabs: [{ label: '商品' }, { label: '评价' }, { label: '商家' }],
      scrollOptions: {
        listenScroll: true,
        probeType: 3
      }
    }
  },
  computed: {
    selectedLabel: {
      get () {
        return this.tabs[this.index].label
      },
      set (newVal) {
        this.index = this.tabs.findIndex(value => {
          return value.label === newVal
        })
      }
    }
  },
  mounted () {
    this.onChange(this.index)
  },
  methods: {
    onChange (current) {
      this.index = current
      const component = this.$refs.component[current]
      component.fetch && component.fetch()
    },
    onScroll (pos) {
      console.log(pos.x)
      const tabbarBarWidth = this.$refs.tabBar.$el.clientWidth
      const slideWidth = this.$refs.slide.slide.scrollerWidth
      const transform = (-pos.x / slideWidth) * tabbarBarWidth
      this.$refs.tabBar.setSliderTransform(transform)
    }
  }
  // components: {
  //   Goods,
  //   Seller,
  //   Ratings
  // }
}
</script>

<style lang="stylus" scoped>
@import '~common/stylus/variable'
.tab
  display: flex
  flex-direction: column
  height: 100%
  >>> .cube-tab
    padding: 10px 0
  .slide-wrapper
    flex: 1
    overflow: hidden
</style>
