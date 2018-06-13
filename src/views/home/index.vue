<template>
  <div class="app-init home-index">
    <header class="public-header">
      <form class="search-box clear">
        <div class="fl logo">
          <!-- <span class="iconfont icon-xiaomi"></span> -->
          <img :src="logo" class="logoicon">
        </div>
        <div class="input-box" @click="$router.openPage('/search')">
          <span class="iconfont icon-sousuo-copy absolute"></span>
          <input type="text" class="input-controller" placeholder="搜索商品名称" readonly>
        </div>
        <div class="myself-center fr" @click="$router.openPage('/mine')">
          <span class="iconfont icon-smile"></span>
        </div>
      </form>
    </header>




    <div ref="view01" class="app-init scroll-box tab-box mi-tj footer-hack" v-show="navListActiveIndex == 0">
      <swiper :list="bannerList"></swiper>
      <div class="nav-mi-tj clear">
        <a href="javascript:;" class="fl goodsType">
          <!-- <img @click="$router.openPage('/pay')" src="http://oz3tayfme.bkt.clouddn.com/show.liluo.cc/2018011022292984819.png" alt=""> -->
          <div @click="changeType('生鲜水果')">
            <svg class="icona iconType" aria-hidden="true">
                <use xlink:href="#icon-39"></use>
            </svg>
            <p>生鲜水果</p>
          </div>
        </a>
        <a href="javascript:;" class="fl goodsType">
          <div @click="changeType('休闲零食')">
            <svg class="icona iconType" aria-hidden="true">
                <use xlink:href="#icon-26"></use>
            </svg>
            <p>休闲零食</p>
          </div>
        </a>
        <a href="javascript:;" class="fl goodsType">
          <div @click="changeType('奶水饮品')">
            <svg class="icona iconType" aria-hidden="true">
                <use xlink:href="#icon-29"></use>
            </svg>
            <p>奶水饮品</p>
          </div>
        </a>
        <a href="javascript:;" class="fl goodsType">
          <div @click="changeType('粮油厨房')">
            <svg class="icona iconType" aria-hidden="true">
                <use xlink:href="#icon-75"></use>
            </svg>
            <p>粮油厨房</p>
          </div>
        </a>
        <a href="javascript:;" class="fl goodsType">
          <div @click="changeType('家庭套餐')">
            <svg class="icona iconType" aria-hidden="true">
                <use xlink:href="#icon-100"></use>
            </svg>
            <p>家庭套餐</p>
          </div>
        </a>
        <a href="javascript:;" class="fl goodsType">
          <div @click="changeType('进口好货')">
            <svg class="icona iconType" aria-hidden="true">
                <use xlink:href="#icon-85"></use>
            </svg>
            <p>进口好货</p>
          </div>
        </a>
        <a href="javascript:;" class="fl goodsType">
          <div @click="changeType('方便速食')">
            <svg class="icona iconType" aria-hidden="true">
                <use xlink:href="#icon-51"></use>
            </svg>
            <p>方便速食</p>
          </div>
        </a>
        <a href="javascript:;" class="fl goodsType">
          <div @click="changeType('个人护理')">
            <svg class="icona iconType" aria-hidden="true">
                <use xlink:href="#icon-96"></use>
            </svg>
            <p>个人护理</p>
          </div>
        </a>
      </div>

      <div class="shop-item">
        <div class="shop-box clear">

          <div class="shop-box-item" v-for="target in shoplist" @click="$router.openPage('/detail/'+target.id)">
            <img v-lazy="getTitle.title+target.picture" alt="">
            <p class="title">{{ target.name }}</p>
            <p class="money">
              <span class="small">￥</span> {{ target.price }}
            </p>
          </div>

        </div>
      </div>
    </div>
     <div class="loading-box" :class="{ 'active' : loaded }" v-show="hide">

      <span class="load-ani iconfont icon-jiazai"></span>

    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import swiper from '../../components/swiper'
  import VueDB from '../../util/vue-db/vue-db'
  import iconfont from '../../assets/js/iconfont.js'
  import axios from 'axios'



  let DB = new VueDB()

  export default {
    data() {
      return {
        logo: "../../static/img/logo1.png",
        bannerList : [
          {
            src: '../../static/img/banner1.jpg',
            href: '/detail/1001'
          },{
            src: '../../static/img/banner2.jpg',
            href: '/detail/1019'
          },{
            src: '../../static/img/banner3.png',
            href: '/detail/1019'
          },{
            src: '../../static/img/banner4.png',
            href: '/detail/1019'
          } 
        ],
        shoplist : [],
        navListActiveIndex: 0,
        loaded: false,
        hide: true
      }
    },
    components: {
      swiper
    },
    mounted() {
      // 设置N个盒子的scroll top

       this.navListActiveIndex = DB.getItemOnce('home-index-nowIndex').toNumber() || 0

      setTimeout(()=>{
        this.$refs.view01.scrollTop = DB.getItemOnce('home-index-view01').toNumber() || 0
      }, 10)
      // 获取商品列表
      axios.get(this.getTitle.title+'/showAllGoods')
          .then(response=> {
          if(response.data.status==1){
            this.shoplist= response.data.data;
            this.loaded = true;
            setTimeout(()=>{
              this.hide = false;
            }, 1000)
          }else{
            console.log(response.data.message);
          }
          })
          .catch(error=> {
            this.$router.replace('/error/404');
          });
    },
    methods: {
    // 根据分类改变显示
      changeType(type){
        this.loaded = false;
        this.hide = true;
        axios.get(this.getTitle.title+'/showAllGoodsByType',{
          params: {
            type:type
          }
        })
          .then(response=> {
            if(response.data.status==1){
              this.shoplist= response.data.data;
              this.loaded = true;
              setTimeout(()=>{
                this.hide = false
              }, 1000)
            }else{
              this.loaded = true;
              this.shoplist=[];
              setTimeout(()=>{
                this.hide = false
              }, 1000)
              console.log(response.data.message);
            }
           
          })
          .catch(error=> {
            this.$router.replace('/error/404')
          });
      }
    },
    beforeRouteLeave (to, from, next) {
      DB.setItem('home-index-view01', this.$refs.view01.scrollTop)

      DB.setItem('home-index-nowIndex', this.navListActiveIndex)
      next();
    }
  }
