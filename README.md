#  Vue饿了么

## 1.构建项目

### 1.1构建项目

在项目的目录里打开终端，安装vue-cli，用npm全局安装就行

`npm i -g vue-cli@3`

然后再项目根目路里 vue create 项目名字(必须小写) 然后根据提示安装就好了。

```javascript
// 项目名称
? Please pick a preset: Manually select features
// 自动安装相应的依赖
? Check the features needed for your project: Babel, CSS Pre-processors, Linter
// 选择CSS预处理器
? Pick a CSS pre-processor (PostCSS, Autoprefixer and CSS Modules are supported by default): Stylus
// 选择Lint的标准
? Pick a linter / formatter config: Standard
// 保存的时候进行格式化还是提交的时候进行格式化
? Pick additional lint features: (Press <space> to select, <a> to toggle all, <i> to invert selection)Lint on save
// 在config里还是packjson里进行配置
? Where do you prefer placing config for Babel, PostCSS, ESLint, etc.? In dedicated config files
// 是否记住这次的选项下次不用弄了
? Save this as a preset for future projects? No
```

### 1.2 安装Cube-ui

首先在vue-cli@3的话，在cube-ui的官方文档里，只需要`vue add cube-ui`就完事了。

```javascript
// 用不用前编译，用就完事了
? Use post-compile? Yes
// 加入部分还是全部 部分
? Import type Partly
// 用不用他的样式
? Custom theme? Yes
? Use rem layout? No
? Use vw layout? No
```

然后会根据配置在安装依赖。

### 1.3引入data.json

复制到项目根目路就行了。真实的项目里数据获取先不用操心。现在vue.config.js里引入data.json

```javascript
const appData = require('./data.json')
const seller = appData.seller
const goods = appData.goods
const ratings = appData.ratings
```



然后用在vue.config.js里配置DevServer里配置，具体的事before是express框架提供的，不太清楚怎么用

```javascript
 devServer: {
    before (app) {
      app.get('/api/seller', function (req, res) {
        res.json({
          errno: 0,
          data: seller
        })
      })
      app.get('/api/goods', function (req, res) {
        res.json({
          errno: 0,
          data: goods
        })
      })
      app.get('/api/ratings', function (req, res) {
        res.json({
          errno: 0,
          data: ratings
        })
      })
    }
  }
```

##2.项目开发

### 2.1 引入字体和样式

在src目录里新建common文件夹里新建两个文件夹，分别是fonts和stylus文件夹。

![image-20190602193308545](/Users/wangzirui/Library/Application Support/typora-user-images/image-20190602193308545.png)

文件结构如下，在index.styl文件下引入icon.styl和base.styl文件

```javascript
@import "./icon"
@import "./base"
```

icon是字体图标的样式，base定义了一些基础的样式。

通过在main.js里引入index.styl这个文件来对整个项目添加这些样式

`import './common/stylus/index.styl'`

另外mixin.styl和variable.styl都是针对样式来定义的文件。在里面直接引入cube-ui里的就行了

```javascript
// maxin.styl
@import "~cube-ui/src/common/stylus/mixin.styl"

// variable.styl
@import "~cube-ui/src/common/stylus/variable.styl"

//这个是定义的一些项目的颜色(在cube-ui的基础上定义的)
$color-background = rgba(7, 17, 27, 1)
$color-background-s = rgba(7, 17, 27, 0.8)
$color-background-ss = rgba(7, 17, 27, 0.5)
$color-background-sss = rgba(7, 17, 27, 0.2)
$color-background-ssss = #f3f5f7

$color-red = rgb(240, 20, 20)
$color-blue = rgb(0, 160, 220)
$color-light-blue = rgba(0, 160, 220, 0.2)
$color-green = #00b43c

$color-col-line = #d9dde1
$color-row-line = rgba(7, 17, 27, 0.1)
```



### 2.2引入v-header组件

首先在src下的components文件夹里新建两个文件夹，一个是support-ico和v-header文件夹，分别表示两个组件

![image-20190603140821219](/Users/wangzirui/Library/Application Support/typora-user-images/image-20190603140821219.png)

在vue.config.js里引用path，在新建一个 resolve（dir）函数。再在**chainWebpack** (*config*) 里面定义关于路径的一些配置

```javascript
// vue.config.js
const path = require('path')

function resolve (dir) {
  return path.join(__dirname, dir)
}

chainWebpack (config) {
    config.resolve.alias
      .set('components', resolve('src/components'))
      .set('common', resolve('src/common'))
}

```

这样就可以直接用'common/stylus/index'这样的路径来表示路径了，不用担心地址问题。另外vscode需要安装

Path Intellisense
Path Autocomplete

这两个插件才可以实现自动化提示路径，如果不安装的话直接输入'common'的时候是不会给出路径的提示的。

```javascript
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

```



另外v-header组件 ，在源码中看吧，没有什么复杂的，就是布局的问题。

```javascript
<template>
  <div class="header" @click="showDetail">
    <div class="content-wrapper">
      <div class="avatar">
        <img width="64" height="64" :src="seller.avatar">
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <div v-if="seller.supports" class="support">
          <support-ico :size=1 :type="seller.supports[0].type"></support-ico>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div v-if="seller.supports" class="support-count">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper">
      <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
      <img :src="seller.avatar" width="100%" height="100%">
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import SupportIco from 'components/support-ico/support-ico'

  export default {
    name: 'v-header',
    props: {
      seller: {
        type: Object,
        default() {
          return {}
        }
      }
    },
    methods: {
      showDetail() {
        this.headerDetailComp = this.headerDetailComp || this.$createHeaderDetail({
          $props: {
            seller: 'seller'
          }
        })
        this.headerDetailComp.show()
      }
    },
    components: {
      SupportIco
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "~common/stylus/mixin"
  @import "~common/stylus/variable"

  .header
    position: relative
    overflow: hidden
    color: $color-white
    background: $color-background-ss
    .content-wrapper
      position: relative
      display: flex
      align-items: center
      padding: 24px 12px 18px 24px
      .avatar
        flex: 0 0 64px
        width: 64px
        margin-right: 16px
        img
          border-radius: 2px
      .content
        flex: 1
        .title
          display: flex
          align-items: center
          margin-bottom: 8px
          .brand
            width: 30px
            height: 18px
            bg-image('brand')
            background-size: 30px 18px
            background-repeat: no-repeat
          .name
            margin-left: 6px
            font-size: $fontsize-large
            font-weight: bold
        .description
          margin-bottom: 8px
          line-height: 12px
          font-size: $fontsize-small
        .support
          display: flex
          align-items: center
          .support-ico
            margin-right: 4px
          .text
            line-height: 12px
            font-size: $fontsize-small-s

      .support-count
        position: absolute
        right: 12px
        bottom: 14px
        display: flex
        align-items: center
        padding: 0 8px
        height: 24px
        line-height: 24px
        text-align: center
        border-radius: 14px
        background: $color-background-sss
        .count
          font-size: $fontsize-small-s
        .icon-keyboard_arrow_right
          margin-left: 2px
          line-height: 24px
          font-size: $fontsize-small-s

    .bulletin-wrapper
      position: relative
      display: flex
      align-items: center
      height: 28px
      line-height: 28px
      padding: 0 8px
      background: $color-background-sss
      .bulletin-title
        flex: 0 0 22px
        width: 22px
        height: 12px
        margin-right: 4px
        bg-image('bulletin')
        background-size: 22px 12px
        background-repeat: no-repeat
      .bulletin-text
        flex: 1
        white-space: nowrap
        overflow: hidden
        text-overflow: ellipsis
        font-size: $fontsize-small-s
      .icon-keyboard_arrow_right
        flex: 0 0 10px
        width: 10px
        font-size: $fontsize-small-s
    .background
      position: absolute
      top: 0
      left: 0
      width: 100%
      height: 100%
      z-index: -1
      filter: blur(10px)
</style>

```

