<template lang="html">

  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
     <!--  <li v-for="(item,index) in goods" @click="menuClick(index,$event)" :class="index==menuCurrentIndex?'menu-item-selected':'menu-item'" @click="selectStyle (item, $index) " :class="{'menu-item-selected':item.active,'menu-item':!item.active}"> -->
        <!-- <li v-for="(item,index) in goods" @click="menuClick(index,$event)" @click="selectStyle (item, $index) " :class="{'menu-item-selected':item.active,'menu-item':!item.active}"> -->
        <li v-for="(item,index) in goods" v-show="item.is_hide==='0'" @click="menuClick(index,$event)" :class="index==menuCurrentIndex?'menu-item-selected':'menu-item'">
          <span class="text" v-html="item.sort_name">
            <!-- <iconMap v-show="item.type>0" :iconType="item.type"></iconMap> -->
            <!-- {{item.sort_name}} -->
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" id="wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="item in goods" v-show="item.is_hide==='0'" class="food-list food-list-hook">
          <h1>{{item.sort_name | filterBr}}</h1>
          <ul>
            <li v-for="food in item.foods" class="food-item" @click="goDetail(food)">
              <div class="icon">
                <img width="57" height="57" :src="food.images.tumb_image"/>
                <div v-if="food.is_discount==='1'" class="discount">&nbsp;</div>
              </div>
              <div class="content">
                <h2>{{food.name}}</h2>
                <p class="subname" v-show="food.subname">{{food.subname}}</p>
                <!-- <div class="sell-info">
                  <span class="sellCount">月售{{food.sellCount}}份</span>
                  <span class="rating">好评率{{food.rating}}%</span>
                </div> -->
                <div class="price">
                  <span v-show="food.old_price" class="old_price">￥{{food.old_price}}</span>
                  <span class="newPrice"><span class="unit">￥</span>{{food.price}}</span>
                </div>
                <div v-if="food.count_num>0" style="position: absolute; bottom:0.58rem;right: 1.7rem;color:red; font-size: .24rem">剩余{{food.count_num}}</div>
                <div v-if="food.count_num==='0'" class="cartcontrol-wrapper">
  <p class="foodsqin">卖光啦!</p>
</div>
                <div v-else class="cartcontrol-wrapper">
                  <cartcontrol v-if="!food.dgg" :food="food"></cartcontrol>
                  <a v-else class="btn-pc">进入选项</a>
                </div>

              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopCart :deliveryPrice="Number(seller.deliveryPrice)" :minPrice = "Number(seller.minPrice)" :selectFoods="selectFoods"></shopCart>
    <foodDetail :food="selectedFood" v-if="selectedFood" ref="myFood" :selectFoods="selectFoods"></foodDetail>
    
  </div>

</template>

<script>
// import iconMap from 'components/iconMap/iconMap'
import BScroll from 'better-scroll'
import shopCart from 'components/shopCart/shopCart'
import cartcontrol from 'components/cartcontrol/cartcontrol'
import foodDetail from 'components/foodDetail/foodDetail'
// import axios from 'axios'
import Vue from 'vue'

