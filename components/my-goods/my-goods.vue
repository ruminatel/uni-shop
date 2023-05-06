<template>
  <view>
    <view class="goods-item">
      <!-- 左侧图片区域 -->
      <view class="goods-item-left">
        <radio :checked="goods.goods_state" color="#c00000" v-if="showRadio" @click="radioChangeHandler"></radio>
        <image :src="goods.goods_small_logo || defaultPic" class="goods-pic"></image>
      </view>
      <!-- 右侧商品详细信息 -->
      <view class="goods-item-right">
        <!-- 商品标题 -->
        <view class="goods-name">{{goods.goods_name}}</view>
        <!-- 商品价格 -->
        <view class="goods-info-box">
          <view class="goods-price">￥{{goods.goods_price | tofixed}}</view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    name:"my-goods",
    data() {
      return {
        // 默认的空图片
        defaultPic: 'https://img3.doubanio.com/f/movie/8dd0c794499fe925ae2ae89ee30cd225750457b4/pics/movie/celebrity-default-medium.png'
      };
    },
    props: {
      goods: {
        type: Object,
        defaul: {}
      },
      // 按钮显示状态
      showRadio: {
        type: Boolean,
        default: false
      }
    },
    methods: {
      // radio 组件的点击事件处理函数
      radioChangeHandler() {
        this.$emit('radio-change',{
          goods_id: this.goods.goods_id,
          goods_state: !this.goods.goods_state
        }
        )
      }
    },
    filters: {
      // 把数字处理为带两位小数点的数字
      tofixed(num) {
        return Number(num).toFixed(2)
      }
    }
  }
</script>

<style lang="scss">
.goods-item {
  display: flex;
  padding: 10px 5px;
  border-bottom: 1px solid #f0f0f0;
  .goods-item-left {
    margin-right: 5px;
    .goods-pic {
      width: 100px;
      height: 100px;
      display: block;
    }
  }
  .goods-item-right {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    .goods-name {
      font-size: 13px;
    }
    .goods-info-box {
      .goods-price {
        font-size: 16px;
        color: #c00000;
      }
    }
  }
}

// 左侧图片区域
.goods-item-left {
  margin-right: 5px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  .goods-pic {
    width: 100px;
    height: 100px;
  }
}
</style>