写好了以后再App.vue去引用，就是很标准的引用组件的样子

```javascript
<template>
  <div id="app">
 <!-- // 第三步，在HTML结构里用羊肉串表示法表示，因为HTML标签不支持大小写，所以一律用小写，然后用签子穿起来
    // 第四步，等一会儿传入一些props给子组件，然后能让他在里面渲染出数据来。
    // 至于为什么要在App.vue页面里传入而不是直接在组件里获取是因为一会这个页面上其他的页面也需要被传入seller数据进去，省了重复太多次获取了。-->
    <v-header></v-header>
  </div>
</template>

<script>
// 第一步 引入组件， 注意组件变量最好用驼峰表示法表示
import VHeader from 'components/v-header/v-header'

export default {
  name: 'app',
  components: {
    // 第二步，在组件里声明一下，然后这个组件就可以在app里用了
    VHeader
  }
}
</script>
<style lang="stylus"></style>

```

引入完成后如果没有问题的话应该是下面这个亚子

![image-20190603151422807](/Users/wangzirui/Library/Application Support/typora-user-images/image-20190603151422807.png)

### 2.3引入axios来获取数据

为什么不用vue-rousoure来获取数据呢？因为尤于溪都让推荐用axios了，所以就不用vue-rousoure了。可以在node里用。

首先在项目根目录里 npm i -S axios

然后新建一个api文件夹来封装关于axios的方法。

![image-20190603160038943](/Users/wangzirui/Library/Application Support/typora-user-images/image-20190603160038943.png)

```javascript
// helpers.js

import axios from 'axios'

const ERR_OK = 0

export function get (url) {
  return function (params) {
    return axios
      .get(url, {
        params
      })
      .then(res => {
      // 这里的这个data不是data.json里的内容里的data，而是res的属性data，表示的是包含data.json的所有内容，const{errno,data}表示的是ES6里的自动匹配的。
        const { errno, data } = res.data
        if (errno === ERR_OK) {
          return data
        }
      })
      .catch(e => {})
  }
}

// index.js
// import {} 表示的是导入了一个方法
import { get } from './helpers'

const getSeller = get('api/seller')

export { getSeller }

```

定义完这个请求方法过后就可以在App.vue里导入这个getSeller方法了

```javascript
<template>
  <div id="app">
   // 绑定的数据就是这样绑定的
    <v-header :seller="seller"></v-header>
  </div>
</template>

<script>

import VHeader from 'components/v-header/v-header'
//这步是新加进来的，前提调用这个'api‘需要在vue.config.js 配置一下
import { getSeller } from 'api'

export default {
  name: 'app',
  data () {
    return {
      // 先初始话一下数据
      seller: {}
    }
  },
  //运用钩子函数在创建页面时获取数据，因为在get方法中已经获取到了数据，所以在getseller这个方法中不需要在确定是否拿到数据了，只要有then那就一定是拿到了。然后把数据给seller这个对象，在在template中给子组件绑定一下数据。
  created () {
    getSeller().then(seller => {
      this.seller = seller
    })
  },
  components: {
   
    VHeader
  }
}
</script>
<style lang="stylus"></style>

```

```javascript
// vue.config.js 配置'api'如下
chainWebpack (config) {
    config.resolve.alias
      .set('components', resolve('src/components'))
      .set('common', resolve('src/common'))
      .set('api', resolve('src/api'))
  }
```

都配置完了，运行下

![image-20190603161510890](/Users/wangzirui/Library/Application Support/typora-user-images/image-20190603161510890.png)

效果是这个样子说明刚才的整的都对了。

但是在这个结束之前还有点要优化的，就是你可以在api/index.js文件里再添加一些东西。

```javascript
// api/index.js
import { get } from './helpers'

const getSeller = get('api/seller')
const getGoods = get('api/goods')
const getRatings = get('api/ratings')
export { getSeller, getGoods, getRatings }

```

另外在App.vue中也可以添加一波

```javascript
created () {
  // 这样业务逻辑更清晰
    this._getGoods()
  },
  methods: {
    _getGoods () {
      getSeller().then(seller => {
        this.seller = seller
      })
    }
  },
```

然后就没啥问题了。

### 2.4 引入header-detail组件 

引入 star 组件和header-detail组件

![image-20190603194156068](/Users/wangzirui/Library/Application Support/typora-user-images/image-20190603194156068.png)

这个star其实和上面那个support-ico理念差不多，都是通过props传值，然后通过传过来的值计算出相应的计算属性当成class，然后再CSS定义出相应class的图片。

然后header-detail组件也没什么好讲的，然后按照标准流程把他放到App.vue里

```javascript
<template>
  <div id="app">
    // 在这把seller绑定一下然后把获取过来的seller整进来。
    <header-detail :seller="seller"></header-detail>
  </div>
</template>

<script>
import HeaderDetail from 'components/header-detail/header-detail'
// ...
components: {
  // ...
    HeaderDetail
  }
</script>
```

现在添加的都是静态的，接下来把交互搞一下





### 2.5 header-detail添加交互

因为样式会被影响，所以在组件内部添加新组件不太好，样式容易变形，所以最好就是类似于全屏弹出式的要挂在到整个App.vue上比较好。那么怎么挂载到App.vue呢？cube-ui有一个createAPI的方法，可以用调用函数的方法把组件调用挂载到App.vue上。

#### 2.5.1createAPI

首先在src根目路新建一个register.js文件

```javascript
// register.js
// 导入createAPI方法
import { createAPI } from 'cube-ui'
// 导入Vue
import Vue from 'vue'
// 导入需要用creatAPI方法创建的组件
import HeaderDetail from 'components/header-detail/header-detail'

createAPI(Vue, HeaderDetail)

```

然后写好了register.js文件，再入口文件里Import他

`import './register'`

加一行这个就行了。然后再App.vue里加入的header-detail组件就可以移除掉了。 