const ERR_OK = 0
const eventHub = new Vue()
export default {
  props: {
    seller: Object
  },
  created() {

    this.goods = foodsData.goods;
    this.$nextTick(() => {
      this._initScroll(); // 初始化scroll
      this._calculateHeight(); // 初始化列表高度列表
    })


    // axios.get('static/data.json').then((res) => {
    //   this.goods = res.data.goods
    //   this.$nextTick(() => {
    //     this._initScroll(); // 初始化scroll
    //     this._calculateHeight(); // 初始化列表高度列表
    //   })
    // });


  },
  mounted() {
    if(foodsData.seller.food_id){
      let foodishas=false;
      this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if(food.meal_id==foodsData.seller.food_id){
              foodishas=true;
              if(food.count_num!=='0'){
                this.goDetail(food);
              } else {
                layer.open({
                    content: '对不起，您扫描的二维码所对应的商品卖完了或者已经下架。请继续选购其它商品，谢谢！',
                    btn: ['确定'],
                    yes: function(index) {
                        layer.close(index);
                    }
                });
              }
            }
          });

      });
      if(!foodishas){
        layer.open({
            content: '对不起，您扫描的二维码所对应的商品卖完了或者已经下架。请继续选购其它商品，谢谢！',
            btn: ['确定'],
            yes: function(index) {
                layer.close(index);
            }
        });
      }
    }
  },
  data() {
    return {
      goods: [],
      listHeight: [],
      foodsScrollY: 0,
      selectedFood: '',
      active: false,
      isclick: true,
    }
  },
  filters: {
    filterBr(value){
      return value.replace(/<\/?[^>]*>/g,'');
    }
  },
  computed: {
    menuCurrentIndex() {
      for (let i = 0, l = this.listHeight.length; i < l; i++) {
        let topHeight = this.listHeight[i]
        let bottomHeight = this.listHeight[i + 1]
        if (!bottomHeight || (this.foodsScrollY >= topHeight && this.foodsScrollY < bottomHeight)) {
          return i
        }
      }
      return 0
    },
    selectFoods() {
      let foods = []
      this.goods.forEach((good) => {
        good.foods.forEach((food) => {
          if (food.count) {
            foods.push(food)
          }
          if (food.dgg && food.childrens.length > 0) {
            food.childrens.forEach((child) => {
              if (child.count) {
                foods.push(child)
              }

            })
          }

          if (food.tjtangpins && food.tjtangpins.length > 0) {
            food.tjtangpins.forEach((tjtangpin) => {
              if (tjtangpin.count) {
                foods.push(tjtangpin)
              }

            })
          }

          if (food.tjtianpins && food.tjtianpins.length > 0) {
            food.tjtianpins.forEach((tjtianpin) => {
              if (tjtianpin.count) {
                foods.push(tjtianpin)
              }

            })
          }

          if (food.tjgongfuchas && food.tjgongfuchas.length > 0) {
            food.tjgongfuchas.forEach((tjgongfucha) => {
              if (tjgongfucha.count) {
                foods.push(tjgongfucha)
              }

            })
          }

        })
      })

      return foods
    }
  },
  methods: {
    _initScroll() {
      this.menuWrapper = new BScroll(this.$refs.menuWrapper, {
        click: true
      });
      this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
        click: true,
        probeType: 1
      });
      // 监控滚动事件
      this.foodsScroll.on('scroll', (pos) => {
        this.isclick = false
        this.foodsScrollY = Math.abs(Math.round(pos.y))
        this.$nextTick(() => {
          this.menuCurrentIndex
        })
      });
      this.foodsScroll.on('scrollStart', (pos) => {
        this.isclick = true
        this.$nextTick(() => {
          this.menuCurrentIndex
        })
      });
      this.foodsScroll.on('scrollCancel', (pos) => {
        this.$nextTick(() => {
          this.isclick = true
          this.menuCurrentIndex
        })
      });
      this.foodsScroll.on('scrollEnd', (pos) => {
        this.isclick = false
        setTimeout(function() {
          this.isclick = true
        }, 1000)
        this.$nextTick(() => {
          this.menuCurrentIndex
        })
      });

    },
    _calculateHeight() {
      let foodList = this.$refs.foodsWrapper.querySelectorAll('.food-list-hook')
      let height = 0
      this.listHeight.push(height)
      for (let i = 0, l = foodList.length; i < l; i++) {
        let item = foodList[i]
        height += item.clientHeight
        this.listHeight.push(height)
      }
    },
    menuClick(index, event) {
      if (!event._constructed) {
        return
      }
      this.foodsScroll.scrollTo(0, -this.listHeight[index], 300)
    },
    goDetail(food) {
      if (this.isclick && food.count_num!=='0' && food.store_id !='34') {
        this.selectedFood = food
        if (!this.selectedFood.jsdescshow) {
          Vue.set(this.selectedFood, 'jsdescshow', false)
        }
        this.$nextTick(() => {
          this.$refs.myFood.showToggle()
        })
      }
    },
    selectStyle(item, index) {　　　　　　
      this.$nextTick(function() {　　　　　　　　
        this.goods.forEach(function(item) {　　　　　　　　　　
          Vue.set(item, 'active', false);　　　　　　　　
        });　　　　　　　　
        Vue.set(item, 'active', true);　　　　　　
      });　　　　
    }
    // listCountNum(foods){
    //   let count=0;
    //   foods.forEach((food)=>{
    //      count+= food.count
    //   })
    //   return count;
    // },
  },
  components: {
    // iconMap,
    shopCart,
    cartcontrol,
    foodDetail
  }
}
</script>

