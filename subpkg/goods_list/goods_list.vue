<template>
  <view>
    <view class="goods-list">
      <block v-for="(goods,i) in goodsList" :key="i">
        <view class="goods-item">
          <!-- 左侧图片区域 -->
          <view class="goods-item-left">
            <image :src="goods.goods_small_logo || defaultPic" class="goods-pic"></image>
          </view>
          <!-- 右侧商品详细信息 -->
          <view class="goods-item-right">
            <!-- 商品标题 -->
            <view class="goods-name">{{goods.goods_name}}</view>
            <!-- 商品价格 -->
            <view class="goods-info-box">
              <view class="goods-price">￥{{goods.goods_price}}</view>
            </view>
          </view>
        </view>
      </block>
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
        // 默认的空图片
        defaultPic: 'https://img3.doubanio.com/f/movie/8dd0c794499fe925ae2ae89ee30cd225750457b4/pics/movie/celebrity-default-medium.png'
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
      async getgoodsList() {
        const {data : res} = await uni.$http.get('/api/public/v1/goods/search',this.queryObj)
        // console.log(res)
        if(res.meta.status !== 200) return this.$showMsg()
        this.goodsList = res.message.goods
        this.total = res.message.total
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
</style>
