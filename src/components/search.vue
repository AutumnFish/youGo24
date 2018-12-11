<template>
  <!-- 顶部的搜索栏 -->
  <div class="nav">
    <i class="iconfont icon-sousuo"></i>
    <input
      type="text"
      placeholder="搜索"
    >
  </div>
</template>
<script>
export default {};
</script>
<style lang="scss">
// 定义变量
$uRed: #ff2d4a;
// 搜索框样式
.nav {
  padding: 20rpx 16rpx;
  background-color: $uRed;
  position: fixed;
  width: 100%;
  left: 0;
  top: 0;
  z-index: 999;
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
</style>
