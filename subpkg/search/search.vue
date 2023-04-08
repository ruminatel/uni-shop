<template>
  <view>
    <!-- 顶部搜索框 -->
    <view class="search-box">
      <uni-search-bar @input="input" cancelButton="none" :radius="100"></uni-search-bar>
    </view>
    
    <!-- 搜索建议列表 -->
    <view class="sugg-list">
      <view class="sugg-item" v-for="(item,i) in searchResults" :key="i" @click="gotoDetail(item)">
        <view class="goods-name">{{item.goods_name}}</view>
        <uni-icons type="forward" size="16"></uni-icons>
      </view>
    </view>
    
    <!-- 搜索历史 -->
    <view class="history-box">
      <!-- 搜索标题区域 -->
      <view class="history-title">
        <text>搜索历史</text>
        <uni-icons type="trash" size="16" @click="cleanHistory"></uni-icons>
      </view>
      <!-- 列表展示区域 -->
      <view class="history-list">
        <uni-tag :text="item" v-for="(item,i) in historys" :key="i" @click="gotoGoodsList(item)"></uni-tag>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 搜索框输入防抖,延时器的 timerId
        timer: null,
        // 搜索关键词
        kw: '',
        // 搜索结果列表
        searchResults: [],
        // 搜索关键词的历史列表
        historyList: []
      };
    },
    onLoad() {
      this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')
    },
    methods: {
      input(e) {
        clearTimeout(this.timer)
        this.timer = setTimeout(() => {
          // console.log(e)
          this.kw = e
          // 根据关键词，查询搜索建议列表
          this.getSearchList()
        },500)
      },
      // 根据搜索关键词，搜索商品建议列表
      async getSearchList() {
        if(this.kw.length === 0) {
          this.searchResults = []
          return
        }
        
        // 发起请求，获取搜索建议列表
        const {data:res} = await uni.$http.get('/api/public/v1/goods/qsearch',{query : this.kw})
        // console.log(res)
        if(res.meta.status !== 200) return uni.$showMsg()
        this.searchResults = res.message
        
        // 1. 查询到搜索建议之后，调用 saveSearchHistory() 方法保存搜索关键词
        this.saveSearchHistory()
      },
      // 点击建议列表 根据id跳转至商品详情页
      gotoDetail(item) {
        // console.log(item.goods_id)
        uni.navigateTo({
          url:'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
        })
      },
      // 2. 保存搜索关键词的方法
      saveSearchHistory() {
        // this.historyList.push(this.kw)
        const set = new Set(this.historyList)
        set.delete(this.kw)
        set.add(this.kw)
        // 将 Set 对象转化为 Array 数组
        this.historyList = Array.from(set)
        // console.log(this.historyList)
        // 调用 uni.setStorageSync(key, value) 将搜索历史记录持久化存储到本地
        uni.setStorageSync('kw',JSON.stringify(this.historyList))
      },
      // 清空搜索历史
      cleanHistory() {
        // 清空 data 中保存的搜索历史
        this.historyList = []
        // 清空本地存储中记录的搜索历史
        uni.setStorageSync('kw','[]')
      },
      // 点击跳转到商品列表页面
      gotoGoodsList(kw) {
        uni.navigateTo({
          url: '/subpkg/goods_list/goods_list?query=' + kw
        })
      }
    },
    computed: {
      historys() {
        return [...this.historyList].reverse()
      }
    }
  }
</script>

<style lang="scss">
// 顶部搜索框
.search-box {
  position: sticky;
  top: 0;
  z-index: 999;
}

// 搜索建议列表
.sugg-list {
  padding: 0 5px;
  .sugg-item {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 10px 0;
    border-bottom: 1px solid #efefef;
    font-size: 12px;
    .goods-name {
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }
  }
}

// 搜索历史
.history-box {
  padding: 0 5px;
  
  .history-title {
    display: flex;
    justify-content: space-between;
    height: 40px;
    align-items: center;
    font-size: 13px;
    border-bottom: 1px solid #efefef;
  }
  
  .history-list {  
    display: flex;
    flex-wrap: wrap;
    .uni-tag {
      display: inline-block;
      margin-top: 5px;
      margin-right: 5px;
    }
  }
}
</style>
