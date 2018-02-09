<template lang="html">
    <div class="">
        <div class="shopCart">
            <div class="content">
                <div class="content-left" @click="listToggle">
                    <div class="logo-wrapper" :class="{'cartlogo2':totalPrice>0}">
                        <div class="badge" v-show="totalCount" :class="{'cartdown':listShow}">{{totalCount}}</div>
                        <!-- <div class="logo" :class="{'active':totalPrice}">
                            <i class="lindeiconcart"></i>
                        </div> -->
                    </div>
                    <div v-if="totalPrice>0" class="price" :class="{'active':totalPrice}">
                        <p>合计：&yen{{totalPrice}}</p>
                        <!-- <p class="small">总共：{{totalCount}}件商品</p> -->
                    </div>
                    <div v-else class="price">未选购商品</div>
                    <!-- <div class="desc">
          另需要配送费￥{{deliveryPrice}}元
        </div> -->
                </div>
                <div @click="goPay" class="content-right" :class="{'enough':totalPrice>minPrice}">
                    {{payDesc}}
                </div>
            </div>
            <div class="ball-container">
                <transition name="drop" v-on:before-enter="beforeEnter" v-on:enter="enter" v-on:after-enter="afterEnter" v-for="(ball,index) in balls">
                    <div class="ball" v-show="ball.show">
                        <div class="inner inner-hook"></div>
                    </div>
                </transition>
            </div>
            <transition name="transHeight">
                <div class="shopcart-list" v-show="listShow">
                    <div class="list-header">
                        <h1 class="title">购物车</h1>
                        <span class="empty" @click="setEmpty()">清空</span>
                    </div>
                    <div class="list-content" ref="foodlist">
                        <ul>
                            <li class="food" v-for="food in selectFoods">
                                <div v-if="food.totalprice">
                                    <span class="name">{{food.name}}</span>
                                    <div class="price">
                                        <span>￥{{(food.totalprice * food.count).toFixed(2)}}</span>
                                    </div>
                                    <div class="cartcontrol-wrapper">
                                        <cartcontrol :food="food"></cartcontrol>
                                    </div>
                                </div>
                                <div style="overflow: hidden" v-else>
                                    <span class="name">{{food.name}}</span>
                                    <div class="price">
                                        <span>￥{{(food.price * food.count).toFixed(2)}}</span>
                                    </div>
                                    <div class="cartcontrol-wrapper">
                                        <cartcontrol :food="food" :selectFoods="selectFoods" @qkgwc="listToggle"></cartcontrol>
                                        <!-- <cartcontrol :food="food"></cartcontrol> -->
                                    </div>
                                </div>
                                <div class="foodother" v-if="food.jcai">
                                  <span v-if="food.kweiname">{{food.kweiname}}&nbsp;</span>
                                  <span v-for="x in food.jcai" v-if="x.count>0">
                                    {{x.name}}<span class="chenghao">×</span>{{x.count}}&nbsp;
                                  </span>
                                </div>
                            </li>
                        </ul>
                    </div>
                </div>
            </transition>
        </div>
        <transition name="fade-backdrop">
            <div class="backdrop" v-show="showBackdrop" @click="hideBackdrop"></div>
        </transition>
    </div>
</template>


<script>
import cartcontrol from 'components/cartcontrol/cartcontrol'
import backdrop from 'components/backdrop/backdrop'
import BScroll from 'better-scroll'

