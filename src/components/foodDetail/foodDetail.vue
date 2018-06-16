<template lang="html">
  <transition name="move">
    <!-- 单品页1 -->
    <div v-if="food.dplx===1" v-show="showDetail">
      <div class="foodDetailTitle">
        <div class="back" @click="showToggle()">&nbsp;</div>
      </div>
      <div class="detailWrapper" ref="detailWrapper">
        <div class="foodDetail">
          <div class="foodpicbox" style="width: 100%; height: 4rem; overflow: hidden; position: relative;"><img :src="food.images.source_image" class="foodpic" style="width:100%;"><div class="title">{{food.name}}</div></div>
          <div class="bgbox">
            <div class="des">
              <div v-if="food.label" class="subname">{{food.label}}</div>
              <div class="desc">
                <span v-if="food.is_scoreRatio==1">满{{food.spendingCash}}凤凰会返现{{food.scoreRatio}}%</span>
              </div>
              <div class="price">
                <span class="old_price" v-show="food.old_price"><del>原价：￥{{food.old_price}}</del></span>
                <span class="unit">会员价：&yen{{food.price}}</span>
              </div>
            </div>
            <div class="divider"></div>
            <div class="desc">
              <div class="title">&nbsp;</div>
              <div class="content" v-html="food.des"></div>
            </div>
          </div>
          <div style="height: 50px">&nbsp</div>
        </div>
        <div class="shopCart">
          <transition name="fade">
            <div v-if="food.count_num==='0'">
              <p class="foodsqin">卖光啦！</p>
            </div>
            <div v-else class="text" @click="addCart($event)" v-show="!food.count">&yen{{food.price}} | 来一份</div>
          </transition>
        </div>
        <cartcontrol v-show="food.count" :food="food"></cartcontrol>
      </div>
      <div class="zhizhao">&nbsp</div>
    </div>

<!-- 单品页2 -->
<div v-if="food.dplx===2" v-show="showDetail">
  <div class="foodDetailTitle">
    <div class="back" @click="showToggle()">&nbsp;</div>
  </div>
  <div class="detailWrapper" ref="detailWrapper">
    <div class="foodDetail">
      <div class="foodpicbox" style="width: 100%; height: 4rem; overflow: hidden; position: relative;"><img :src="food.images.source_image" class="foodpic" style="width:100%;"><div class="title">{{food.name}}<!-- <div v-if="food.label" class="subname">{{food.label}}</div> --></div></div>
      <div class="bgbox">
        <div>
          <childthis :childfood="food" :selectFoods="selectFoods" :food="food" @dggchange="refreshScroll" ref="c1"></childthis>
        </div>
        <div class="divider"></div>
        <div class="desc">
          <div class="title">&nbsp;</div>
          <div class="content" v-html="food.des"></div>
        </div>
      </div>
      <div style="height: 50px">&nbsp</div>
    </div>
    <div class="shopCart">
      <transition name="fade">
        <div v-if="food.count_num==='0'">
          <p class="foodsqin">卖光啦！</p>
        </div>
        <div v-else class="text" @click="addCart_dgg1($event)" v-show="!food.count">&yen;{{ food.totalprice*1 }} | 来一份</div>
      </transition>
    </div>
  </div>
  <div class="zhizhao">&nbsp</div>
</div>