```javascript
<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <!-- <header-detail :seller="seller"></header-detail> -->
  </div>
</template>
<script>
// 第一步 引入组件， 注意组件变量最好用驼峰表示法表示
import VHeader from 'components/v-header/v-header'
// import HeaderDetail from 'components/header-detail/header-detail'
import { getSeller } from 'api'

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
    VHeader
    // HeaderDetail
  }
}
</script>
<style lang="stylus"></style>

```

下一步就可以在v-header里绑定事件调用方法来创建header-detail组件

刚才只是在main.js里导入了注册过的createAPI方法，该方法在 Vue 的 prototype 上增加一个名为 `$create{camelize(Component.name)}` 的方法，这样就可以在其他组件中直接通过 `const instance = this.$createAaBb(config, [renderFn, single])` 这样来实例化组件了，而且这个实例化组件的元素是被附加到 `body` 元素下的。

camelize(Component.name)是表示把字符串变成驼峰形式所以如果header-detail就会变成HeaderDetail，所以调用这个方法就是this.$createHeaderDetail

**配置参数 config：**

默认所有的值都会当做 props，但是要排除 createAPI 传入的 `events` 中的事件（默认会做转换，例如：`events` 的值为 `['click']`，那么 `config` 中的 `onClick` 就是作为 `click` 事件的回调函数，而不是作为 props 传递给组件）。

1.8.0 版本后 `config` 中可以直接设置 `$props` 和 `$events`，`$props` 中的值是**响应式**的，自动监控当前实例化上下文（即 `this.$createXx` 中的 `this`）的上对应的属性值：

| 参数    | 说明                   | 类型   | 默认值 | 示例                                                   |
| :------ | :--------------------- | :----- | :----- | :----------------------------------------------------- |
| $props  | 传递给组件的 Props     | Object | -      | { title: 'title', content: 'my content', open: false } |
| $events | 组件的 Events 事件回调 | Object | -      | { click: 'clickHandler', select: this.selectHandler }  |

```javascript
<template>
  <div class="header" @click="showDetail">
    //...
  </div>
</template>

<script type="text/ecmascript-6">
export default {
  name: 'v-header',
  props: {
    seller: {
      type: Object,
      default () {
        return {}
      }
    }
  },
  methods: {
    // 这里是新定义的showDetail方法
    showDetail () {
      // 创建一个组件 如果有他就缓存 或者直接用createAPI提供的方法创建一个新的组件然后用this.headerDetailComp接着。
      this.headerDetailComp =
        this.headerDetailComp ||
        this.$createHeaderDetail({
        	// 这里是config参数，也是按照上面的文档添加参数。
          $props: {
            seller: 'seller'
          }
        })
      //既然提到show这个函数，那么就应该在headerDetail组件里定义相应的方法来实现show或者是不show
      this.headerDetailComp.show()
    }
  },
  components: {
    SupportIco
  }
}
</script>

```

上面最后调用show方法，所以在组件里定义一个show方法,然后再根据按钮来定义一个关闭按钮。开始不用定义按钮哈，在调用父组件的showdetail里直接调用了header-detail的show方法了，所以只需要一个关闭按钮。

```javascript
// header-detail.vue
<template>
  <transition name="fade">
    <div v-show="visible" class="header-detail" @touchmove.stop.prevent>
     //....
      <div class="detail-close" @click="hide">
        <i class="icon-close"></i>
      </div>
    </div>
  </transition>
</template>
<script>
data () {
    return {
      visible: false
    }
  },
  methods: {
    show () {
      this.visible = true
    },
    hide () {
      this.visible = false
    }
  }
</script>
```

### 2.6 tab组件引入

之前用的vue-router,重构的项目用cube-ui的tabebar组件

但是如果想要做成上面有标签，底下滑动的效果的话，就需要上面是tabebar组件，下面是side组件，但是如果只是摆上去的话，他们两个不会有联动。

```javascript

<template>
  <div class="tab">
    <cube-tab-bar
      :showSlider="true"
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
      >
        <cube-slide-item>
          <goods></goods>
        </cube-slide-item>
        <cube-slide-item>
          <seller></seller>
        </cube-slide-item>
        <cube-slide-item>
          <ratings></ratings>
        </cube-slide-item>
      </cube-slide>
    </div>
  </div>
</template>

<script>
// 把三个slide 里的三个组件导入进来
import Goods from 'components/goods/goods'
import Seller from 'components/seller/seller'
import Ratings from 'components/ratings/ratings'
export default {
  name: 'tab',

  data () {
    return {
      // 这个index就是个索引，通过这个index来协助selectedLabel来计算获得当前页面。
      index: 0,
      // 配合cube-tab-bar中的:data="tabs"属性，来配置
      tabs: [{ label: '商品' }, { label: '评价' }, { label: '商家' }]
    }
  },
  computed: {
    //这个属性就是通过index来通过设置v-model
    selectedLabel: {
      get () {
        return this.tabs[this.index].label
      },
      set (newVal) {
        // 因为index是响应式的，所以这个用在slide的index会随着cube-tab-bar的选项的改变而改变。从而让tab-bar能控制下面的slide。
        this.index = this.tabs.findIndex(value => {
          return value.label === newVal
        })
      }
    }
  },
  methods: {},
  components: {
    //设置三个导入的组件
    Goods,
    Seller,
    Ratings
  }
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

```

现在的进度是可以用tabbar来控制下面的slide，而slide不能实时的反馈给tabbar，所以根据cube-ui的文档我们知道slide有一个onchang方法，可以监听当前的轮播图的索引。代码如下。

```javascript
<div class="slide-wrapper">
      <cube-slide
        :loop="false"
        :auto-play="false"
        :show-dots="false"
        :initial-index="index"
        ref="slide"
        @change="onChange"
      >
        //...
      </cube-slide>
    </div>
<script>
// ...
  methods: {
    onChange (current) {
      this.index = current
    }
  }
// ... 
</script>

```

根据current来设置index，再通过计算属性，确定tab-bar的选项。

现在可以通过slide来确定tab-bar了但是还想优化体验的话需要借助slide的另一个事件

```javascript
<div class="slide-wrapper">
      <cube-slide
        :loop="false"
        :auto-play="false"
        :show-dots="false"
        :initial-index="index"
        ref="slide"
        @change="onChange"
				@scroll="onScroll"
      >
        //...
      </cube-slide>
    </div>
<script>
// ...
  methods: {
    onChange (current) {
      this.index = current
    },
    onScroll(pos){
        console.log(pos.x)
    }
  }
// ... 
</script>


```

但是现在并不能够监听到scroll事件，是因为没有给这个slide组件配置好。

```javascript
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

 //...       
 data () {
    return {
     //...
      scrollOptions: {
        listenScroll: true,
        probeType: 3
      }
    }
  }
```

这样就可以配置好了，然后再给他添加动画效果