export default {
  props: {
    selectFoods: {
      type: Array,
      default: []
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
  data() {
    return {
      balls: [{
        show: false
      }, {
        show: false
      }, {
        show: false
      }, {
        show: false
      }, {
        show: false
      }],
      dropBalls: [],
      listShow: false
    }
  },
  created() {
    this.$root.eventHub.$on('cart.add', this.drop)
  },
  computed: {
    showBackdrop() {
      if (this.listShow && this.totalPrice) {
        return true
      }
      this.listShow = false
      return false
    },
    totalPrice() {
      let total = 0
      this.selectFoods.forEach((food) => {
        if (food.count) {
          total += food.price * food.count
        }
      })
      return total.toFixed(2)
    },
    totalCount() {
      let count = 0
      this.selectFoods.forEach((food) => {
        count += food.count
      })
      return count
    },
    leftAmount() {
      if (this.minPrice - this.totalPrice > 0 && totalPrice) {
        return true;
      }
      return false
    },
    payDesc() {
      let diff = this.minPrice - this.totalPrice
      if (!this.totalPrice) {
        return '下一步'
        // return `￥${this.totalPrice}起送`
      } else if (diff > 0) {
        // return `还差￥${diff}元`
        return '请点餐'
      } else {
        return '下一步'
      }
    }
  },
  methods: {
    drop(el) {
      for (let i = 0, l = this.balls.length; i < l; i++) {
        let ball = this.balls[i]
        if (!ball.show) {
          ball.show = true
          ball.el = el
          this.dropBalls.push(ball)
          return
        }
      }
    },
    setEmpty() {
      var _this=this;
        layer.open({
            content: '确定清空购物车？',
            btn: ['清空', '取消'],
            yes: function(index) {
                layer.close(index);

        _this.selectFoods.forEach((food) => {
          food.count = 0
        })
        _this.listToggle()


            }
        });

      // var msg = "确认清空购物车?";
      // if (confirm(msg) == true) {
      //   this.selectFoods.forEach((food) => {
      //     food.count = 0
      //   })
      //   this.listToggle()
      // } else {
      //   return false;
      // }

    },
    hideBackdrop() {
      this.listShow = false
    },
    _initScroll() {
      this.foodlistScroll = new BScroll(this.$refs.foodlist, {
        click: true
      });
    },
    listToggle() {
      if (!this.selectFoods.length) {
        return
      }
      this.listShow = !this.listShow
      if (this.listShow) {
        this.$nextTick(() => {
          if (!this.foodlistScroll) {
            this._initScroll()
          } else {
            this.foodlistScroll.refresh()
          }
        })
      }
    },
    goPay() {
      if (this.totalPrice > this.minPrice) {
        var tid=this.tid;
        var newselectFoodsArr = [];
        this.selectFoods.forEach(function(element, index) {
          var newfoodObj = {};
          newfoodObj.meal_id = element.meal_id;
          newfoodObj.price = element.price;
          newfoodObj.count = element.count;
          newfoodObj.kwei = element.kwei
          newfoodObj.kweiname = element.kweiname

          // newfoodObj.parent_meal_id = element.parent_meal_id;
          newfoodObj.jcai = []
          if(element.jcai){
          element.jcai.forEach((jcaiitem, index) => {
            if (jcaiitem.count > 0) {
              let childnodejcaiitem = {}
              childnodejcaiitem.rel_meal_id = jcaiitem.rel_meal_id
              childnodejcaiitem.name = jcaiitem.name
              childnodejcaiitem.price = jcaiitem.price
              childnodejcaiitem.count = jcaiitem.count
              newfoodObj.jcai.push(childnodejcaiitem)
            }
          })
          }

          newselectFoodsArr.push(newfoodObj);
        });

          console.log(newselectFoodsArr)

        $.ajax({
          url: postData.url,
          data: {
            'mer_id': postData.mer_id,
            'store_id': postData.store_id,
            "selectFoodsPos": newselectFoodsArr
          },
          type: "post",
          success: function(json) {
            if (json.code == 1) {
              go_pay();
            }
            if (json.code == 0) {
              alert(json.msg);
            }
          }
        });

      }

    },
    beforeEnter(el) {
      let count = this.balls.length
      while (count--) {
        let ball = this.balls[count]
        if (ball.show) {
          let rect = ball.el.getBoundingClientRect()
          let x = rect.left - 32;
          let y = -(window.innerHeight - rect.top - 22)
          el.style.display = ''
          el.style.webkitTransform = `translate3d(0,${y}px,0)`
          el.style.transform = `translate3d(0,${y}px,0)`
          let inner = el.querySelector('.inner-hook')
          inner.style.webkitTransform = `translate3d(${x}px,0,0)`
          inner.style.transform = `translate3d(${x}px,0,0)`
        }
      }
    },
    enter(el) {
      el.offsetHeight
      this.$nextTick(() => {
        el.style.webkitTransform = 'translate3d(0,0,0)'
        el.style.transform = 'translate3d(0,0,0)'
        let inner = el.querySelector('.inner-hook')
        inner.style.webkitTransform = 'translate3d(0,0,0)'
        inner.style.transform = 'translate3d(0,0,0)'
      })
    },
    afterEnter(el) {
      let ball = this.dropBalls.shift()
      if (ball) {
        ball.show = false
        el.style.display = 'none'
      }
    }
  },
  components: {
    cartcontrol,
    backdrop
  }
}
</script>

<style lang="stylus" scoped>
.shopCart
  position fixed
  left 0
  bottom 0
  width 100%
  height 1.1rem
  z-index 9999
  .content
    display flex
    background #222222
    .content-left
      display inline-block
      width 5.3rem
      height 1.1rem
      .logo-wrapper
        display inline-block
        vertical-align top
        position: relative
        width: 1.24rem
        height: 1.16rem
        line-height: 1.16rem
        border-radius: 50%
        top: -10px
        margin:0 12px
        padding 6px
        box-sizing border-box
        text-align: center
        background-image url('../../images/shopping_0.png')
        background-size 100%
       .cartlogo2
        background url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAHwAAAB0CAYAAABHTVbTAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAyFpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuNi1jMTQyIDc5LjE2MDkyNCwgMjAxNy8wNy8xMy0wMTowNjozOSAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIiB4bWxuczp4bXBNTT0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL21tLyIgeG1sbnM6c3RSZWY9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9zVHlwZS9SZXNvdXJjZVJlZiMiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENDIChXaW5kb3dzKSIgeG1wTU06SW5zdGFuY2VJRD0ieG1wLmlpZDpDNTcxMTE3RURCRjIxMUU3ODUzOUJDMDk1NjRGNzM3NCIgeG1wTU06RG9jdW1lbnRJRD0ieG1wLmRpZDpDNTcxMTE3RkRCRjIxMUU3ODUzOUJDMDk1NjRGNzM3NCI+IDx4bXBNTTpEZXJpdmVkRnJvbSBzdFJlZjppbnN0YW5jZUlEPSJ4bXAuaWlkOkM1NzExMTdDREJGMjExRTc4NTM5QkMwOTU2NEY3Mzc0IiBzdFJlZjpkb2N1bWVudElEPSJ4bXAuZGlkOkM1NzExMTdEREJGMjExRTc4NTM5QkMwOTU2NEY3Mzc0Ii8+IDwvcmRmOkRlc2NyaXB0aW9uPiA8L3JkZjpSREY+IDwveDp4bXBtZXRhPiA8P3hwYWNrZXQgZW5kPSJyIj8+sjwRkQAAMg1JREFUeNrkfQmMXdd5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")
        background-size 100%
        .badge
          position absolute
          top: 0;
          right 0
          background: rgb(240,20,20);
          color: white;
          width .4rem
          height .4rem
          line-height: .4rem;
          font-size: .24rem;
          box-shadow: 0px 4px 8px 0px rgba(0,0,0,0.4);
          font-weight: 700;
          border-radius: 16px;
          text-align center
          background-color #ff2d4b
        .cartdown
          background-color #ff2d4b
        .logo
          width 100%
          height 100%
          background: #000
          border-radius: 50%
          font-size: 24px
          color: #80858a
          line-height: 44px
          font-weight: 700
          &.active
            background: #000;
            color: white;
      .price
        display inline-block
        vertical-align top;
        font-size .26rem
        padding-right .24rem
        box-sizing border-box
        color #999999
        font-weight 700
        line-height .8rem
        margin-top .1rem
        .small
         font-size .24rem
         line-height: .36rem
        p
          // line-height: .44rem
        p:nth-child(1)
          font-size .34rem
          margin-top .04rem
        &.active
          color white
      .desc
        position relative
        display inline-block
        vertical-align top
        margin 12px 0 0 12px
        font-size 10px
        color rgba(255,255,255,0.4)
        font-weight 700
        line-height 24px
    .content-right
      display inline-block
      width 2.2rem
      font-size .32rem
      font-weight 700
      background #999
      color rgba(255,255,255,1)
      line-height 1.1rem
      text-align center
      &.enough
        background #ef7f2d
        color white
  .ball-container
    .ball
      position fixed
      left 32px
      bottom 22px
      z-index 200
      &.drop-enter,&.drop-enter-active
        transition all 0.4s cubic-bezier(0.49,-0.29,0.75,0.41)
        .inner
          width 16px
          height 16px
          border-radius 50%
          background #005e32
          transition all 0.4s linear
  .shopcart-list
    position absolute
    top 0
    left 0
    width 100%
    background white
    transform translate3d(0,-100%,0)
    z-index -1
    &.transHeight-enter-active,&.transHeight-leave-active
      transition all 0.5s
    &.transHeight-enter,&.transHeight-leave-active
      transform translate3d(0,0,0)
    .list-header
      height 40px
      line-height 40px
      background #f3f5f7
      border-bottom 1px solid rgba(7,17,27,0.1)
      .title
        display inline-block
        font-size .28rem
        font-weight 200
        color #666
        padding-left .24rem
      .empty
        position absolute
        right 8px
        font-size .28rem
        color #005e32
        padding 0 .1rem
    .list-content
      padding-bottom .1rem
      max-height 217px
      overflow hidden
      .food
        position relative
        display block
        // height .96rem
        margin 0 .24rem
        border-bottom 1px solid rgba(7,17,27,0.1)
        .name
          float left
          width 3.46rem
          overflow hidden
          font-size .28rem
          color rgb(7,17,27)
          height .96rem
          line-height .96rem
          font-weight 700
        .price
          float left
          font-size .28rem
          font-weight 700
          color rgb(240,20,20)
          padding 0 .1rem 0 .1rem
          line-height .96rem
        .cartcontrol-wrapper
          float right
          font-size .28rem
          margin-top .22rem
.backdrop
  position fixed
  top 0
  bottom 0
  left 0
  right 0
  background rgba(7,17,27,0.6)
  backdrop-filter blur(10px)
  z-index 40
  &.fade-backdrop-enter-active,&.fade-backdrop-leave-active
    transition opacity 0.5s
  &.fade-backdrop-enter,&.fade-backdrop-leave-active
    opacity 0
.foodother
  font-size .26rem
  padding: 0 0 .1rem
  color #969696
.chenghao
  font-size: .3rem
  display: inline-block
  height: .3rem
  line-height: .3rem

</style>
