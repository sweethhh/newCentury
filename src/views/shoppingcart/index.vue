<template>
  <div class="app-init gouwuche-page">
    <header-back title="购物车"></header-back>

    <div class="scroll-box gouwu-view">

      <div class="shop-wrap" v-if="shopCarListLength == 0">
        <div class="empty" @click="$router.openPage('/')">
          <svg class="icona iconType" aria-hidden="true">
              <use xlink:href="#icon-konggouwuche"></use>
          </svg>
          <p>购物车是空的</p>
          <button>去选购</button>
        </div>
      </div>

      <div class="shop-wrap shop-list-wrap" v-else>
        <div class="shop-item clear" v-for="target in shopCarList">
          <div class="shop-img fl" @click="$router.openPage(target.link)">
            <img :src="target.img" alt="">
          </div>
          <div class="function-box fr">
            <p class="title">{{ target.title }}</p>
            <p class="money">售价：{{ target.money }}元</p>
            <div class="button-group">
              <div class="function-button">
                <span class="btn jia iconfont icon-jia" @click="jia(target)"></span>
                <span class="number">{{ target.length }}</span>
                <span class="btn jian iconfont icon-jian" @click="jian(target)"></span>
              </div>

              <span class="delete iconfont icon-del" @click="deleteShop(target)"></span>
            </div>
          </div>
        </div>

      </div>
    </div>

    <div class="shopcar-footer" v-show="shopCarListLength>0">
      <div class="item money">
        <p>共{{ shopCarListLength }}件</p>
        <p> <span class="mm">{{ getAllMoney }}</span> 元 </p>
      </div>
      <div class="item continue" @click="$router.openPage('/')">继续购物</div>
      <div class="item pay" @click="checkOut()">去结算</div>
    </div>
    <transition name='check'>
    <div class="checkOutBar" v-show="checkOutShow" transition='fade'>
      <div class="whitePanel">
        <div class="whitePanelTop">
            <svg class="icona iconType iconClose" @click="closePay()" aria-hidden="true">
                <use xlink:href="#icon-close"></use>
            </svg>
          <p>确认支付</p>
        </div>
        <svg class="icona iconType iconJinggao" aria-hidden="true" >
            <use xlink:href="#icon-jinggao"></use>
        </svg>
        <p>默认地址：{{ address }}</p>
        <p>总价<span>{{ getAllMoney }}</span>元，确定结算？</p>
        <button @click="pay()">支付</button>
      </div>
    </div>
  </transition>
  </div>
</template>

<script type="text/ecmascript-6">
  import headerBack from '../../components/header-back'
  import ShopCarTool from '../../util/shop-car-tool/index'
  import VueDB from '../../util/vue-db/vue-db-long'
  import axios from 'axios'


  let DB = new VueDB();


  export default{
    name: 'shop-car',
    components: {
      headerBack
    },
    data() {
      return {
        shopCarList: {},
        checkOutShow : false,
        address:"暂无",
        userName:""
      }
    },
    mounted() {
      // 获取当前用户名
      this.userName=DB.getItem('userName');
      this.shopCar = new ShopCarTool(this.$store)
      this.shopCarList = this.shopCar.getAll()

      // console.log(this.shopCarList)
    },
    computed: {
      shopCarListLength() {
        var n = 0;
        for(var i in this.shopCarList){
          n += this.shopCarList[i].length
        }
        return n
      },
      getAllMoney(){
        var n = 0;
        for(var i in this.shopCarList){
          n += this.shopCarList[i].money * this.shopCarList[i].length
        }
        return n
      }
    },
    methods: {
      jia(target) {
        this.shopCar.add(target)
      },
      jian(target) {
        this.shopCar.minus(target)
      },
      deleteShop(target){
        this.shopCar.remove(target.id)
        this.shopCarList = this.shopCar.getAll()
      },
      checkOut(){
        // 获取默认收货地址
        axios.get('/user', {
            params: {
              userName: this.userName
            }
        })
        .then(response=> {
         this.address=response.data.data.address;
        })
        .catch(error=> {
          this.$router.replace('/error/404')
        });
        this.checkOutShow=true;
     },
      closePay(){
        this.checkOutShow=false;
      },
      pay(){
        // 发送数据到后台
        axios.post('/user', {
          data:this.shopCarList
        })
        .then(function (response) {
          console.log(response);
          // 清空购物车
          this.shopCar.removeAll()
          this.shopCarList = this.shopCar.getAll()
          // 关闭支付面板
          this.closePay();
        })
        .catch(function (error) {
          console.log(error);
        });
      }
    }
  }