```javascript
 methods: {
    onChange (current) {
      this.index = current
    },
    onScroll (pos) {
      console.log(pos.x)
      // 通过在template上加的ref属性，可以在script里通过this.$refs.tabBar来获取到dom元素，然后通过$el来取得元素，然后再clientWidth再获取元素的宽度
      const tabbarBarWidth = this.$refs.tabBar.$el.clientWidth
      // 这个是获取到了slideDOM元素，然后通过里面的slide.scrollerwidth来获取滑块的宽度。
      const slideWidth = this.$refs.slide.slide.scrollerWidth
      const transform = (-pos.x / slideWidth) * tabbarBarWidth
      // 这个是通过tabBar自带的方法来控制它的滑动
      this.$refs.tabBar.setSliderTransform(transform)
    }
  },
```

另外由于本身tab-bar自带个滑动，和我们设置的onScorll冲突，要把默认的tab-bar滚动清除，所以要在tabbar组件的props里添加 `   :useTranstion="false" `这个东西。就好了

#### 2.6.1 对tab组件进行抽象

首先对tabs数据进行传入，而不是固定写死的。

```javascript
props: {
    tabs: {
      type: Array,
      defalut () {
        return {}
      }
    }
  },
  data () {
    return {
      index: 0,
      // tabs: [{ label: '商品' }, { label: '评价' }, { label: '商家' }],
      scrollOptions: {
        listenScroll: true,
        probeType: 3
      }
    }
  },
```

然后针对传入的组件也不是一一导入的形式并且写死的格式，

```javascript
<cube-slide-item v-for="(tab,index )in tabs" :key="index">
	<component :is="tab.component" :data="tab.data"></component>
</cube-slide-item>
//...
// import Goods from 'components/goods/goods'
// import Seller from 'components/seller/seller'
// import Ratings from 'components/ratings/ratings'
// components: {
  //   Goods,
  //   Seller,
  //   Ratings
  // }
```

但是这么一来就缺数据了，所以需要从App.vue来传这些东西。

现在来操作App.vue来定义数据

```javascript
<template>
  <div id="app">
    <div class="tap-wapper">
      // 这步是将数据传送给子组件的
      <tab :tabs="tabs"></tab>
    </div>
  </div>
</template>

<script>
// 这步是将组件导入进来
import Goods from 'components/goods/goods'
import Seller from 'components/seller/seller'
import Ratings from 'components/ratings/ratings'


export default {
  computed: {
    tabs () {
      return [
        {
          label: '商品',
          components: Goods,
          data: {
            seller: this.seller
          }
        },
        {
          label: '评价',
          components: Ratings,
          data: {
            seller: this.seller
          }
        },
        {
          label: '商家',
          components: Seller,
          data: {
            seller: this.seller
          }
        }
      ]
    }
  }


}
</script>


```

### 2.7 商品组件开发

商品组件一个很重要的框架用cube-ui里的ScrollNav里的组件就OK了。

```javascript
<template>
  <div class="goods">
    <div class="scroll-nav-wrapper">
      // 下面这个是scroll-nav，然后提供的默认插槽是scroll-nav-panel
      // 其实这个组件大概的结构就是cube-scroll > cube-secroll-nav-panel > ul > li > 内容 
      <cube-scroll-nav :side="true" :data="goods" :options="scrollOptions" v-if="goods.length">
        <cube-scroll-nav-panel
          v-for="good in goods"
          :key="good.name"
          :label="good.name"
          :title="good.name"
        >
          <ul>
            <li
              @click="selectFood(food)"
              v-for="food in good.foods"
              :key="food.name"
              class="food-item"
            >
              <div class="icon">
                <img width="57" height="57" :src="food.icon">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span>
                  <span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span>
                  <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
              </div>
            </li>
          </ul>
        </cube-scroll-nav-panel>
      </cube-scroll-nav>
    </div>
  </div>
</template>
<script>
import { getGoods } from 'api'

export default {
  name: 'goods',
  props: {
    data: {
      type: Object,
      default () {
        return {}
      }
    }
  },
  data () {
    return {
      goods: [],
      selectedFood: {},
      scrollOptions: {
        click: false,
        directionLockThreshold: 0
      }
    }
  }
}
</script>
```

静态页面现在写好了，但是由于没有数据，根据`      <cube-scroll-nav :side="true" :data="goods" :options="scrollOptions" v-if="goods.length">`后面的v-if ，因为goods现在没有，所以length也没有，所以导致v-if=false，所以是渲染不出来任何东西的。现在就要导入数据。

导入数据就要涉及到合适的导入时机，其实应该在当tabbar切换到goods的时候再获取，所以这时候就要到tab.vue这个组件里去获取数据来初始化。那么就现在goods里面定义一个函数来获取数据，然后不搭配goods的钩子函数，而是用tab组件里的onchang方法里去触发。

```javascript
// goods.vue
methods: {
    fetch () {
      getGoods().then(goods => {
        this.goods = goods
      })
    }
  }


```

还有一个问题，怎么在tab.vue里的onchange事件里去确定这个组件是啥，就给遍历的components里加一个ref属性绑定一下，然后获取到那个components里面是否有fetch方法，有就执行，没有就不执行。

```javascript
// tab.vue
<cube-slide-item v-for="(tab,index) in tabs" :key="index" ref="component">
          <component :is="tab.component" :data="tab.data"></component>
        </cube-slide-item>
//script
methods: {
    onChange (current) {
      this.index = current
      const component = this.$refs.component[current]
      component.fetch && component.fetch()
    }
  }
```

现在基本上就做好了

#### 2.7.1购物车组件开发

先把shop-cart.vue放到组件文件夹里

然后将购物车组件挂载到goods.vue组件里

```javascript
// shop-cart.vue

<template>
  <div>
    <div class="shopcart">
      <div class="content">
        <div class="content-left">
          <div class="logo-wrapper">
            <div class="logo" :class="{'highlight':totalCount>0}">
              <i class="icon-shopping_cart" :class="{'highlight':totalCount>0}"></i>
            </div>
            <div class="num" v-show="totalCount>0"></div>
          </div>
          <div class="price" :class="{'highlight':totalPrice>0}">￥{{totalPrice}}</div>
          <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
        </div>
        <div class="content-right">
          <div class="pay" :class="payClass">{{payDesc}}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'shop-cart',
  props: {
    selectFoods: {
      type: Array,
      default () {
        return []
      }
    },
    deliveryPrice: {
      type: Number,
      default: 0
    },
    minPrice: {
      type: Number,
      default: 0
    }
  },
  computed: {
    totalPrice () {
      let total = 0
      this.selectFoods.forEach(food => {
        total += food.price * food.count
      })
      return total
    },
    totalCount () {
      let count = 0
      this.selectFoods.forEach(food => {
        count += food.count
      })
      return count
    },
    payDesc () {
      if (this.totalPrice === 0) {
        return `￥${this.minPrice}元起送`
      } else if (this.totalPrice < this.minPrice) {
        let diff = this.minPrice - this.totalPrice
        return `还差￥${diff}元起送`
      } else {
        return '去结算'
      }
    },
    payClass () {
      if (!this.totalCount || this.totalPrice < this.minPrice) {
        return 'not-enough'
      } else {
        return 'enough'
      }
    }
  }
}
</script>
```

####2.7.2多级组件的传值