<!-- 单品页3 -->
<div v-if="food.dplx===3" v-show="showDetail">
  <div class="foodDetailTitle">
    <div class="back" @click="showToggle()">&nbsp;</div>
  </div>
  
  <div  class="detailWrapper detailWrapper2" ref="detailWrapper">
    <div  class="foodDetail">
      <!-- 主块 -->
      <div v-if="this.food.flag==0" class="contentbox">
      <div class="foodpicbox" style="width: 100%; height: 4rem; overflow: hidden; position: relative;"><img :src="food.images.source_image" class="foodpic" style="width:100%;"><div class="title">{{food.name}}<!-- <div v-if="food.label" class="subname">{{food.label}}</div> --></div></div>
      <div class="bgbox">
        <div>
          <childthis :childfood="food" :selectFoods="selectFoods" :food="food" @dggchange="refreshScroll" ref="c1"></childthis>
        </div>
        <div class="divider"></div>
        <div class="desc">
          <div class="title">&nbsp;</div>
          <div class="content" v-html="food.des"></div>
        </div>
      </div>
      <div style="height: 50px">&nbsp</div>
      </div>
      <!-- 推荐汤品 -->
        <div v-if="this.food.flag==1" class="contentbox">
          <div class="foodpicbox" style="width: 100%; height: 4rem; overflow: hidden; position: relative;"><img :src="food.images.source_image" class="foodpic" style="width:100%;"><div class="title txtcenter"><span class="big">一</span><span class="log">&nbsp;</span>推荐汤品&nbsp;&nbsp;<span class="big">一</span></div></div>
          <div class="bgbox">
            <div class="tuijian">
              <div v-if="food.tjtangpins" v-for="tjtangpin in food.tjtangpins">
                <div class="tuijianbox">
                  <span class="name">{{tjtangpin.name}}</span>
                  <div class="cartcontrol-wrapper">
                    <cartcontrol2 :food="tjtangpin"></cartcontrol2>
                  </div>
                  <div class="price">
                    <span>￥{{tjtangpin.price*1}}</span>
                  </div>
                </div>
              </div>
            </div>
            <div class="divider"></div>
          </div>
          <div style="height: 1000px">&nbsp</div>
        </div>
        <!-- 推荐甜品 -->
          <div v-if="this.food.flag==2" class="contentbox">
            <div class="foodpicbox" style="width: 100%; height: 4rem; overflow: hidden; position: relative;"><img :src="food.images.source_image" class="foodpic" style="width:100%;"><div class="title txtcenter"><span class="big">一</span><span class="log">&nbsp;</span>推荐甜品名吃&nbsp;&nbsp;<span class="big">一</span></div></div>
            <div class="bgbox">
              <div class="tuijian">
                <div v-if="food.tjtianpins" v-for="tjtianpin in food.tjtianpins">
                  <div class="tuijianbox">
                    <span class="name">{{tjtianpin.name}}</span>
                    <div class="cartcontrol-wrapper">
                      <cartcontrol2 :food="tjtianpin"></cartcontrol2>
                    </div>
                    <div class="price">
                      <span>￥{{tjtianpin.price*1}}</span>
                    </div>
                  </div>
                </div>
              </div>
              <div class="divider"></div>
            </div>
            <div style="height: 1000px">&nbsp</div>
          </div>
          <!-- 推荐功夫茶 -->
          <div v-if="this.food.flag==3" class="contentbox">
              <div  class="foodpicbox" style="width: 100%; height: 4rem; overflow: hidden; position: relative;"><img :src="food.images.source_image" class="foodpic" style="width:100%;"><div class="title txtcenter"><span class="big">一</span><span class="log">&nbsp;</span>推荐功夫茶&nbsp;&nbsp;<span class="big">一</span></div></div>
              <div class="bgbox">
                <div class="tuijian">
                  <div v-if="food.tjgongfuchas" v-for="tjgongfucha in food.tjgongfuchas">
                    <div class="tuijianbox">
                      <span class="name">{{tjgongfucha.name}}</span>
                      <div class="cartcontrol-wrapper">
                        <cartcontrol2 :food="tjgongfucha"></cartcontrol2>
                      </div>
                      <div class="price">
                        <span>￥{{tjgongfucha.price*1}}</span>
                      </div>
                    </div>
                  </div>
                </div>
                <div class="divider"></div>
              </div>
              <div style="height: 1000px">&nbsp</div>
            </div>
    </div>
    <div v-if="this.food.flag==0" class="shopCart shopCart2">
      <transition name="fade">
        <div v-if="food.count_num==='0'">
          <p class="foodsqin">卖光啦！</p>
        </div>
        <div v-else class="text" @click="addCart_dgg1($event)" v-show="!food.count">&yen;{{ food.totalprice*1 }} | 来一份</div>
      </transition>
    </div>
</div>
<!-- 切换按钮 -->
<div class="cjchange">
    <ul class="cjchange-list">
        <li v-if="food.flag===0"  @click="selectCj(0)" :class="{'on':food.flag==0 }" class="zhushi" :key="1"><span class="ico_zhushi">&nbsp;</span></li>
        <li v-else  @click="selectCj(0)" :class="{'on':food.flag==0 }" :key="1">再选一份</li>
        <li  @click="selectCj(1)" :class="{'on':food.flag==1 }" :key="2">推荐汤品</li>
        <li  @click="selectCj(2)" :class="{'on':food.flag==2 }" :key="3">推荐甜品名吃</li>
        <li  @click="selectCj(3)" :class="{'on':food.flag==3 }" :key="4">推荐功夫茶</li>
    </ul>
</div>
  <div class="zhizhao">&nbsp</div>
</div>
  </transition>
</template>




