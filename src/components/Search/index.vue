<template>
  <div class="search_body">
      <div class="search_input">
          <div class="search_input_wrapper">
              <i class="iconfont icon-sousuo"></i>
              <input type="text" v-model="message">
          </div>
      </div>
      <div class="search_result">
          <h1>电影/电视剧/综艺</h1>
          <ul>
              <li v-for="item in movieList" :key="item.id">
                  <div class="img"><img :src="item.img | setWH('128.180')" alt=""></div>
                  <div class="info">
                      <p><span>{{item.nm}}</span><span>{{item.sc}}</span></p>
                      <p>{{item.enm}}</p>
                      <p>{{item.cat}}</p>
                      <p>{{item.rt}}</p>
                  </div>
              </li>
          </ul>
      </div>
  </div>
</template>

<script>
export default {
    name:'Search',
    data(){
        return {
            message : '',
            movieList:[]
        }
    },
    methods:{
         cancelRequest(){
            if(typeof this.source ==='function'){
                this.source('终止请求')
            }
        }
    },
    watch:{
        message(newVal){
            this.cancelRequest();
            let that=this ;
            let cityId = this.$store.state.city.id;
            this.axios.get('/api/searchList?cityId='+cityId+'&kw='+newVal,{
                cancelToken:new this.axios.CancelToken(function(c) {
                    that.source = c;
                })
            }).then((res) => {
                let msg= res.data.msg;
                let movies = res.data.data.movies;
                if(msg && movies){
                    this.movieList = res.data.data.movies.list;
                }
            }).catch((err) => {
                if (axios.isCancel(err)) {
                    console.log('Rquest canceled', err.message); //请求如果被取消，这里是返回取消的message
                } else {
                    //handle error
                    console.log(err);
                }
            });
        }
    },
}
</script>

<style scoped>
.search_body{flex: 1;overflow: auto;}
.search_body .search_input{padding: 8px 10px;background: #f5f5f5;border-bottom: 1px solid #e5e5e5;}
.search_body .search_input_wrapper{padding: 0 10px;border: 1px solid #e6e6e6;border-radius: 5px;background: #fff;display: flex;}
.search_body .search_input_wrapper i{font-size: 16px;padding: 4px 0;}
.search_body .search_input_wrapper input{border: none;font-size: 13px;color: #333;padding: 4px 0;outline: none;}
.search_body .search_result h1{font-size: 15px;color: #999;padding: 9px 15px;border-bottom: 1px solid #e6e6e6;}
.search_body .search_result li{border-bottom: 1px dashed #c9c9c9;padding: 10px 15px;box-sizing: border-box;display: flex;}
.search_body .search_result .img{width: 60px;float: left;}
.search_body .search_result .img img{width:100%;}
.search_body .search_result .info{float: left;margin-left: 15px;flex: 1;}
.search_body .search_result p{height: 22px;display: flex;line-height: 22px;font-size: 12px;}
.search_body .search_result p:nth-of-type(1) span:nth-of-type(1){font-size: 18px;flex: 1;}
.search_body .search_result p:nth-of-type(1) span:nth-of-type(2){font-size: 16px;color: #fc7103;}
</style>