很简单的一个组件的页面，把他挂载到goods.vue上就好了，主要强调一下这个数据的传输，因为没有使用vuex，所以只能一层一层的传递数据，就是props一层一层的传，这个逻辑不是特别复杂，所以就没用vuex

```javascript
// 首先这个数据是哪来的呢？
// 从api里的helper里调用axios函数获取的

import axios from 'axios'

const ERR_OK = 0

export function get (url) {
  return function (params) {
    return axios
      .get(url, {
        params
      })
      .then(res => {
        const { errno, data } = res.data
        if (errno === ERR_OK) {
          return data
        }
      })
      .catch(e => {})
  }
}

// 然后通过api/idnex.js定义了一下参数，然后把他们暴露（export）出来。
import { get } from './helpers'

const getSeller = get('api/seller')
const getGoods = get('api/goods')
const getRatings = get('api/ratings')
export { getSeller, getGoods, getRatings }
// 再在App.vue里引用这个api/index.js里就获取到了那个暴露出来的方法
// App.vue
import { getSeller } from 'api'
// 再找一个时机给这个方法调用出来，获取到的result付给data里的某个值
// App.vue
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
// 然后将已被赋值的seller传递给别的计算属性，然后通过绑定的方法将数值传递给下一个子组件
 // App.vue
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
    
// 组件中
// App.vue
  <div class="tap-wapper">
      <tab :tabs="tabs"></tab>
    </div>
//  这样就穿进去了，然后再tab组件里看一下怎么接受的，然后怎么给goods组件传过去的
//tab.vue
props: {
    tabs: {
      type: Array,
      defalut () {
        return []
      }
    }
  },
//这样这个tabs就可以用了，tabs是一个数组，对象里有三个属性，分别是label,component,data，data是个对象，它里面有App.vue获取到的seller
 // tab.vue
 <cube-slide-item v-for="(tab,index) in tabs" :key="index">
          <component :is="tab.component" :data="tab.data" ref="component"></component>
        </cube-slide-item>
// 这是一个遍历的组件，但是也可以看出来绑定的属性们其实都挺有依据的，然后看第一个遍历的good组件里怎么接受绑定的data数据的。
// goods.vue
props: {
    data: {
      type: Object,
      default () {
        return {}
      }
    }
  },
// 然后再通过计算属性返回一下
    computed: {
    seller () {
      return this.data.seller
    }
  },
// 最后把他给了
     <shop-cart :deliveryPrice="seller.deliveryPrice" :minPrice="seller.minPrice"></shop-cart>
```

#### 2.7.3 商品加减按钮

按钮组件如下

```html
<template>
  <div class="cartcontrol">
    <transition name="move">
      <div class="cart-decrease" v-show="food.count>0" @click.stop="decrease">
        <span class="inner icon-remove_circle_outline"></span>
      </div>
    </transition>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop="add"></div>
  </div>
</template>
```



```javascript

<script>

export default {
  name: 'cart-control',
  props: {
    food: {
      type: Object
    }
  },
  methods: {
    add (event) {
      if (!this.food.count) {
        this.$set(this.food, 'count', 1)
      } else {
        this.food.count++
      }
    },
    decrease () {
      if (this.food.count) {
        this.food.count--
      }
    }
  }
}
</script>


```

其中给对象新增加属性的话用`  this.$set(this.food, 'count', 1)`

这时候在goods.vue里可以给cartControl绑定food参数了，然后给shop-cart传一个selectFoods

```javascript
 <shop-cart
        :selectFoods="selectFoods"
        :deliveryPrice="seller.deliveryPrice"
        :minPrice="seller.minPrice"
      >
</shop-cart>
computed: {
    seller () {
      return this.data.seller
    },
    selectFoods () {
      let ret = []
      this.goods.forEach(good => {
        good.foods.forEach(food => {
          if (food.count) {
            ret.push(food)
          }
        })
      })
      return ret
    }
  },


```

#### 2.7.4 小球动画

首先小球动画要获得到小球的位置，所以要从cart-control提交出来按钮的元素，给good页面，就是在goods页面里给cart-control组件定义一个方法，这个方法可以接收到cart-control里提交出来的元素。然后再给shop-cart定义一个drop方法，然后再cart-control里定义的方法调用drop方法

```javascript
// cart-control.vue
const EVENT_ADD  = 'add'
methods: {
    add (event) {
      if (!this.food.count) {
        this.$set(this.food, 'count', 1)
      } else {
        this.food.count++
      }
      this.$emit(EVENT_ADD, event.target)
}
// goods.vue
   <cart-control @add="onAdd" :food="food"></cart-control>
//...
// 这个传过来的el是event.target
onAdd (el) {
   this.$refs.ShopCart.drop(el)
}
// shop-art.vue
methods: {
  drop (el) {}
}
   
```

现在定义完了方法还需要有小球来表现出来动画，所以我们得新建n个小球，怎么新建小球呢？用div呗，但是用div的话不能很好的控制球的数量，所以我们要建一个创建球的辅助函数，要给每个求对象一个属性，show:false

```javascript
// shop-cart.vue
const BALL_LEN = 10
function creatBalls () {
  let ret = []
  for (let i = 0; i < BALL_LEN; i++) {
    ret.push({
      show: false
    })
  }
  return ret
}

data () {
    return {
      balls: creatBalls()
    }
  }
```

现在球是一个数组，每个数组都有一个show:false的属性，然后我们在template里面v-for渲染一下这些球

```html
 <div class="ball-container">
        <div v-for="(ball,index )in balls" :key="index">
          <transition>
            <div class="ball" v-show="ball.show"></div>
          </transition>
        </div>
      </div>
```

其实也不是渲染，就是建了很多div，然后用css把他们做成小球的样子。

```html
<div class="ball-container">
        <div v-for="(ball,index )in balls" :key="index">
          <transition>
            <div class="ball" v-show="ball.show">
              <div class="inner inner-hook"></div>
            </div>
          </transition>
        </div>
      </div>
```

因为这个过度动画有一个平移，有一个旋转，所以给他一个inner ，来做旋转，外侧的用来做平移。

调用drop的时候，其实是调动transtion的动画，怎么用drop调用的呢？

就是在调用cart-control的时候，会调用drop，然后drop会把ball给dropballs数组，入党dropballs有的时候，就会被befroeDrop的动画钩子函数获取到，然后就可以开始动画了。

```javascript
created () {
    this.dropBalls = []
  },
  methods: {
    drop (el) {
      for (let i = 0; i < this.balls.length; i++) {
        const ball = this.balls[i]
        if (!ball.show) {
          ball.show = true
          ball.el = el
          this.dropBalls.push(ball)
          return
        }
      }
    },
```

```html
 <div v-for="(ball,index) in balls" :key="index">
          <transition
            v-on:before-enter="beforeDrop"
            v-on:enter="dropping"
            v-on:after-enter="afterDrop"
          >
            <div class="ball" v-show="ball.show">
              <div class="inner inner-hook"></div>
            </div>
          </transition>
        </div>
```

