<template>
  <view>
    <!-- 搜索自定义组件 -->
    <view class="search-box">
      <my-search @click="gotoSearch"></my-search>
    </view>
    
    <!-- 轮播图区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <swiper-item v-for="( item , i ) in swiperList" :key="i">
        <navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
          <image :src="item.image_src"></image>
        </navigator>
      </swiper-item>
    </swiper>
    
    <!-- 分类导航区域 -->
    <view class="nav-list">
      <view class="nav-item" v-for="(item,i) in navList" :key="i" v-on:click="navClickHandler(item)">
        <image class="nav-image" :src="item.image_src"></image>
      </view>
    </view>
    
    <!-- 楼层显示区域 -->
    <view class="floor-list">
      <view class="floor-item" v-for="(item ,i) in floorList" :key="i">
        <image class="floor-title" :src="item.floor_title.image_src"></image>
        <!-- 楼层的图片区域 -->
        <view class="floor-img-box">
          <!-- 左侧 -->
          <navigator class="left-img-box" :url="item.product_list[0].url">
            <image :src="item.product_list[0].image_src" :style="{width: item.product_list[0].image_width + 'rpx'}" mode="widthFix"></image>
          </navigator>
          <!-- 右侧 -->
          <view class="right-img-box">
            <navigator class="right-img-item" v-for="(item2 ,i2) in item.product_list" :key="i2" v-if="i2 !== 0" :url="item2.url">
              <image :src="item2.image_src" :style="{width: item2.image_width + 'rpx'}" mode="widthFix"></image>
            </navigator>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
  import badgeMix from '@/mixins/tabbar-badge.js'
  export default {
    mixins: [badgeMix],
    data() {
      return {
        // 轮播图的数据列表，默认为空数组
        swiperList: [],
        // 分类导航
        navList: [],
        // 楼层数据
        floorList: []
      };
    },
    onLoad() {
      this.getSwiperList(),
      this.getNavList(),
      this.getFloorList()
    },
    // 获取轮播图数据的方法
    methods: {
      async getSwiperList() {
        // 发起请求
        const { data : res } = await uni.$http.get('/api/public/v1/home/swiperdata')
        // console.log(res)
        // 请求失败
        if(res.meta.status !== 200)  return uni.$showMsg()
        // 请求成功
        this.swiperList = res.message
        uni.$showMsg('数据请求成功')
      },
      // 获取分类导航数据方法
      async getNavList() {
        const {data : res} = await uni.$http.get('/api/public/v1/home/catitems')
        // console.log(res)
        if(res.meta.status !== 200) return uni.$showMsg()
        this.navList = res.message
      },
      // nav-item 项被点击时候的事件处理函数
      navClickHandler(item) {
        // 判断点击的是哪个 nav
        if(item.name === '分类') {
          uni.switchTab({
            url: '/pages/cate/cate'
          })
        }
      },
      // 楼层数据获取
      async getFloorList () {
        const {data : res} = await uni.$http.get('/api/public/v1/home/floordata')
        if(res.meta.status !== 200) return uni.$showMsg()
        
        // 对数据的 url 进行处理
        res.message.forEach( floor => {
          floor.product_list.forEach(prod => {
            prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
          })
        })
        this.floorList = res.message
      },
      gotoSearch () {
        uni.navigateTo({
          url: '/subpkg/search/search'
        })
      }
    }
  }
</script>

<style lang="scss">
  // 自定义搜索样式
  .search-box {
    // 粘性固定 
    position: sticky;
    top: 0;
    z-index: 999;
  }
  
// 轮播图样式
swiper {
  height: 330rpx;
  
  .swiper-item,
  image {
    width: 100%;
    height: 100%;
  }
}

// 分类导航
.nav-list {
  display: flex;
  justify-content: space-around;
  margin: 15rpx 0;
  
  .nav-image {
    width: 128rpx;
    height: 140rpx;
  }
}

// 楼层
.floor-title {
  width: 100%;
  height: 60rpx;
}

.right-img-box {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
}

.floor-img-box {
  display: flex;
  padding-left: 15rpx;
}
</style>
