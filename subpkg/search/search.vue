<template>
  <view>
    <view class="search-box">
      <uni-search-bar @input="input" radius="100" cancel-button="none" ></uni-search-bar>
    </view>
    
    <!-- 搜索建议列表 -->
    <view class="sugg-list">
      <view class="sugg-item" v-for="(item,i) in searchResults" :key="i" @click="goodsDetail(item.goods_id)">
        <view class="goods-name">{{item.goods_name}}</view>
        <uni-icons type="arrowright" size="16"></uni-icons>
      </view>
    </view>
    
    <!-- 搜索历史 -->
    <view class="history-box">
      <!-- 标题区域 -->
      <view class="history-title">
        <text>搜索历史</text>
        <uni-icons type="trash" size="17"></uni-icons>
      </view>
      <!-- 列表区域 -->
      <view class="history-list" >
        <uni-tag :text="item" v-for="(item,i) in historyList" :key="i"></uni-tag>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        timer: null,
        kw: '',
        // 搜索结果列表
        searchResults: [],
        // 搜索关键词的历史记录
        historyList: ['iphone','mac','ipad']
      };
    },
    methods: {
      input(e){
        // 清除timer对应的定时器
        clearTimeout(this.timer)
        this.timer = setTimeout(() =>{
           // 如果 500 毫秒内，没有触发新的输入事件，则为搜索关键词赋值
          this.kw = e
          // 根据关键词，查询搜索列表
          this.getSearchResult()
          console.log(this.kw)
        },500)
      },
      // 根据搜索关键词，搜索商品列表
      async getSearchResult(){
        if(this.kw === ''){
          this.searchResults = []
          return
        }
        // 发起请求，获取搜索建议列表
        const {data: res} = await uni.$http.get('/api/public/v1/goods/search',{query: this.kw})
        if(res.meta.status !== 200) return uni.$showMsg()
        this.searchResults = res.message.goods
        console.log(res)
      },
      goodsDetail(goods_id){
        // 跳转到商品详情页
        uni.navigateTo({
          url: '/subpkg/goods_detail/goods_detail?goods_id=' + goods_id
        })
      }
    }
  }
</script>

<style lang="scss">
.search-box {
  position: sticky;
  top: 0;
  z-index: 999;
}

.sugg-list {
  padding: 0 5px;

  .sugg-item {
    font-size: 12px;
    padding: 13px 0;
    border-bottom: 1px solid #efefef;
    display: flex;
    align-items: center;
    justify-content: space-between;

    .goods-name {
      // 文字不允许换行（单行文本）
      white-space: nowrap;
      // 溢出部分隐藏
      overflow: hidden;
      // 文本溢出后，使用 ... 代替
      text-overflow: ellipsis;
      margin-right: 3px;
    }
  }
}

.history-box {
  padding: 0 5px;

  .history-title {
    display: flex;
    justify-content: space-between;
    align-items: center;
    height: 40px;
    font-size: 16px;
    border-bottom: 1px solid #efefef;
  }

  .history-list {
    display: flex;
    flex-wrap: wrap;
   

    .uni-tag {
      margin-top: 5px;
      margin-right: 10px;
      background-color: #efefef;
      color: #000000;
      display: block;
    }
  }
}
</style>
