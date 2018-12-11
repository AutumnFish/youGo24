<template>
  <div class="search-container">
    <div class="search">
      <i class="iconfont icon-sousuo"></i>
      <input
        type="text"
        placeholder="请输入你想要的商品"
        @confirm="search"
        v-model.trim="key"
      >
      <button>取消</button>
    </div>
    <div class="history">
      <div class="top">
        <text>历史搜索</text>
        <i class="iconfont icon-shanchu"> </i>
      </div>
      <div class="bottom">
        <div class="item" v-for="(item, index) in historyList" :key="index">{{item}}</div>
        <!-- <div class="item">大米</div>
        <div class="item">糯米</div> -->
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data: function() {
    return {
      // 搜索历史
      historyList: [],
      //   搜索内容
      key: ""
    };
  },
  methods: {
    search() {
        // console.log(this.key);
        this.historyList.push(this.key);
    }
  },
  onShow() {
    // console.log('显示了')
    // 读取缓存
    wx.getStorage({
      key: "history",
      success: res => {
        // console.log(res.data);
        // 保存数据
        this.historyList = res.data;
      },
      fail: () => {
        console.log("失败啦");
      },
      complete: () => {}
    });
  },
  onUnload() {
    // console.log('卸载了');
    wx.setStorage({
      key: "history",
      //   插件提示的是 value 但是是错的 官方文档是 data
      //   value: this.historyList
      data: this.historyList
    });
  }
};
</script>
<style lang="scss">
.search-container {
  .search {
    height: 120rpx;
    background-color: #eeeeee;
    padding: 0 20rpx;
    box-sizing: border-box;
    position: relative;
    display: flex;
    align-items: center;
    .iconfont {
      position: absolute;
      left: 40rpx;
      top: 50%;
      transform: translateY(-50%);
    }
    input {
      padding-left: 70rpx;
      font-size: 26rpx;
      flex: 1;
      background: white;
      margin-right: 16rpx;
      height: 60rpx;
    }
    button {
      width: 160rpx;
      height: 60rpx;
      border-radius: 4rpx;
      border: 1rpx solid gray;
      text-align: center;
      line-height: 60rpx;
    }
  }
  .history {
    padding: 28rpx 20rpx 0;
    .top {
      display: flex;
      justify-content: space-between;
      font-size: 26rpx;
    }
    .bottom {
      display: flex;
      flex-wrap: wrap;
      padding-top: 30rpx;
      .item {
        padding: 16rpx 20rpx;
        background-color: #dddddd;
        margin-right: 30rpx;
        font-size: 26rpx;
        margin-bottom: 20rpx;
      }
    }
  }
}
</style>
