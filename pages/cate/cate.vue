<template>
  <view>
    <!-- 自定义组件样式 -->
    <!-- <my-search :bgcolor="pink" :radius="0"></my-search> -->
    <my-search @click="gotoSearch"></my-search>
    
    <view class="scroll-view-container">
      <!-- 左侧滚动 -->
      <scroll-view class="left-scroll-view" scroll-y="true" :style="{height: wh + 'px'}">
        <block v-for="(item,i) in cateList" :key="i">
          <view :class="['left-scroll-view-item', i === active ? 'active' : '']" @click="activeChanged(i)">{{item.cat_name}}</view>
        </block>
      </scroll-view>
      <!-- 右侧滚动 -->
      <scroll-view class="right-scroll-view" scroll-y="true" :style="{height: wh + 'px'}" :scroll-top="scrollTop">
        <view class="cate-lv2" v-for="(item2,i2) in cateLevel2" :key="i2">
          <view class="cate-lv2-title">/ {{item2.cat_name}} /</view>
          <!-- 动态渲染三级分类列表 -->
          <view class="cate-lv3-list">
            <view class="cate-lv3-item" v-for="(item3,i3) in item2.children" :key="i3" @click="gotoGoodsList(item3)">
              <image :src="item3.cat_icon"></image>
              <text>{{item3.cat_name}}</text>
            </view>
          </view>
        </view>
      </scroll-view>
    </view>
  </view>
</template>

<script>
  import badgeMix from '@/mixins/tabbar-badge.js'
  export default {
    mixins: [badgeMix],
    data() {
      return {
        // 设备屏幕可用高度
        wh: 0,
        // 获取的一级分类数据
        cateList: [],
        // 默认选中项
        active: 0,
        // 二级分类列表
        cateLevel2: [],
        // 滚动条距离顶部的距离
        scrollTop: 0
      };
    },
    onLoad() {
      // 获取当前系统的信息
      const sysInfo = uni.getSystemInfoSync()
      // console.log(sysInfo)
      // -50 控制分类窗口的可用高度
      this.wh = sysInfo.windowHeight - 50
      
      this.getCateList()
    },
    methods: {
      async getCateList () {
        const {data : res} = await uni.$http.get('/api/public/v1/categories')
        if(res.meta.status !== 200) return uni.$showMsg()
        
        this.cateList = res.message
        // 为二级分类赋值,进入分类页面,默认显示第一个一级分类下的二级分类
        this.cateLevel2 = res.message[0].children
      },
      activeChanged (i) {
        this.active = i
        // 根据用户点击选中的当前一级分类,显示出对应的二次分类列表
        this.cateLevel2 = this.cateList[i].children
        
        this.scrollTop = this.scrollTop === 0 ? 1 : 0 
      },
      gotoGoodsList (item3) {
        uni.navigateTo({
          url:'/subpkg/goods_list/goods_list?cid=' + item3.cat_id
        })
      },
      gotoSearch () {
        // console.log('ok')
        uni.navigateTo({
          url: '/subpkg/search/search'
        })
      }
    }
  }
</script>

<style lang="scss">
.scroll-view-container {
  display: flex;
  
  .left-scroll-view {
    width: 120px;
    
    .left-scroll-view-item {
      line-height: 60px;
      background-color: #f7f7f7;
      text-align: center;
      font-size: 12px;
      
      &.active {
        background-color: #ffffff;
        position: relative;
        
        // 渲染激活项左侧的红色指示边线
        &::before {
          content: '';
          display: block;
          width: 3px;
          height: 30px;
          background-color: #c00000;
          position: absolute;
          top: 50%;
          left: 0px;
          transform: translateY(-50%);
        }
      }
    }
  }
  .cate-lv2-title {
    font-size: 12px;
    font-weight: bold;
    text-align: center;
    padding: 15px 0;
  }
}

  // 三级列表样式
  .cate-lv3-list {
    display: flex;
    flex-wrap: wrap;
    .cate-lv3-item {
      width: 33.33%;
      display: flex;
      flex-direction: column;
      align-items: center;

      image {
        width: 60px;
        height: 60px;
      }
      text {
        font-size: 12px;
      }
    }
  }
</style>