<script>
// import '../../filter/time.js'
import BScroll from 'better-scroll'
import cartcontrol from 'components/cartcontrol/cartcontrol'
import cartcontrol2 from 'components/cartcontrol/cartcontrol2'
import jcaicontrol from 'components/cartcontrol/jcaicontrol'
import childFood from 'components/foodDetail/childFood'
import childthis from 'components/foodDetail/childthis'


export default {
  components: {
    cartcontrol,
    cartcontrol2,
    jcaicontrol,
    childFood,
    childthis
  },
  props: {
    goods: Array,
    food: Object,
    selectFoods: {
      type: Array,
      default: []
    }
  },
  data() {
    return {
      showDetail: false,
      evelflag: true,
      descshow: false,
    }
  },
  computed: {
    Descshow: function() {
      return this.food.jsdescshow
    },

  },
  methods: {
    selectCj(index) {
      this.food.flag = index;
      this.detailWrapper.scrollTo(0, 0);
    },
    zoom(obj, width, height) {
      var img = new Image();
      img.src = obj.src;
      var scale = Math.max(width / img.width, height / img.height);
      var newWidth = img.width * scale;
      var newHeight = img.height * scale;
      var div = obj.parentNode;
      obj.width = newWidth;
      obj.height = newHeight;
      div.style.width = width + "px";
      div.style.height = height + "px";
      div.style.overflow = "hidden";
      obj.style.marginLeft = (width - newWidth) / 2 + "px";
      obj.style.marginTop = (height - newHeight) / 2 + "px";

    },
    showToggle() {
      this.showDetail = !this.showDetail
      if (this.showDetail) {
        this.$nextTick(() => {
          // this.detailWrapper = false
          this._initScroll()
          this.detailWrapper.scrollTo(0, 0)
        })
      }
    },
    refreshScroll() {
      if (this.showDetail) {
        this.$nextTick(() => {
          this._initScroll()
        })
      }
    },
    _initScroll() {
      if (!this.detailWrapper) {
        this.detailWrapper = new BScroll(this.$refs.detailWrapper, {
          click: true
        });
      } else {
        this.detailWrapper.refresh()
      }
    },
    addCart(event) {
      if (!event._constructed) {
        return
      }
      this.$set(this.food, 'count', 1)
      this.$root.eventHub.$emit('cart.add', event.target)
    },
    addCart_dgg1(event){
      this.$refs.c1.addCart(event);
    },
    descShow() {
      this.food.jsdescshow = !(this.food.jsdescshow)
    }

  }
}
</script>
<style lang="stylus" scoped>
.shopCart
  width 6.67rem
  height 1rem
  background-color: #f4f4f4
  position fixed
  left .42rem
  top 9rem
  text-align center
  z-index 51
  .foodsqin
          color red
  .text
    float right
    height .64rem
    box-sizing border-box
    line-height .64rem
    padding 0 .5rem
    margin .2rem .2rem 0 0
    color #fff
    font-size .32rem
    border-radius .32rem
    background #ff8226
    &.fade-enter-active,&.fade-leave-active{
      transition opacity .2s
    }
    &.fade-enter,&.fade-leave-active{
      opacity 0
    }
.cartcontrol
  font-size .30rem
  width 2.2rem
  height .6rem
  position fixed
  right .5rem
  top 9.9rem
  text-align center
  z-index 52

.detailWrapper
  width 6.67rem
  height 9rem
  overflow hidden
  background-color #fff
  border-radius 5px
  -webkit-overflow-scrolling touch
  position fixed
  left .42rem
  top 1rem
  z-index 50
  bottom 1.1rem
  transition all 0.4s ease
  &.move-enter-avtive,&.move-leave-active{
    transform translate3d(0,0,0)
  }
  &.move-enter,&.move-leave-active{
    transform translate3d(100%,0,0)
  }
