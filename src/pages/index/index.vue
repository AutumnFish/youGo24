<template>
  <div class="index">
    <!-- 顶部的搜索栏 -->
    <div class="nav">
      <i class="iconfont icon-sousuo"></i>
      <input
        type="text"
        placeholder="搜索"
      >
    </div>
    <!-- 轮播图 -->
    <swiper
      indicator-dots
      autoplay
      circular
      class="swiper"
    >
      <swiper-item v-for="(item, index) in swiperList" :key="index">
        <image
          mode="aspectFill"
          :src="item.image_src"
        ></image>
      </swiper-item>
    </swiper>
  </div>
</template>
<script>
// 导入 自己抽取的 hxios
import hxios from "../../utils/index";
export default {
  data: function() {
    return {
      // 轮播图数组
      swiperList: []
    };
  },
  // 获取数据
  created() {
    hxios.get("api/public/v1/home/swiperdata").then(res => {
      //   console.log(res);
      this.swiperList = res.data.message;
    });
  }
};
</script>

<style lang="scss" >
// 定义变量
$uRed: #ff2d4a;
.index{
    padding-top:100rpx ;
}
.nav {
  padding: 20rpx 16rpx;
  background-color: $uRed;
  position: fixed;
  width: 100%;
  left: 0;
  top: 0;
  //   怪异盒模型
  box-sizing: border-box;
  input {
    background-color: white;
    height: 60rpx;
    text-align: center;
    color: #bbb;
    font-size: 24rpx;
  }
  i.icon-sousuo {
    color: #bbb;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-220%, -50%);
  }
}
// 轮播图
.swiper{
    image{
        width: 100%;
    }
}
</style>