```javascript
// 底下的el 都是指<transtion>标签里的ball div元素 
beforeDrop (el) {
      const ball = this.dropBalls[this.dropBalls.length - 1]
      const rect = ball.el.getBoundingClientRect()
      const x = rect.left - 32
      const y = -(window.innerHeight - rect.top - 22)
      el.style.display = ''
      el.style.transform = el.style.webkitTransform = `translate3d(0,${y}px,0)`
      const inner = el.getElementsByClassName(innerClsHook)[0]
      inner.style.transform = inner.style.webkitTransform = `translate3d(${x}px,0,0)`
    },
    dropping (el, done) {
      // 这句是清屏用的
      this._reflow = document.body.offsetHeight
      el.style.transform = el.style.webkitTransform = `translate3d(0,0,0)`
      const inner = el.getElementsByClassName(innerClsHook)[0]
      inner.style.transform = inner.style.webkitTransform = `translate3d(0,0,0)`
      el.addEventListener('transitionend', done)
    },
    afterDrop (el) {
      const ball = this.dropBalls.shift()
      if (ball) {
        ball.show = false
        el.style.display = 'none'
      }
    }
```

然后就可以为所欲为了，点cart-control就可以调用动画了。

总结一下：在cart-control提交一个事件给goods，然后goods接受事件，然后顺便把按钮的元素也给拿过来，在goods里调用shop-cart的drop方法，然后drop方法同时也接受按钮的元素，然后添加到drop球数组里，drop球数组里有数据的话就会调用动画钩子，然后动画通过getboundingclientrect这个方法获取到他的位置，然后让球div跳转到原来 自己的位置，整个事件就弄好了。

#### 2.7.5 侧边栏的定制化

根据cube-ui里scrollnav里提供的插槽，侧边栏可以用scrollnavbar来搞一搞，然后scrollnavbar也支持插槽，总之可定义的部分很多，很全面。

现在vue里 slot=""和slot-scope这两个属性被删除了，取而代之的是v-slot: 和v-bind="props"或者v-slot:default="props"这两个属性，第一个代表slotname。第二各代表穿作用域。

下面这个是结构的，person代表user

```javascript
<current-user v-slot="{ user: person }">
  {{ person.firstName }}
</current-user>
```

跟 `v-on` 和 `v-bind` 一样，`v-slot` 也有缩写，即把参数之前的所有内容 (`v-slot:`) 替换为字符 `#`。例如 `v-slot:header` 可以被重写为 `#header`

上面的都是新标准，原来那种的不要记了。废弃了

在想定义侧边栏的时候，cube-ui的scrollNav组件提供了bar插槽，需要自定义导航结构的话使用，里边放置 cube-scroll-nav-bar 组件

```html
<cube-scroll-nav :side="true" :data="goods" :options="scrollOptions" v-if="goods.length">
        <template v-slot:bar="props">
          <cube-scroll-nav-bar
            direction="vertical"
            :current="props.current"
            :labels="props.labels"
            :txts="props.txts"
          ></cube-scroll-nav-bar>
        </template>
//....
```

大概上面这样子，但是txts不要使用props提供的，因为只有一个，我们要穿很多东西进去，所以我们需要构造一个东西。

```javascript
//计算属性
barTxts () {
      let ret = []
      this.goods.forEach(good => {
        const { foods, type, name } = good
        let count = 0
        foods.forEach(food => {
          count += food.count || 0
        })
        ret.push({
          name,
          type,
          count
        })
      })
      return ret
    }
```

然后把txts换成barTxts,然后下面的v-slot=""的作用域就变成了barTxts了。然后我们引入两个组件，分别是bubble和support-ico组件，在cube-srcoll-nav-bar里使用。

```html
<div class="side-container">
  <cube-scroll-nav-bar
    direction="vertical"
    :current="current"
    :labels="labels"
    :txts="txts"
    @change="changeHandler">
    <i slot-scope="props">{{props.txt}}</i>
  </cube-scroll-nav-bar>
</div>

```

上面设计cube-ui给提供示例，表示了如果你给他传过来一个txts的活，里面必须有个东西接着，然后带这个props，在这个东西里必须调用Props.txt才能调用里面的数据。所以在项目中同理

```html
<cube-scroll-nav :side="true" :data="goods" :options="scrollOptions" v-if="goods.length">
        <template v-slot:bar="props">
          <cube-scroll-nav-bar
            direction="vertical"
            :current="props.current"
            :labels="props.labels"
            :txts="barTxts"
          >
            <template v-slot="props">
              <div class="text">
                <support-ico v-if="props.txt.type>=1" :size="3" :type="props.txt.type"></support-ico>
                <span>{{props.txt.name}}</span>
                <span class="num" v-if="props.txt.count">
                  <bubble :num="props.txt.count"></bubble>
                </span>
              </div>
            </template>
          </cube-scroll-nav-bar>
        </template>
```

侧边栏就这样做好了。感觉如果多看看cube-ui的文档的话这些东西就不会很难懂。

#### 2.7.6购物车列表组件

```html
<template>
  <transition name="fade">
    <cube-popup
      :mask-closable="true"
      v-show="visible"
      position="bottom"
      type="shop-cart-list"
      :z-index="90"
    >
      <transition name="move" @after-leave="afterLeave">
        <div v-show="visible">
          <div class="list-header">
            <h1 class="title">购物车</h1>
            <span class="empty" @click="empty">清空</span>
          </div>
          <cube-scroll class="list-content" ref="listContent">
            <ul>
              <li class="food" v-for="(food,index) in selectFoods" :key="index">
                <span class="name">{{food.name}}</span>
                <div class="price">
                  <span>￥{{food.price*food.count}}</span>
                </div>
                <div class="cart-control-wrapper">
                  <cart-control @add="onAdd" :food="food"></cart-control>
                </div>
              </li>
            </ul>
          </cube-scroll>
        </div>
      </transition>
    </cube-popup>
  </transition>
</template>

<script>
import CartControl from 'components/cart-control/cart-control'

export default {
  name: 'shop-cart-list',
  props: {
    selectfoods: {
      type: Array,
      dafault () {
        return []
      }
    }
  },
  data () {
    return {
      visible: false
    }
  },
  methods: {
    show () {
      this.visible = false
    },
    hide () {
      this.visible = true
    },
    maskClick () {
      this.hide()
    }
  },
  components: {
    CartControl
  }
}
</script>

<style lang="stylus" scoped>
@import '~common/stylus/variable'
.cube-shop-cart-list
  bottom: 48px
  &.fade-enter, &.fade-leave-active
    opacity: 0
  &.fade-enter-active, &.fade-leave-active
    transition: all 0.3s ease-in-out
  .move-enter, .move-leave-active
    transform: translate3d(0, 100%, 0)
  .move-enter-active, .move-leave-active
    transition: all 0.3s ease-in-out
  .list-header
    height: 40px
    line-height: 40px
    padding: 0 18px
    background: $color-background-ssss
    .title
      float: left
      font-size: $fontsize-medium
      color: $color-dark-grey
    .empty
      float: right
      font-size: $fontsize-small
      color: $color-blue
  .list-content
    padding: 0 18px
    max-height: 217px
    overflow: hidden
    background: $color-white
    .food
      position: relative
      padding: 12px 0
      box-sizing: border-box
      .name
        line-height: 24px
        font-size: $fontsize-medium
        color: $color-dark-grey
      .price
        position: absolute
        right: 90px
        bottom: 12px
        line-height: 24px
        font-weight: 700
        font-size: $fontsize-medium
        color: $color-red
      .cart-control-wrapper
        position: absolute
        right: 0
        bottom: 6px
</style>


```

