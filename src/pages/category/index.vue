<template>
  <div class="nav-container">
    <!-- 使用搜索组件 -->
    <search></search>
    <!-- 分类区域 -->
    <div class="category-box">
      <div class="left">
        <scroll-view scroll-y>
          <div
            v-for="(item, index) in categoryList"
            :key="item.cat_id"
            class="item"
            :class="selectIndex==index?'active':''"
            @click="selectIndex=index"
          >
            {{item.cat_name}}
          </div>
        </scroll-view>
      </div>
      <div class="right">
        <scroll-view scroll-y>
          <img
            class="title-img"
            src="/static/icon/titleImage.png"
            alt=""
          >
          <div
            class="section"
            v-for="(item, index) in categoryList[selectIndex].children"
            :key="index"
          >
            <div class="title">/&nbsp;&nbsp;{{item.cat_name}}&nbsp;&nbsp;/</div>
            <div class="items">
              <div
                class="item"
                v-for="(it, i) in item.children"
                :key="it.cat_id"
              >
                <img
                  :src="'https://autumnfish.cn/wx/'+it.cat_icon"
                  alt=""
                >
                <text>{{it.cat_name}}</text>
              </div>
            </div>
          </div>
        </scroll-view>
      </div>
    </div>
  </div>
</template>
<script>
// 导入组件
import search from "../../components/search";
// 导入自己抽取的工具函数
import hxios from "../../utils/index";
export default {
  // 数据
  data: function() {
    return {
      categoryList: [],
      // 选中的分类 索引
      selectIndex: 1
    };
  },
  //   注册组件
  components: {
    search
  },
  // 查询数据
  created() {
    hxios.get("api/public/v1/categories").then(res => {
      console.log(res);
      this.categoryList = res.data.message;
    });
  }
};
</script>
<style lang="scss">
$uRed: #eb4450;
// 设置高度 让scroll-view滚动
page {
  height: 100%;
  .nav-container {
    height: 100%;
    // div:not(.nav) {
    //   height: 100%;
    // }
    scroll-view {
      height: 100%;
    }
    .category-box {
      height: 100%;
      .left {
        height: 100%;
      }
      .right {
        height: 100%;
        scroll-view {
          height: 100%;
        }
      }
    }
  }
}

.category-box {
  display: flex;
  .left {
    width: 200rpx;
    // 注册的滚动区域
    scroll-view {
      .item {
        height: 100rpx;
        text-align: center;
        line-height: 100rpx;
        font-size: 26rpx;
        // 高亮样式
        &.active {
          color: $uRed;
          position: relative;
        }
        // 高亮的伪元素
        &.active:before {
          content: "";
          position: absolute;
          width: 10rpx;
          height: 60rpx;
          background-color: $uRed;
          left: 0;
          top: 20rpx;
        }
      }
    }
  }
  //   右侧
  .right {
    flex: 1;
    scroll-view {
      padding: 16rpx;
      // 怪异盒模型
      box-sizing: border-box;
      .title-img {
        width: 100%;
        height: 180rpx;
      }
      .section {
        .title {
          padding: 40rpx 0;
          text-align: center;
          font-size: 24rpx;
        }
        .items {
          display: flex;
          flex-wrap: wrap;
          .item {
            // flex:1;
            width: 33.333%;
            img {
              width: 120rpx;
              height: 80rpx;
              display: block;
              margin: 0 auto;
            }
            text {
              text-align: center;
              display: block;
              font-size: 22rpx;
              padding: 20rpx;
            }
          }
        }
      }
    }
  }
}
</style>
