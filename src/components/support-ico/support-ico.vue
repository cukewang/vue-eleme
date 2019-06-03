<template>
  <span class="support-ico" :class="iconCls"></span>
</template>

<script>
export default {
  // 组件名称
  name: 'support-ico',
  // 组件传入的对象
  props: {
    size: {
      type: Number
    },
    type: {
      type: Number
    }
  },
  computed: {
    // 根据传入的值 然后输出个计算属性，然后返回到绑定的class上去，根据下面的css文件确定背景图片，从而搞出来一个很方便的图标。
    iconCls () {
      const classMap = [
        'decrease',
        'discount',
        'special',
        'invoice',
        'guarantee'
      ]
      // 这个是符号拼接符，在``里面填入正常的字符，然后用${}里面添加动态的东西。
      return `icon-${this.size} ${classMap[this.type]}`
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '~common/stylus/mixin'
.support-ico
  display: inline-block
  background-repeat: no-repeat
.icon-1
  width: 12px
  height: 12px
  background-size: 12px 12px
  &.decrease
    bg-image('decrease_1')
  &.discount
    bg-image('discount_1')
  &.guarantee
    bg-image('guarantee_1')
  &.invoice
    bg-image('invoice_1')
  &.special
    bg-image('special_1')
.icon-2
  width: 16px
  height: 16px
  background-size: 16px 16px
  &.decrease
    bg-image('decrease_2')
  &.discount
    bg-image('discount_2')
  &.guarantee
    bg-image('guarantee_2')
  &.invoice
    bg-image('invoice_2')
  &.special
    bg-image('special_2')
.icon-3
  width: 12px
  height: 12px
  background-size: 12px 12px
  &.decrease
    bg-image('decrease_3')
  &.discount
    bg-image('discount_3')
  &.guarantee
    bg-image('guarantee_3')
  &.invoice
    bg-image('invoice_3')
  &.special
    bg-image('special_3')
.icon-4
  width: 16px
  height: 16px
  background-size: 16px 16px
  &.decrease
    bg-image('decrease_4')
  &.discount
    bg-image('discount_4')
  &.guarantee
    bg-image('guarantee_4')
  &.invoice
    bg-image('invoice_4')
  &.special
    bg-image('special_4')
</style>
