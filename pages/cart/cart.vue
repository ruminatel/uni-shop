<template>
  <view>
    <!-- 购物车商品列表的标题区域 -->
    <view class="cart-title">
      <uni-icons type="shop" size="18"></uni-icons>
      <text class="cart-title-text">购物车</text>
    </view>
    
    <!-- 商品列表区域 -->
    <block v-for="(goods,i) in cart" :key="i">
      <my-goods :goods="goods" :show-radio="true" @radio-change="radioChangeHandler"></my-goods>
    </block>
  </view>
</template>

<script>
  import badgeMix from '@/mixins/tabbar-badge.js'
  import {mapState,mapMutations} from 'vuex'
  export default {
    mixins: [badgeMix],
    computed: {
      ...mapState('m_cart',['cart'])
    },
    data() {
      return {
        
      };
    },
    methods: {
      ...mapMutations('m_cart',['updateGoodsState']),
      radioChangeHandler(e) {
        // console.log(e)
        this.updateGoodsState(e)
      }
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
