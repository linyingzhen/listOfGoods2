<template lang="html">

  <div class="cartcontrol">
    <transition name="fadeRotate">
      <div class="cart-decrease" v-show="food.count>0" @click.stop.prevent="decreaseCart()">
          <span class="icon-remove_circle_outline inner"></span>
      </div>
    </transition>
    <div class="cart-count" v-show="food.count>0">
      {{food.count}}
    </div>
    <div class="cart-add" @click.stop.prevent="addCart($event)">
      <i class="icon-add_circle"></i>
    </div>
  </div>

</template>

<script>
import Vue from 'vue'

export default {
  props: {
    food: Object,
    selectFoods:Array
  },
  methods: {
    addCart(event) {
      // console.log(event.target);
      if (!event._constructed) {
        return
      }
      if (!this.food.count) {
        Vue.set(this.food, 'count', 0)
      }
      this.food.count++;
      this.$root.eventHub.$emit('cart.add', event.target); 
    },
    decreaseCart() {
      if (!event._constructed || !this.food.count) {
        return
      }
      this.food.count--;
      if(this.food.count<=0){
          if(this.selectFoods !='undefined'){
            return;
          }
          if(this.selectFoods.length==1){
            this.$emit('qkgwc')
          }
      }
    },
  }
}

</script>

<style lang="stylus">

.cartcontrol
  .cart-decrease
    font-size .30rem
    display inline-block
    line-height 0
    .icon-remove_circle_outline
      width .5rem
      display inline-block
      background-image url('../../images/minus.png')
      background-size 100%
      background-repeat no-repeat
    .inner
      line-height 0
      padding .27rem 0
      transition all 0.4s linear
    &.fadeRotate-enter-active, &.fadeRotate-leave-active
      transform translate3d(0,0,0)
      .inner
        display inline-block
        transform rotate(0)
    &.fadeRotate-enter, &.fadeRotate-leave-active
      opacity: 0
      transform translate3d(100px,0,0)
      .inner
        transform rotate(180deg)
  .cart-count
    display inline-block
    vertical-align top
    font-size .26rem
    color rgb(147,153,159)
    text-align center
    line-height 0
    padding .27rem .1rem
  .cart-add
    display inline-block
    color #005e32
    line-height 0
    .icon-add_circle
      width .5rem
      line-height 0
      padding .27rem 0
      display inline-block
      background-image url('../../images/plus.png')
      background-size 100%
      background-repeat no-repeat
      
</style>