</script>

<style type="text/sass" lang="sass">

    @keyframes ani01
      0%
        transform: rotate(0deg)
      100%
        transform: rotate(360deg)
    @-webkit-keyframes ani01
      0%
        -webkit-transform: rotate(0deg)
      100%
        -webkit-transform: rotate(360deg)

    .home-index .tab-box
        top: 1.35rem
    .home-index .tab-box.mi-tj .nav-mi-tj a
        display: block
        width:  25%
    .home-index .shop-box
      background-color: #fff
    
    .loading-box
      position: absolute
      left: 0px
      top: 0px
      right: 0px
      bottom: 0px
      background-color: #000
      opacity: 0.6
      z-index: 999
      transition: all .4s ease
      .load-ani
        display: block
        position: absolute
        width: 4rem
        height: 4rem
        line-height: 4rem
        text-align: center
        color: #fff9f8
        font-size: 2rem
        top: 50%
        margin-left: -2rem
        margin-top: -2rem
        border-radius: 50%
        left: 50%
        -webkit-transform: scale(0)
        transform: scale(0)
        -webkit-animation: ani01 1s ease infinite
        animation: ani01 1s ease infinite


    .loading-box.active
      opacity: 0
</style>
<style type="text/css">

  .icona {
    /* 通过设置 font-size 来改变图标大小 */
    width: 1em; height: 1em;
    /* 图标和文字相邻时，垂直对齐 */
    vertical-align: -0.15em;
    /* 通过设置 color 来改变 SVG 的颜色/fill */
    fill: currentColor;
    /* path 和 stroke 溢出 viewBox 部分在 IE 下会显示
       normalize.css 中也包含这行 */
    overflow: hidden;
  }
  .goodsType{
    margin-top: 15px; 
  }
  .goodsType p{
    font-size: 20px;
    font-size: 0.4rem;
    color: rgb(102, 102, 102);
    text-align: center;
    width: 160px;
    width: 2.4rem;
  }
  .iconType{
    font-size: 95px;
    font-size: 1.75rem;
    text-align: center;
    width: 160px;
    width: 2.4rem;
  }
  .icon-smile{
    color: #fff;
  }
  .logoicon{
    margin-top: -0.1rem;
    width: 1rem;
  }
  .public-header .search-box .logo{
    padding: 0 0.2938rem !important;
  }
</style>
