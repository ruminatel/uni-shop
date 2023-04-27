<template>
  <view>
    <view class="goods-list">
      <view v-for="(goods,i) in goodsList" :key="i" @click="gotoDetail(goods)">
       <my-goods :goods="goods"></my-goods>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 请求参数对象
        queryObj: {
          // 查询关键字
          query: '',
          // 商品分类ID
          cid: '',
          // 页码值
          pagenum: 1,
          // 每页显示数据条数
          pagesize: 10
        },
        // 商品列表数据
        goodsList: [],
        // 商品列表数据总条数
        total: 0,
        // 节流阀 ,  是否正在请求数据
        isloading: false
      };
    },
    onLoad(options) {
      // console.log(options)
      this.queryObj.query = options.query || ''
      this.queryObj.cid = options.cid || ''
      // console.log(this.queryObj)
      
      // 发起获取商品列表数据请求
      this.getgoodsList()
    },
    methods: {
      // 获取商品列表数据
      async getgoodsList(cb) {
        // 获取数据前打开节流阀
        this.isloading = true
        const {data : res} = await uni.$http.get('/api/public/v1/goods/search',this.queryObj)
        // 为下拉刷新,即可调用cb回调
        cb && cb()
        // 关闭节流阀
        this.isloading = false
        // console.log(res)
        if(res.meta.status !== 200) return this.$showMsg()
        this.goodsList = [...this.goodsList,...res.message.goods]
        this.total = res.message.total
      },
      gotoDetail(goods) {
        uni.navigateTo({
          url: '/subpkg/goods_detail/goods_detail?goods_id=' + goods.goods_id
        })
      }
    },
    // 监听上拉触底事件
    onReachBottom () {
      // 判断是否还有下一页数据
      if(this.queryObj.pagenum * this.queryObj.pagesize >= this.total) return uni.$showMsg('数据全部加载完毕')
      
      // 判断是否正在请求其它数据，如果是，则不发起额外的请求
      if(this.isloading) return 
      // console.log('ok')
      this.queryObj.pagenum ++
      this.getgoodsList()
    },
    // 下拉刷新
    onPullDownRefresh () {
      this.queryObj.pagenum = 1,
      this.total = 0,
      this.isloading = false,
      this.goodsList = []
      
      this.getgoodsList(() => uni.stopPullDownRefresh())
    }
  }
</script>

<style lang="scss">

</style>