</script>

<style type="text/sass" lang="sass">
  @import "../../assets/sass/util"
  .gouwuche-page
    .gouwu-view
      position: absolute
      width: 100%
      left: 0
      background-color: #fff
      top: $backHeaderHeight
      bottom: $footerHeight
    .shop-list-wrap
      padding: getIphonese(20px) 0
      .shop-item
        padding: 0 0.4rem
        margin-bottom: 0.4rem
        border-bottom: 1px solid #eee
        padding-bottom: 0.4rem
      .shop-img
        width: getIphonese(178px)
        height: getIphonese(178px)
        border-radius: 4px
        border: 1px solid #eeeeee
        img
          width: 100%
          height: 100%
      .function-box
        width: getIphonese(380px)
        height: getIphonese(178px)
        .title
          color: #717171
        .money
          color: #999999
          @include f12px
          padding-top: getIphonese(10px)
          padding-bottom: getIphonese(10px)
        .button-group
          position: relative
        .function-button
          width: getIphonese(160px)
          height: getIphonese(52px)
          border: 1px solid #eeeeee
          line-height: getIphonese(52px)
          position: relative
          text-align: center
          line-height: getIphonese(52px)
          .btn
            position: absolute
            width: getIphonese(52px)
            height: getIphonese(52px)
            background-color: #fafafa
            text-align: center
            color: #666666
            top: 0
            .iconfont
              color: #C3C3C3
          .jia
            right: 0px
          .jian
            left: 0px
        .delete
          display: inline-block
          position: absolute
          right: 0
          top: 0
          line-height: getIphonese(52px)
          font-size: 0.56rem
          color: #C3C3C3
    .shopcar-footer
      position: absolute
      height: $footerHeight+0.01rem
      width: 100%
      left: 0px
      bottom: 0
      .item
        width: 33.33%
        float: left
        text-align: center
        height: $footerHeight+0.01rem
        line-height: $footerHeight+0.01rem
        @include f12px
      .money
        background-color: #ffffff
        color: #9a9a9a
        line-height: normal
        height: $footerHeight+0.01rem
        @include box-sizing
        padding-top: 0.2rem
        .mm
          font-size: 0.48rem
          color: #e6423b
          font-weight: bold
      .continue
        background-color: #f4f4f4
        color: #e6423b
      .pay
        background-color: #e6423b
        color: #fff
</style>
<style type="text/css">
  .empty{
    width: 100%;
    color: #999;
    text-align:center;
    padding-top :0.5rem;
  }
.empty p{
  margin: 0.2rem 0;
  font-size: 0.4rem;
}
.empty button{
  border: none;
  color: #fff;
  font-size: 0.3rem;
  background-color: #e6423b;
}
.checkOutBar{
  text-align: center;
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: rgba(0,0,0,0.5);
  line-height: 100%;
}
.checkOutBar svg{
  margin-top: 0.3rem;
  font-size: 3rem;
  color: rgba(230,66,59,0.8);

}
.checkOutBar p{
  font-size: 0.5rem;
  color: #818384;
  margin-top: 0.5rem;
}
.checkOutBar span{
  padding: 0 .2rem;
  color: rgba(0,0,0,0.8);
  font-weight: 600;
  font-size: 1rem;
}
.checkOutBar button{
  border: none;
  margin: 0.7rem;
  margin-top: 0.4rem;
  font-size: 0.5rem;
  width: 85%;
  border-radius: 5px;
  height: 0.9rem;
  background-color: rgba(230,66,59,0.8);
  color: #fff;
}

.whitePanel{
  position: absolute;
  bottom:0;
  width: 100%;
  height: 10rem;
  background-color: #fff;
}
.whitePanelTop{
  width: 100%;
  height: 1rem;
  border-bottom: 1px solid #CECECE;
}
.whitePanelTop p{
  text-align: center;
}
.iconClose{
  width: 1rem;
  font-size: 0.5rem !important;
  color: #CECECE !important;
  margin-top: 0 !important;
  position: absolute;
  left: 0;

}
.check-enter-active, .check-leave-active {
   transition: all 0.2s ease-out;
 }
.check-enter, .check-leave-to {
  opacity: 0;
}
</style>
