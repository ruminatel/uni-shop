<template>
  <!-- 防止闪烁 -->
  <view v-if="goods_info.goods_name" class="goods-detail-container ">
    <!-- 轮播图区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <swiper-item v-for="(item,i) in goods_info.pics" @click="preview(i)">
        <image :src="item.pics_big"></image>
      </swiper-item>
    </swiper>
    
    <!-- 商品信息区域 -->
    <view class="goods-info-box">
      <!-- 商品价格 -->
      <view class="price">￥{{goods_info.goods_price}}</view>
      <!-- 商品主体区域 -->
      <view class="goods-info-body">
        <!-- 商品名称 -->
        <view class="goods-name">{{goods_info.goods_name}}</view>
        <!-- 收藏 -->
        <view class="favi">
          <uni-icons type="star" size="18"></uni-icons>
          <view>收藏</view>
        </view>
      </view>
      <!-- 运费 -->
      <view class="yf">快递：免运费</view>
      
      <!-- 商品详情页 -->
      <rich-text :nodes="goods_info.goods_introduce"></rich-text>
      
      <!-- 底部导航组件区域 -->
      <view class="goods_nav">
        <uni-goods-nav :fill="true"  :options="options" :buttonGroup="buttonGroup"  @click="onClick" @buttonClick="buttonClick" />
      </view>
    </view>
  </view>
</template>

<script>
  import {mapState,mapMutations,mapGetters } from 'vuex'
  
  export default {
    computed: {
      ...mapState('m_cart',['cart']),
      ...mapGetters('m_cart',['total'])
    },
    watch: {
      // 侦听器函数形式定义,首页加载不会触发,使用 对象形式 即可
      // total(newVal) {
      //   // console.log(newVal)
      //   const findResult = this.options.find(x => x.text === '购物车')
        
      //   if(findResult) {
      //     findResult.info = newVal
      //   }
      // }
      total: {
        handler(newVal) {
         const findResult = this.options.find(x => x.text === '购物车')
         
         if(findResult) {
           findResult.info = newVal
         }
        },
        immediate: true
      }
    },
    data() {
      return {
        // 商品详情
        goods_info: {},
        // 底部导航
        options: [{
        			icon: 'shop',
        			text: '店铺',
        			infoBackgroundColor:'#007aff',
        			infoColor:"red"
        		}, {
        			icon: 'cart',
        			text: '购物车',
        			info: 0
        		}],
        	    buttonGroup: [{
        	      text: '加入购物车',
        	      backgroundColor: '#ff0000',
        	      color: '#fff'
        	    },
        	    {
        	      text: '立即购买',
        	      backgroundColor: '#ffa200',
        	      color: '#fff'
        	    }
        	    ]
      };
    },
    onLoad(options) {
      // console.log(options)
      const goods_id = options.goods_id
      this.getGoodsDetail(goods_id)
    },
    methods: {
      ...mapMutations('m_cart',['addToCart']),
      // 请求商品详情数据方法
      async getGoodsDetail(goods_id) {
        const {data : res} = await uni.$http.get('/api/public/v1/goods/detail',{goods_id})
        if(res.meta.status !== 200) return uni.$showMsg()
        
        res.message.goods_introduce = res.message.goods_introduce.replace(/<img /g, '<img style="display:block"').replace(/webp/g,'jpg')
        this.goods_info = res.message
      },
      // 轮播图预览效果
      preview(i) {
        uni.previewImage({
          current: i,
          urls: this.goods_info.pics.map(x => x.pics_big)
        })
      },
      // 底部左侧点击
      onClick(e) {
        // console.log(e)
        if(e.content.text === '购物车') {
          uni.switchTab({
            url: '/pages/cart/cart'
          })
        }
      },
      // 底部右侧按钮的点击事件处理函数
      buttonClick(e) {
        // console.log(e)
        // 判断是否点击了 加入购物车 按钮
        if(e.content.text === '加入购物车') {
          // goods_id, goods_name, goods_price, goods_count, goods_small_logo, goods_state
          const goods = {
            goods_id: this.goods_info.goods_id,     // 商品的Id  
            goods_name: this.goods_info.goods_name,  // 商品的名称
            goods_price: this.goods_info.goods_price,  // 商品的价格
            goods_count: 1,                             // 商品的数量
            goods_small_logo: this.goods_info.goods_small_logo,  // 商品的图片
            goods_state: true                           // 商品的勾选状态
          }
          
           // 通过 this 调用映射过来的 addToCart 方法，把商品信息对象存储到购物车中
          this.addToCart(goods)
        }
      }
    }
  }
</script>

<style lang="scss">
  // 轮播图区域
swiper {
  height: 750rpx;
  image {
    height: 100%;
    width: 100%;
  }
}

// 商品信息区域
.goods-info-box {
  padding: 0 10px;
  padding-right: 0;
  .price {
    color: #c00000;
    font-size: 18px;
    margin: 10px 0;
  }
  .goods-info-body {
    display: flex;
    justify-content: space-between;
    .goods-name {
      font-size: 13px;
      padding-right: 10px;
    }
    .favi {
      width: 120px;
      font-size: 12px;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      color: gray;
      border-left: 1px solid #efefef;
    }
  }
  .yf {
    padding: 10px 0;
    font-size: 12px;
    color: gray;
  }
}

// 底部导航组件区域
.goods-detail-container {
  padding-bottom: 50px;
}
.goods_nav {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
}
</style>