.foodDetail
  // position relative
  background #fff
  -webkit-overflow-scrolling touch
  z-index 50
  // min-height: 13.34rem
  // .foodDetailTitle
  //   width 100%
  //   height .8rem
  //   background-image url('../../images/foodDetailTitle.jpg')
  //   background-size 100%
  //   line-height .8rem
  //   text-align center
  //   color white
  //   position absolute
  //   top: 0
  //   left:0
  // .back
  //   width .8rem
  //   height .8rem
  //   position absolute
  //   color white
  //   top 0
  //   right 0
  //   font-size 20px
  //   background-image url('../../images/back.png')
  //   background-size 100%
  .foodpicbox
    .title  
      width 5.9rem
      line-height 1.2
      background-color #ffffff
      opacity 0.8
      font-size .34rem
      color #005e32
      font-weight bold
      padding .2rem
      position absolute
      bottom 0
      left .19rem
  .bgbox
    width 100%
  .des
    position relative
    box-sizing border-box
    width 100%
    padding .2rem .36rem
    // border 0.02rem solid #005e32
    // border-radius: 0.2rem
    // margin 0.5rem 2%
    .title
      font-size .3rem
      color #005e32
      line-height .5rem
      font-weight bold
    .desc
      display flex
      padding 0
      font-size .26rem
      color #666666
      line-height .4rem
      span:last-child
        // padding-left 12px
    .price
      display block
      padding-top .08rem
      font-size .28rem
      color #006666
      line-height .36rem
      .unit
        // font-size 10px
        // font-weight normal
        display block
        line-height .4rem
      .old_price
        font-size .26rem
        color #999999
        line-height .36rem
  .desc
    padding 0
    .title
      width 6.67rem
      height 1.5rem
      background-image url("data:image/jpeg;base64,/9j/4QAYRXhpZgAASUkqAAgAAAAAAAAAAAAAAP/sABFEdWNreQABAAQAAABkAAD/4QOIaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wLwA8P3hwYWNrZXQgYmVnaW49Iu+7vyIgaWQ9Ilc1TTBNcENlaGlIenJlU3pOVGN6a2M5ZCI/PiA8eDp4bXBtZXRhIHhtbG5zOng9ImFkb2JlOm5zOm1ldGEvIiB4OnhtcHRrPSJBZG9iZSBYTVAgQ29yZSA1LjYtYzE0MiA3OS4xNjA5MjQsIDIwMTcvMDcvMTMtMDE6MDY6MzkgICAgICAgICI+IDxyZGY6UkRGIHhtbG5zOnJkZj0iaHR0cDovL3d3dy53My5vcmcvMTk5OS8wMi8yMi1yZGYtc3ludGF4LW5zIyI+IDxyZGY6RGVzY3JpcHRpb24gcmRmOmFib3V0PSIiIHhtbG5zOnhtcE1NPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvbW0vIiB4bWxuczpzdFJlZj0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL3NUeXBlL1Jlc291cmNlUmVmIyIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIiB4bXBNTTpPcmlnaW5hbERvY3VtZW50SUQ9InhtcC5kaWQ6ZWY5Njk3NjItODY2MS0xOTQwLTk3MzMtNDkyMzRiNTBhMDRjIiB4bXBNTTpEb2N1bWVudElEPSJ4bXAuZGlkOjdDNzU2QzQ2REUyNjExRTc5QkYxQjAzRUYxQUJDMzg4IiB4bXBNTTpJbnN0YW5jZUlEPSJ4bXAuaWlkOjdDNzU2QzQ1REUyNjExRTc5QkYxQjAzRUYxQUJDMzg4IiB4bXA6Q3JlYXRvclRvb2w9IkFkb2JlIFBob3Rvc2hvcCBDQyAoV2luZG93cykiPiA8eG1wTU06RGVyaXZlZEZyb20gc3RSZWY6aW5zdGFuY2VJRD0ieG1wLmlpZDowMjBiMTE5Ni1mYTg5LWQ0NDQtYmRjYS0xZjMwZmFjOGRmYWQiIHN0UmVmOmRvY3VtZW50SUQ9ImFkb2JlOmRvY2lkOnBob3Rvc2hvcDpkYWQ5YTliZS0zMzQ5LWIwNDUtOGQyMy1lN2NjMDczNzI2YmMiLz4gPC9yZGY6RGVz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")
      background-size 100%
    .content
      padding .1rem .4rem
      font-size .26rem
      color #666666
      line-height .36rem
    &:after
      content ''
      clear both
      display block
      height 0

  .evaluation
    padding 18px 0
    position relative
    .title
      padding-left 18px
      font-size: 14px
      font-weight 500
      color: #07111b
    .classify
      padding 18px 0
      margin 0 18px
      border-bottom 1px solid rgba(7,17,27,0.1)
      .item
        display inline-block
        font-size 12px
        padding 8px 12px
        line-height 16px
        background rgba(0,160,220,0.2)
        color rgb(77,85,95)
        margin-right 8px
        .count
          font-size 8px
          padding-left 2px
        &.active
          color white
          background rgb(0,169,220)
        &.bad
          background rgba(77,85,93,0.2)
        &.badActive
          background #4d555d
    .switch
      font-size 12px
      width 100%
      padding 12px 0 12px 18px
      color rgb(147,153,159)
      border-bottom 1px solid rgba(7,17,27,0.1)
      .icon-check_circle
        font-size 24px
        vertical-align middle
        &.on
          color #00c850
    .evel-list
      margin 0 18px
      .evel
        padding 16px 0
        border-bottom 1px solid rgba(7,17,27,0.1)
        .userdes
          display flex
          color rgb(147,153,159)
          font-size 10px
          line-height 12px
          .time
            flex 1
          .user
            flex 1
            text-align right
            .avatar
              img
                padding-left 6px
                border-radius 50%
        .content
          padding-top 6px
          .icon
            font-size 12px
            line-height 24px
            &.icon-thumb_up
              color rgb(0,160,220)
            &.icon-thumb_down
              color rgb(147,153,159)
          .text
            font-size 12px
            color rgb(7,17,27)
            line-height 16px
            padding-left 4px

