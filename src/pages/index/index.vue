<template>
  <div class="index nav-container">
    <!-- 使用search 组件 -->
    <search></search>
    <!-- 轮播图 -->
    <swiper
      indicator-dots
      autoplay
      circular
      class="swiper"
    >
      <swiper-item
        v-for="(item, index) in swiperList"
        :key="index"
      >
        <image
          mode="aspectFill"
          :src="item.image_src"
        ></image>
      </swiper-item>
    </swiper>
    <!-- 分类 -->
    <div class="category-box">
      <div
        class="item"
        v-for="(item, index) in categoryList"
        :key="index"
      >
        <image :src="item.image_src"></image>
        <text>{{item.name}}</text>
      </div>
    </div>
    <!-- 楼层数据 -->
    <div
      class="floor-box"
      v-for="(item, index) in floorList"
      :key="index"
    >
      <div class="title">
        <h2>{{item.floor_title.name}}</h2>
        <img
          :src="item.floor_title.image_src"
          alt=""
        >
      </div>
      <div class="bottom">
        <!-- Vue动画的时候 必须给key  平时可以不给 key的作用(dom元素的唯一标记 让vue尽可能高效的重用这个dom元素) -->
        <div
          class="item"
          v-for="(it, i) in item.product_list"
        >
          <img
            :src="it.image_src"
            alt=""
          >
        </div>
      </div>
    </div>
    <!-- 底线 -->
    <div class="bottom">
      <!-- :before :after的区别 内容的前面 内容的后面 只能让哪些标签使用  -->
      <i class="iconfont icon-xiao">我是有底线的</i>
    </div>
    <!-- 返回顶部 -->
    <div
      class="backtop"
      v-show="isShow"
      @click="backTop"
    >
      <i class="iconfont icon-jiantoushang"></i>
      顶部
    </div>
  </div>
</template>
<script>
// 导入 自己抽取的 hxios
import hxios from "../../utils/index";

// 引入 search
import search from '../../components/search'

export default {
  data: function() {
    return {
      // 轮播图数组
      swiperList: [],
      // 分类数据
      categoryList: [],
      // 楼层数据
      floorList: [],
      // 是否显示返回顶部
      isShow: false
    };
  },
  methods: {
    // 调用微信的api 滚到顶部
    backTop() {
      wx.pageScrollTo({
        scrollTop: 0
        // duration: 300
      });
    }
  },
  // 注册组件
  components:{
    search
  },
  // 获取数据
  created() {
    // 轮播图数据
    // hxios.get("api/public/v1/home/swiperdata").then(res => {
    //   //   console.log(res);
    //   this.swiperList = res.data.message;
    //   // 返回 hxios
    // });
    let hx1 = hxios.get("api/public/v1/home/swiperdata");
    // 分类数据
    let hx2 = hxios.get("api/public/v1/home/catitems");
    // 楼层数据
    let hx3 = hxios.get("api/public/v1/home/floordata");

    // 一次性获取所有数据
    Promise.all([hx1, hx2, hx3]).then(res => {
      // console.log(res);
      // 轮播图
      this.swiperList = res[0].data.message;
      // 分类
      this.categoryList = res[1].data.message;
      // 楼层
      this.floorList = res[2].data.message;
    });
  },
  // 小程序生命周期钩子
  onPageScroll(e) {
    console.log(e);
    // console.log('滚了')
    if (e.scrollTop > 150) {
      this.isShow = true;
    } else {
      this.isShow = false;
    }
  }
};
</script>

<style lang="scss" >
// 定义变量
$uRed: #ff2d4a;
.index {
  background-color: #ccc;
}

// 轮播图
.swiper {
  image {
    width: 100%;
  }
}
// 分类
.category-box {
  display: flex;
  padding: 24rpx 0 29rpx;
  .item {
    flex: 1;
    image {
      display: block;
      width: 95rpx;
      height: 95rpx;
      margin: 0 auto;
    }
    text {
      font-size: 24rpx;
      display: block;
      text-align: center;
      margin-top: 15rpx;
    }
  }
}

// 楼层数据
.floor-box {
  .title {
    position: relative;
    h2 {
      position: absolute;
      color: #ff7b94;
      font-size: 35rpx;
      font-weight: 700;
      top: 50%;
      left: 16rpx;
      transform: translateY(-50%);
    }
    img {
      width: 100%;
      height: 90rpx;
    }
  }
  .bottom {
    padding: 20rpx 16rpx;
    overflow: hidden;
    .item {
      width: 33.333%;
      float: left;
      padding-right: 10rpx;
      box-sizing: border-box;
      img {
        display: block;
        width: 100%;
      }
    }
    .item:not(:first-child) {
      padding-bottom: 10rpx;
      img {
        height: 235rpx;
      }
    }
    .item:first-child img{
      height: 480rpx;
    }
  }
}

//  底部
.bottom {
  text-align: center;
  display: block;
  color: #999999;
}
// 返回顶部
.backtop {
  text-align: center;
  position: fixed;
  width: 90rpx;
  height: 90rpx;
  right: 10rpx;
  bottom: 10rpx;
  border-radius: 50%;
  background-color: $uRed;
  color: white;
  font-size: 24rpx;
  .iconfont {
  }
}
</style>