用了cube-ui的popup组件，上面的是简单的html页面，不多说了。新建完了，因为他是个整体弹出式的东西，所以要用到cube-ui的creatAPI的方法，所以我们要在register引入shop-cart-list组件，然后注册这个方法，让我们能在shop-cart组件里调用它。下面是实现方法。

首先给shop-cart-list定义一下出现消失的方法，完全依靠v-show的visible参数

```javascript
  methods: {
    onAdd (target) {
      this.$emit(EVENT_ADD, target)
    },
    show () {
      this.visible = true
    },
    // 这和个maskClick是pop-up组件中提供的props绑定的
    maskClick () {
      this.hide()
    },
    hide () {
      this.visible = false
    }
  },
```

```html
// 那个mask-click就是绑定的上面的方法。
<cube-popup
      :mask-closable="true"
      v-show="visible"
      position="bottom"
      @mask-click="maskClick"
      type="shop-cart-list"
      :z-index="90"
    >
```

定义完了shop-cart-list的出现消失的方法以后要在cart里触发，所以上来给cartcontent的div绑上一个方法。

```html
 <div class="content" @click="toggleList">
```

然后在script定义一下这个方法。

```javascript
 toggleList () {
      if (this.listFold) {
        if (!this.totalCount) {
          return
        }
        this.listFold = false
        this._showShopCarList()
      } else {
        this.listFold = true
        this._hideShopCartList()
      }
    },
    _showShopCarList () {
      this.shopCartListComp =
        this.shopCartListComp ||
        this.$createShopCartList({
          $props: {
            selectFoods: 'selectFoods'
          },
        })
      this.shopCartListComp.show()
    },
    _hideShopCartList () {
      this.shopCartListComp.hide()
    }
```

然后就定义好了，不过有个问题，就是现在点击遮罩层的话，不会让foldlist= ture，所以必须在cartlist里的hide里提交一个事件

```javascript
  hide () {
      this.visible = false
      this.$emit(EVENT_HIDE)
    }
```

然后让createAPI接着

```javascript
 _showShopCarList () {
      this.shopCartListComp =
        this.shopCartListComp ||
        this.$createShopCartList({
          $props: {
            selectFoods: 'selectFoods'
          },
          $events: {
            hide: () => {
              this.listFold = true
            }
          }
        })
```

事情就这样搞好了。然后再去点遮罩层的话，就会很不错的实现效果了。

不过另外一点，就是craeteAPI创造的实例是挂载到APP.vue里面去的，所以势必比在goods页面上面，所以我们在挂载shopcartlist组件得时候，也要挂载一个shopcart出来，要不然违和感太强烈了。因为我们的shopcart有一块是凸起来的，会被盖住。

####2.7.7shopcartsticky

```html
<template>
  <div class="shop-cart-sticky" v-show="visible">
    <shop-cart
      ref="shopCart"
      :selectFoods="selectFoods"
      :deliveryPrice="deliveryPrice"
      :minPrice="minPrice"
    ></shop-cart>
  </div>
</template>

<script>
import ShopCart from 'components/shop-cart/shop-cart'

export default {
  name: 'shop-cart-sticky',
  data () {
    return {
      visible: false
    }
  },
  props: {
    selectFoods: {
      type: Array,
      default () {
        return []
      }
    },
    deliveryPrice: {
      type: Number,
      default: 0
    },
    minPrice: {
      type: Number,
      default: 0
    },

    list: {
      type: Object,
      default () {
        return {}
      }
    }
  },
  methods: {
    show () {
      this.visible = true
    },
    hide () {
      this.visible = false
    }
  },
  components: {
    ShopCart
  }
}
</script>

<style lang="stylus" scoped>
.shop-cart-sticky
  position: absolute
  left: 0
  bottom: 0
  z-index: 999
  width: 100%
  height: 48px
</style>

```

然后把组件吧他用createAPI注册一下。然后在togglelist()方法调用_showshopcartlist方法的同时调用_showShopcartSticky方法

```javascript
toggleList () {
      if (this.listFold) {
        if (!this.totalCount) {
          return
        }
        this.listFold = false
        this._showShopCarList()
        // 这个是要点
        this._showShopCartSticky()
      } else {
        this.listFold = true
        this._hideShopCartList()
      }
    },
  _showShopCartSticky () {
      this.shopCartStickyComp =
        this.shopCartStickyComp ||
        this.$createShopCartSticky({
          $props: {
            selectFoods: 'selectFoods',
            deliveryPrice: 'deliveryPrice',
            minPrice: 'minPrice'
          }
        })
      this.shopCartStickyComp.show()
    },
```



现在可以弹出来了，但是有一个问题，就是，这个有bug，就是一般把list呼出来的时候，listFOLD都会变成false，然而当现在把sticky组件叫出来的时候他因为在里面定义了一个listfold始终未true，所以你可以无限点这个shopcartsticky，从而让他无限叠加。所以我们要把这个listfold变成一个Porps传进去.

```javascript
// shop-cart.vue
    fold: {
      type: Boolean,
      default: true
    }
//把传进来的fold用listfold接着
data () {
    return {
      balls: creatBalls(),
      listFold: this.fold
    }
  },
// 然后再把listfold传给创建的shopcartsticky
    _showShopCartSticky () {
      this.shopCartStickyComp =
        this.shopCartStickyComp ||
        this.$createShopCartSticky({
          $props: {
            selectFoods: 'selectFoods',
            deliveryPrice: 'deliveryPrice',
            minPrice: 'minPrice',
            //就这个穿进去
            fold: 'listFold'
          }
        })
      this.shopCartStickyComp.show()
    },
```

然后再shop-cart-sticky里

```javascript
 		fold: {
      type: Boolean,
      default: true
    },
    list: {
      type: Object,
      default () {
        return {}
      }
    }
```

这样通过props就可以猥琐的传递这个fold了，就可以不无线创建了，但是，有个问题，关的时候，关的是        this.shopCartListComp  是shop-cart组件的，shop-cart-sticky没有这个东西，所以新建完了蹦出来个shop-cart-sticky再关掉就 管不了，因为根本没this.shopCartListComp 这个东西。这个弹窗是shop-cart的。所以我们要把这个弹窗给shop-cart-sticky传过去。然后执行他parent的hide。

这时候在shopcartsticky的props里加个盆，接着这个list