.divider
  border none
  height 0
  margin 0 .26rem
  width auto
.zhizhao
  position fixed
  width 100%
  height 100%
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
.desc2
  width 6.62rem
  height .4rem
  position absolute
  overflow hidden
  top 4.5rem
  background-color white
  border-radius .2rem
  left .26rem
  padding .2rem
  z-index 99
  box-shadow 0.08rem 0.08rem 0.08rem rgba(0,0,0,.6);
  .title
    width .76rem
    font-size .26rem
    color #666666
    float left
    line-height .5rem
    background-color white

  .content
    width 5rem
    float left
    font-size .26rem
    color #666666
    line-height .5rem
    background-color white

  &:after
    content ''
    clear both
    display block
    height 0
.desc2show
  overflow visible
  height auto
.btnsj
  width: 1rem
  height: .8rem
  background-image url('../../images/btnsj_down.png')
  background-size 100%
  float right
.btnsjon
  background-image url('../../images/btnsj_up.png')
.tuijian
  position relative
  display block
  margin 0 .4rem
  .log
    width .32rem
    height .32rem
    display inline-block
    margin 0 .18rem
    background-image url('../../images/tuijianlog.jpg')
    background-size 100%
  .title
    text-align: center
    font-size .28rem
    color #eb344e
    height .32rem
    margin .4rem 0
    .big
      font-weight: 700
      line-height: 0
      padding: .16rem 0
      display: inline-block
  .tuijianbox
    overflow: hidden
    border-bottom 1px solid #dddddd
  .name
    font-weight: bold
    float left
    width 3rem
    overflow hidden
    font-size .28rem
    color #666666
    height .96rem
    line-height .96rem
    position: relative
    margin-right .2rem
    .circlebox
      display: inline-block
      width: .1rem
      height .1rem
      border-radius: .05rem
      background-color #005e32
      position: absolute
      left 0
      top .38rem
  .price
    flex 2
    float left
    font-size .28rem
    font-weight 700
    color #666666
    height: 0.96rem
    line-height 0.96rem
    white-space: nowrap
    span
     display inline-block
     width .5rem
     text-align left
     padding-right .2rem
  .cartcontrol-wrapper
    flex: 2.5
    float right
    font-size .28rem
    margin-top .22rem
    text-align: right
.foodDetailTitle
  width .6rem
  height .6rem
  padding-bottom .1rem
  background-image url('../../images/btn_close.png') 
  background-size 100%
  background-repeat no-repeat
  text-align center
  color white
  position absolute
  top: .25rem
  left:3.46rem
  z-index: 1000
  .back
    width .6rem
    height .6rem
.subname
  font-size: .26rem;
  margin-top .1rem
  white-space: nowrap;
  color #666
.detailWrapper2
  height 8rem
.shopCart2 
  top 8rem
.cjchange
  width 6.67rem
  height .6rem
  overflow hidden
  position fixed
  top 9.2rem
  left .42rem
  z-index 50
  border-radius: .2rem
  .cjchange-title
    display: inline-block
    float: left
    line-height: 0
    padding: .32rem 0
  .cjchange-list
    width 7rem
    display: inline-block
    float: left
    li
      float: left
      width 1.66rem
      line-height: 0
      padding .3rem 0
      background-color #fff
      text-align: center
      font-size .24rem
      &.on
        background #005e32
        color #fff
        border-color #00796b
      &:last-child
        margin-right 0  
.txtcenter
  text-align center     
.zhushi
  padding: 0 !important
  line-height .44rem  !important
  .ico_zhushi
    display inline-block
    width .28rem
    height .28rem
    background #005e32  url('../../images/ico_zhushi.png') 0 0 no-repeat
    background-size 100%
    margin-top .16rem
  
   

</style>
