<template>
  <div>

    <div class="loading-bar" :class="nowStatus">
      <span class="press"></span>
    </div>

    <div class="app-init">
      <transition :name="transitionName">
        <router-view></router-view>
      </transition>
    </div>


    <footer class="footer clear" :class="{ 'leave': leaveComp }">
      <div class="footer-item" :class="{ 'active': isActive('home') }" @click="open('/home')">
        <!-- <p class="icon">
          <span class="iconfont icon-shouye"></span>
        </p> -->
        <svg class="iconButtom" aria-hidden="true">
            <use xlink:href="#icon-home"></use>
        </svg>
        <p class="name">首页</p>
      </div>

      <div class="footer-item" :class="{ 'active': isActive('classification') }" @click="open('/classification')">
        <svg class="iconButtom" aria-hidden="true">
            <use xlink:href="#icon-gifts"></use>
        </svg>
        <!-- <p class="icon">
          <span class="iconfont icon-fenlei"></span>
        </p> -->
        <p class="name">活动</p>
      </div>

      <div class="footer-item shop-car" :class="{ 'active': isActive('shoppingcart') }" @click="open('/shoppingcart')">
        <svg class="iconButtom" aria-hidden="true">
            <use xlink:href="#icon-cart"></use>
        </svg>
       <!--  <p class="icon">
          <span class="iconfont icon-gouwuche"></span>
        </p> -->
        <p class="name">购物车</p>
        <span class="num" v-show="getShopCarLength > 0" :class="{ 'full': parseInt(getShopCarLength) >= 99 }" >{{ getShopCarLength >= 99 ? '99+' : getShopCarLength }}</span>
      </div>

      <div class="footer-item" :class="{ 'active': isActive('mine') }" @click="open('/mine')">
        <svg class="iconButtom" aria-hidden="true">
            <use xlink:href="#icon-account"></use>
        </svg>
        <!-- <p class="icon">
          <span class="iconfont icon-smile"></span>
        </p> -->
        <p class="name">我的</p>
      </div>
    </footer>

  </div>


</template>

<script>
  import Rem from '@/assets/js/rem'
  import { mapGetters } from 'vuex'

  new Rem();

  export default {
    name: 'app',
    data () {
      return {
        transitionName : 'slide-go',
        nowUrl: '/'
      }
    },
    watch: {
      '$route' (to, from) {

        if (from.query.time) {
          if (to.query.time > from.query.time) {
            this.transitionName = 'slide-go'
          } else {
            this.transitionName = 'slide-back'
          }
        } else {
          this.transitionName = 'slide-go'
        }

        this.nowUrl = to.fullPath
      }
    },
    computed: {
      ...mapGetters([
        'nowStatus',
        'getShopCarLength'
      ]),
      leaveComp() {
        var resout = true;
        var base = ['home', 'classification', 'shoppingcart', 'mine'];


        for(var i=0; i<base.length; i++){
          if(this.isActive(base[i])){
            resout = false;
          }
        }
        if(this.isActive('shoppingcart') && this.getShopCarLength > 0){
          resout = true;
        }
        return resout;
      }
    },
    methods: {
      open(link) {
        this.$router.openPage(link)
      },
      isActive(name) {
        return this.nowUrl.indexOf(name) != -1
      }
    }
  }
</script>



<style lang="sass" type="text/sass">
  @import "./assets/sass/public"
  @import './assets/font/iconfont.css'




</style>
<style type="text/css">
  .iconButtom{
    /* 通过设置 font-size 来改变图标大小 */
    width: 1em; height: 1em;
    /* 图标和文字相邻时，垂直对齐 */
    vertical-align: -0.15em;
    /* 通过设置 color 来改变 SVG 的颜色/fill */
    fill: currentColor;
    /* path 和 stroke 溢出 viewBox 部分在 IE 下会显示
       normalize.css 中也包含这行 */
    overflow: hidden;
    font-size: 0.75rem;
  }
</style>
