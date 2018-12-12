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
      <button @click="clearKey">取消</button>
    </div>
    <div
      class="history"
      v-show="searchList.length==0"
    >
      <div class="top">
        <text>历史搜索</text>
        <i
          @click="clearHistory"
          class="iconfont icon-shanchu"
        > </i>
      </div>
      <div class="bottom">
        <div
          class="item"
          v-for="(item, index) in historyList"
          :key="index"
          @click="clickSearch(item)"
        >{{item}}</div>
        <!-- <div class="item">大米</div>
        <div class="item">糯米</div> -->
      </div>
    </div>
    <div
      class='search-box'
      v-show="searchList.length!=0"
    >
      <!-- 顶部的排序 -->
      <div class='order-box'>
        <div
          class="option"
          :class="{active:orderOption==0}"
          @click="orderOption=0"
        >
          综合
        </div>
        <div
          class="option"
          :class="{active:orderOption==1}"
          @click="orderOption=1"
        >
          销量
        </div>
        <div
          class="option"
          :class="{active:orderOption==2}"
          @click="orderOption=2;isUp=!isUp"
        >
          价格
          <div class="arrow-box">
            <i
              class='iconfont icon-jiantoushang'
              :class="{active:isUp}"
            ></i>
            <i
              class='iconfont icon-jiantouxia'
              :class="{active:!isUp}"
            ></i>
          </div>
        </div>
      </div>
      <!-- 底部的结果 需要滚动 -->
      <scroll-view scroll-y>
        <!-- 使用计算属性实现 -->
        <a
          class="item"
          v-for="(item, index) in orderList"
          :key="item.goods_id"
          :href="'/pages/detail/main?id='+item.goods_id"
        >
          <div class="left">
            <img
              :src="item.goods_small_logo"
              alt=""
            >
          </div>
          <div class="right">
            <div class="title">{{item.goods_name}}</div>
            <div class="price">{{item.goods_price}}</div>
          </div>
        </a>
      </scroll-view>
    </div>
  </div>
</template>
<script>
// 导入 hxios
import hxios from "../../utils/index";
export default {
  data: function() {
    return {
      // 搜索历史
      historyList: [],
      //   搜索内容
      key: "",
      // 查询的结果
      searchList: [],
      // 专门用来排序的数组
      _searchList:[],
      // 筛选的条件字段
      orderOption: 0,
      // 标记价格排序的字段
      isUp: false
    };
  },
  // 计算属性
  computed: {
    // 获取排序之后的数组
    orderList() {
      // 导入如何排序 取决于
      if (this.orderOption == 0) {
        // 综合排序 返回默认数组
        return this.searchList;
      } else if (this.orderOption == 1) {
        // 销量排序 sort是原数组排序
        // 专门弄一个排序的数组
        return this._searchList.sort((a, b) => {
          return a.goods_id - b.goods_id;
        });
      }else if(this.orderOption==2){
        // 升序 和降序
       return this._searchList.sort((a,b)=>{
          if(this.isUp==true){
            return a.goods_price - b.goods_price
          }else{
            return b.goods_price - a.goods_price
          }
        })
      }
    }
  },
  methods: {
    // 查询数据
    search() {
      // 重复内容判断
      // 判断是否有相同的内容 如果有 删除旧的 再追加新的
      let index = this.historyList.indexOf(this.key);
      if (index != -1) {
        this.historyList.splice(index, 1);
      }
      // 长度限制
      // 长度超过了 5个  每次追加的时候 再删除第一个即可
      if (this.historyList.length >= 5) {
        this.historyList.shift();
      }

      // console.log(this.key);
      this.historyList.push(this.key);
      // 网络数据获取 hxios
      hxios.get(`api/public/v1/goods/search?query=${this.key}`).then(res => {
        // console.log(res);
        // 初始数组
        this.searchList = res.data.message.goods;
        // 专门弄一个排序的数组
        this._searchList = JSON.parse(JSON.stringify(this.searchList));
      });
    },
    // 清除历史记录
    clearHistory() {
      // 清除历史记录
      this.historyList = [];
    },
    // 点击搜索结果
    clickSearch(key) {
      // 填充到搜索框中
      this.key = key;
      // 执行搜索
      this.search();
    },
    // 清空搜索
    clearKey() {
      this.key = "";
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
$uRed: #eb4450;
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

// 搜索的排序
.search-box {
  .order-box {
    display: flex;
    height: 100rpx;
    border-bottom: 1px solid gray;
    .option {
      flex: 1;
      text-align: center;
      line-height: 100rpx;
      font-size: 25rpx;
      .arrow-box {
        .iconfont {
          display: block;
        }
      }
    }
    .option:last-child {
      display: flex;
      justify-content: center;
      .iconfont {
        width: 20rpx;
        height: 15rpx;
        margin-bottom: 5rpx;
      }
    }

    // 高亮的样式
    .option.active {
      color: $uRed;
    }
    .option.active {
      .iconfont {
        color: black;
      }
      .iconfont.active {
        color: $uRed;
      }
    }
  }
  // 内容区域
  scroll-view {
    // 约束死高度
    height: 1112rpx;
    padding: 0 20rpx;
    box-sizing: border-box;
    .item {
      height: 260rpx;
      border-bottom: 1rpx solid gray;
      display: flex;
      .left {
        padding: 30rpx;
        img {
          display: block;
          width: 200rpx;
          height: 200rpx;
        }
      }
      .right {
        flex: 1;
        padding: 30rpx 0;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        .title {
          text-overflow: -o-ellipsis-lastline;
          overflow: hidden;
          text-overflow: ellipsis;
          display: -webkit-box;
          -webkit-line-clamp: 2;
          line-clamp: 2;
          -webkit-box-orient: vertical;
          font-size: 28rpx;
        }
        .price {
          font-size: 30rpx;
          color: $uRed;
          &::before {
            content: "¥";
            font-size: 20rpx;
          }
        }
      }
    }
  }
}
</style>
