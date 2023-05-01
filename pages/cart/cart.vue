<template>
  <view>
    <!-- 购物车商品列表的标题区域 -->
    <view class="cart-title">
      <uni-icons type="shop" size="18"></uni-icons>
      <text class="cart-title-text">购物车</text>
    </view>
    
    <!-- 商品列表区域 -->
    <block v-for="(goods,i) in cart" :key="i">
      <my-goods :goods="goods"></my-goods>
    </block>
  </view>
</template>

<script>
  import badgeMix from '@/mixins/tabbar-badge.js'
  import {mapState} from 'vuex'
  export default {
    mixins: [badgeMix],
    computed: {
      ...mapState('m_cart',['cart'])
    },
    data() {
      return {
        
      };
    }
  }
</script>

<style lang="scss">
// 购物车商品列表的标题区域
.cart-title {
  height: 40px;
  display: flex;
  font-size: 14px;
  align-items: center;
  padding-left: 5px;
  border-bottom: 1px solid #efefef;
  .cart-title-text {
    margin-left: 5px;
  }
}
</style>
