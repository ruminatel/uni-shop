<template>
  <view class="my-search-container" :style="{'background-color' : bgcolor}" @click="searchBoxHandler">
    <view class="my-search-box" :style="{'border-radius' : radius + 'px'}">
      <!-- 展示 icon 图标 -->
      <uni-icons type="search" size="17"></uni-icons>
      <text class="placeholder">搜索</text>
    </view>
  </view>
</template>

<script>
  export default {
    name:"my-search",
    data() {
      return {
        
      };
    },
    props: {
      // 自定义背景颜色
      bgcolor: {
        type: String,
        default: '#c00000'
      },
      // 自定义圆角边框大小
      radius: {
        type: Number,
        default: 18
      }
    },
    methods: {
      searchBoxHandler () {
        // console.log('```')
        this.$emit('click')
      }
    }
  }
</script>

<style lang="scss">
.my-search-container {
  height: 50px;
  // background-color: #c00000;
  display: flex;
  align-items: center;
  padding: 0 15px;
    
  .my-search-box {
    height: 36px;
    background-color: #fff;
    // border-radius: 18px;
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    
    .placeholder {
      font-size: 15px;
      margin-left: 5px;
    }
  }
}
</style>