```javascript
// shopcartsticky.vue
list: {
      type: Object,
      default () {
        return {}
      }
    }
```

给sticky新建盆了以后就得在 **$createShopCartSticky**那里面想办法传进来。

```javascript
 this.$createShopCartSticky({
          $props: {
            selectFoods: 'selectFoods',
            deliveryPrice: 'deliveryPrice',
            minPrice: 'minPrice',
            fold: 'listFold',
            list: this.shopCartListComp
          }
        })
```

传进来之后就可以大胆的用this.$parent.list.hide()这个方法了。

```javascript
 _hideShopCartList () {
      this.$parent.list.hide()
   //原来是sticky是调用的这个，可是当时根本没有shopcartlistcomp这个东西，所以就会报错
   // 但是当我们传过来父组件的这个list之后就可以调用上面的list了，但是怎么判断 这个要隐藏哪个呢？
      this.shopCartListComp.hide()
    }
```

所以又要传值

```javascript
props:{
  sticky: {
      type: Boolean,
      default: false
    }
} 

```

在sticky直接绑上去一个sticky=true就行了，然后在_hideShopCartList()里

```javascript
_hideShopCartList () {
      const comp = this.sticky
        ? this.$parent.list.hide()
        : this.shopCartListComp.hide()
      comp.hide && comp.hide()
    }
```

List显示隐藏的问题件解决了，但是没有解决sticky的隐藏，所以我们新建一个方法，来吧sticky隐藏掉，再找个时机调用他。

```javascript
// shop-cart.vue
_hideShopCartSticky () {
      this.shopCartStickyComp.hide()
    }
```

```javascript
this.$createShopCartList({
          $props: {
            selectFoods: 'selectFoods'
          },
          $events: {
            hide: () => {
              this.listFold = true
              //在这里调用
              this._hideShopCartSticky()
            }
          }
```

但是有一个问题，不自然，还有别的bug，很难受。

先解决不自然，然后在解决bug，不自然是因为再点击触发hide的一瞬间sticky就完了，但是List有一段过渡动画，所以往下过度的时候底下sticky没有遮住，所以要让动画完了的时候再然sticky消失。

```javascript
// shop-cart-list.vue
   <transition name="move" @after-leave="onLeave">
// 
onLeave () {
      this.$emit(EVENT_LEAVE)
}
```

在creatAPI里接受这个递交出来的事件

```javascript
 _showShopCarList () {
      this.shopCartListComp =
        this.shopCartListComp ||
        this.$createShopCartList({
          $props: {
            selectFoods: 'selectFoods'
          },
          $events: {
            hide: () => {
              this.listFold = true
              this._hideShopCartSticky()
            },
            leave: () => {
              this._hideShopCartList()
            }
          }
        })
      this.shopCartListComp.show()
    },
```

刚才的不流畅通过钩子函数解决了，然后解决第二次点sticky不管用的毛病。

因为点过以后this.listfold就不会变了，所以我们要监听这个fold，创建一个watch属性。

```javascript
//shopcart.vue
watch: {
    fold (newVal) {
      this.listFold = newVal
    }
  }
```

#### 2.7.8购物车组件的丰富

#####2.7.8.1：cart-list购物车小球的动画

先给shop-cart-list的cart-control添加一个时间，然后让这个时间把add提交出去，顺便带上这个目标，然后在shop-cart里面的的createAPI的events接受，然后调用drop方法就好了

```javascript
// shop-cart-list.vue
<cart-control @add="onAdd" :food="food"></cart-control>
const EVENT_ADD = 'add'
onAdd (el) {
      this.$emit(EVENT_ADD, el)
    }
// shop-cart.vue
_showShopCarList () {
      this.shopCartListComp =
        this.shopCartListComp ||
        this.$createShopCartList({
          $props: {
            selectFoods: 'selectFoods'
          },
          $events: {
            hide: () => {
              this.listFold = true
            },
            leave: () => {
              this._hideShopCartSticky()
            },
            add: el => {
              this.drop(el)
            }
          }
        })
      this.shopCartListComp.show()
    },
```

但是问题没有解决，因为现在调用的都是shop-cart的，现在shop-list-sticky浮在上面呢，所以你看不到底下做的动画，所以要把这个时间传给shop-cart-sticky，然他调用drop方法。

现在 所以要在给shop-cart-sticky.vue提供一个drop方法，因为sticky里面引入了shop-cart组件，所以只需要在他里面调用shop-cart的就行了。

```javascript
// shop-cart-stciky.vue
 methods: {
    drop (el) {
      this.$refs.shopCart.drop(el)
    }
  },
```

然后再shop-cart的createShopCartList里

```javascirpt
_showShopCarList () {
      this.shopCartListComp =
        this.shopCartListComp ||
        this.$createShopCartList({
          $props: {
            selectFoods: 'selectFoods'
          },
          $events: {
            hide: () => {
              this.listFold = true
            },
            leave: () => {
              this._hideShopCartSticky()
            },
            add: el => {
              this.shopCartStickyComp.drop(el)
            }
          }
        })
      this.shopCartListComp.show()
    },
```

#####2.7.8.2滚动问题

获取dom，然后让他刷新

```javascript
// shop-cart-list
show () {
      this.visible = true
      this.$nextTick(() => {
        this.$refs.listContent.refresh()
      })
    },
```

#####2.7.8.3清空问题

清空用到cube-ui的dialog组件，

```html
     <span @click="empty" class="empty">清空</span>
```

```javascript
 empty () {
      this.dialogComp =
        this.dialogComp ||
        this.$createDialog({
          type: 'confirm',
          title: '清空',
          content: '你确认吗?',
          $events: {
            confirm: () => {
              this.selectFoods.forEach(food => {
                food.count = 0
              })
              this.hide()
            }
          }
        })
      this.dialogComp.show()
    }
```

详细的看看文档就行了。

##### 2.7.8.4 商品在列表里减到0自动关闭列表

```javascript
totalCount (newVal) {
      if (!this.listFold && !newVal) {
        this._hideShopCartList()
      }
    }
```

#### 2.7.9 弹层优化

因为createAPI是单例模式，所以会有很多BUG。所以就不缓存了。

createAPI组件都会有两个方法，就是show和hide，他们控制visible的真假，所以当这些东西太冗余的时候我们可以借助mixins

### 2.8商品详情页开发

首先写好商品详情页的静态页面,主要看food组件

如果不用createAPI的话引入方法是在script里面import，然后在components里注册一下。然后给cube-nave-panel里的li标签加一个方法selected(food)记住把food也要传进去。然后根据传进来的food将再新建一个属性叫selectedFood,将传进来的food付给selectedFood，然后再把selectFood传给food组件。那怎么显示food组件呢？就是不是点击li定义的selected方法嘛，可以给food加一个ref，在执行selected方法的同事执行this.$refs.food.show()。这样就可以了，但是因为外面的css会影响内部的，所以我们还是要用createAPI.

createAPI的具体操作就是在register.js注册，然后在方法调用就行了。

