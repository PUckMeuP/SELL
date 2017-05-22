<template>
  <div class="app">
    <v-header :seller="seller"></v-header>
    <div class="tab border-1px">
      <div class="tab-item"><a v-link="{path:'/seller'}">商家</a></div>
      <div class="tab-item"><a v-link="{path:'/goods'}">商品</a></div>
      <div class="tab-item"><a v-link="{path:'/ratings'}">评价</a></div>
    </div>
    <router-view :seller="seller" keep-alive></router-view>
  </div>
</template>
<script type="text/esmascript-6">
  import {urlParse} from 'common/js/url'; 
  import header from './components/header/header.vue';

  const ERR_OK = 0;

  export default {
    data() {
      return {
        seller: {
          id: (() =>{
            let queryParam = urlParse();
            return queryParam.id;
          })()
        }
      };
    },
    created() {
      this.$http.get('./api/seller?id=' + this.seller.id).then((response) => {
          response = response.body;
          if(response.errno === ERR_OK){
            this.seller = Object.assign({}, this.seller, response.data);
          }
      });
    },
    components: {
      'v-header': header
    }
  };
</script>

<style lang="scss" rel="stylesheet/scss">
 @import 'common/sass/minxin.scss';
  .app{
    .tab{
      display:flex;
      width:100%;
      height:px2rem(80px);
      line-height:px2rem(80px);
      @include border-1px-tb(bottom,rgba(7,17,27,.2));
      .tab-item{
        flex:1;
        text-align:center;
        a{
          display:block;
          font-size:px2rem(28px);
          color:rgb(77,85,93);
          font-weight:400;
          &.active{
            color:rgb(240,20,20);
            text-decoration:none;
          }
        }
      }
    }
  }
</style>
