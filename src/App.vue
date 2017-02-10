<template>
  <div>
    <headera class="header-index" :seller='seller'>我是头部</headera>
    <div class="tab border-1px">
      <div class="tab-item">
        <router-link to="/goods">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/ratings">单价</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/seller">商家</router-link>
      </div>
    </div>
    <router-view></router-view>
    <div class="footer">我是尾部</div>
  </div>
</template>

<script type="text/ecmascript-6">
  import headera from './components/header/headerIndex.vue';
  const SUCCESS_OK = 0;
  export default {
    name: 'app',
    data() {
      return {
        seller: {}
      };
    },
    components: {
      headera
    },
    created: function () {
      this.$http.get('/api/seller').then((response) => {
        response = response.body;
        // console.log(response.data);
        if (response.errno === SUCCESS_OK) {
          // console.log(this.seller);
          this.seller = response.data;
        }
      }, (response) => {
        console.log(response);
      });
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "./common/stylus/index.styl"

  .tab
    display:flex
    width:100%
    height:40px
    line-height:40px
    //border-bottom:1px solid rgba(2,17,27,0.1)
    border-1px(rgba(7, 17, 27, 0.1))
    .tab-item
      flex:1
      text-align:center
      & > a
        display:block

        font-size 14px
        color: rgb(77, 85, 93)
        &.active
          color:rgb(240,20,20)
</style>
