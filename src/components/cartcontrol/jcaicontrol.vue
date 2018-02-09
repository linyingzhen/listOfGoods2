<template lang="html">

  <div class="cartcontrol">
    <transition name="fadeRotate">
      <div class="cart-decrease" v-show="jcai.count>0" @click.stop.prevent="decreaseCart()">
          <span class="icon-remove_circle_outline inner"></span>
      </div>
    </transition>
    <div class="cart-count" v-show="jcai.count>0">
      {{jcai.count}}
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
    childfood:Object,
    jcai: Object,
  },
  methods: {
    addCart(event) {
      // console.log(event.target);
      if (!event._constructed) {
        return
      }
      if (!this.jcai.count) {
        Vue.set(this.jcai, 'count', 0)
      }
      this.jcai.count++;
      this.childfood.totalprice=(parseFloat(this.childfood.totalprice)+parseFloat(this.jcai.price)).toFixed(2)
    },
    decreaseCart() {
      if (!event._constructed || !this.jcai.count) {
        return
      }
      this.jcai.count--;

      this.childfood.totalprice=(parseFloat(this.childfood.totalprice)-parseFloat(this.jcai.price)).toFixed(2)
    },
  }
}

</script>