<style lang="stylus">
@import '../../common/stylus/mixin'
  .goods
    display flex
    position absolute
    top 0
    bottom 1.1rem
    width 100%
    overflow hidden
    .menu-wrapper
      width 1.8rem
      background #f2f2f2
      margin-top: 0;
      .menu-item,.menu-item-selected
        position relative
        display table
        font-size .26rem
        height 0.93rem
        line-height .28rem
        color #666666
        width 1.56rem
        padding 0 .12rem
        text-align: center
        border-bottom 1px solid #dddddd
        &:last-child:after
          content none
      .menu-item-selected
        color #036f3b
        background white
        font-weight bold
        padding 0 .12rem 0 .12rem
        .text
          font-weight bold
          // font-size .28rem    
      .menu-item:after
          position: absolute
          content: ''
          left: .24rem
          width: 56px
          bottom: 0
        .text
          display table-cell
          vertical-align middle
          white-space normal
          line-height .32rem
          // .iconMap
          //   vertical-align middle
            
    .foods-wrapper
      width 5.7rem
      margin-top: 0;
      .food-list
        h1
          height .6rem
          line-height .6rem
          padding-left .2rem
          font-size .24rem
          color #666666
          background #e9f4ee
      .food-item
        position relative
        display block
        margin: 0 .2rem;
        padding: .2rem 0;
        border-bottom 1px solid rgba(7,17,27,0.1)
        overflow hidden
        .icon
          float left
          width 1.24rem
          display inline-block
          img
            width 1.24rem
            height 1.24rem
            border-radius 0.07rem
          .discount
            text-align center
            color red
            font-size .28rem
            width 1.3rem
            height .31rem
            background url("data:image/jpeg;base64,/9j/4QAYRXhpZgAASUkqAAgAAAAAAAAAAAAAAP/sABFEdWNreQABAAQAAAA8AAD/4QMZaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wLwA8P3hwYWNrZXQgYmVnaW49Iu+7vyIgaWQ9Ilc1TTBNcENlaGlIenJlU3pOVGN6a2M5ZCI/PiA8eDp4bXBtZXRhIHhtbG5zOng9ImFkb2JlOm5zOm1ldGEvIiB4OnhtcHRrPSJBZG9iZSBYTVAgQ29yZSA1LjMtYzAxMSA2Ni4xNDU2NjEsIDIwMTIvMDIvMDYtMTQ6NTY6MjcgICAgICAgICI+IDxyZGY6UkRGIHhtbG5zOnJkZj0iaHR0cDovL3d3dy53My5vcmcvMTk5OS8wMi8yMi1yZGYtc3ludGF4LW5zIyI+IDxyZGY6RGVzY3JpcHRpb24gcmRmOmFib3V0PSIiIHhtbG5zOnhtcE1NPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvbW0vIiB4bWxuczpzdFJlZj0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL3NUeXBlL1Jlc291cmNlUmVmIyIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIiB4bXBNTTpEb2N1bWVudElEPSJ4bXAuZGlkOkQ1OEI0NzRDOEUxMzExRTc5QjYzOEJGMEUwNUM1QjZDIiB4bXBNTTpJbnN0YW5jZUlEPSJ4bXAuaWlkOkQ1OEI0NzRCOEUxMzExRTc5QjYzOEJGMEUwNUM1QjZDIiB4bXA6Q3JlYXRvclRvb2w9IkFkb2JlIFBob3Rvc2hvcCBDUzYgV2luZG93cyI+IDx4bXBNTTpEZXJpdmVkRnJvbSBzdFJlZjppbnN0YW5jZUlEPSI0Mjg1Qzk1RTM4MjExQkQ0NUExRkY0REY3RTcyMDMwQyIgc3RSZWY6ZG9jdW1lbnRJRD0iNDI4NUM5NUUzODIxMUJENDVBMUZGNERGN0U3MjAzMEMiLz4gPC9yZGY6RGVzY3JpcHRpb24+IDwvcmRmOlJERj4gPC94OnhtcG1ldGE+IDw/eHBhY2tldCBlbmQ9InIiPz7/7gAOQWRvYmUAZMAAAAAB/9sAhAAGBAQEBQQGBQUGCQYFBgkLCAYGCAsMCgoLCgoMEAwMDAwMDBAM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")
            background-size 100%
        &:last-child
          border-bottom none
        .content
          display inline-block
          font-size .30rem
          width 3.8rem
          padding-left .2rem
          h2
            overflow hidden
            // white-space: nowrap
            margin 0 0 .1rem 0
            font-size .3rem
            height .36rem
            line-height .36rem
            color #333333
          .sell-info,.subname
            font-size .2rem
            color rgb(147,153,159)
            line-height .2rem
            .sellCount
              margin-right .08rem
          .subname
            font-size .26rem
            margin-bottom .1rem
            line-height .3rem
          .price
            font-size .24rem
            line-height .3rem
            .newPrice
              display block
              font-size .34rem
              font-weight bold
              color #fb4e44
              .unit
                font-size .2rem
                font-weight normal
            .old_price
              text-decoration line-through
              color #cbcbcb
          .cartcontrol-wrapper
            position: absolute
            right: 0
            bottom .3rem
            z-index 20
            .foodsqin
              color red
              padding: .2rem
              font-size: .28rem
  .btn-pc
    position: relative;
    float: right;
    border: 1px solid #085a36;
    padding: .11rem .12rem;
    font-size: .24rem;
    color: #005e32;
    text-decoration: none;
    border-radius: .5rem;


</style>
