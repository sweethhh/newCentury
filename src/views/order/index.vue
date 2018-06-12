<template>
  <div class="main">
    <div class="classification-header">
      <header-back title="我的订单"></header-back>
    </div>
    <!-- 订单类型栏 -->
    <div class="order-type">
      <span index="6" href="#" @click="changeType" :class="state===6 ? 'type-active':''">全部</span>
      <span index="0" href="#" @click="changeType" :class="state===0 ? 'type-active':''">待付款</span>
      <span index="1" href="#" @click="changeType" :class="state===1 ? 'type-active':''">待收货</span>
    </div>
      <!-- 订单列表 -->
      <div class="user-order">
        <ul>
          <li v-for="item in list">
            <!-- 全部订单 -->
            <div class="order-item item-finish" 
            v-if="state === 6 && (item.orderState === 6 || item.orderState === 7)">
              <!-- 订单信息 -->
              <div class="item-row">
                <p class="item-type">{{item.serviceName}}<span class="item-mentor montor-finish">导师<i>-</i>{{item.tutorName}}</span></p>
                <img class="item-img" src="http://otn43irfn.bkt.clouddn.com/mentor/image/finish.png" alt="全部">
              </div>
              <div class="item-row">
                <p class="item-time">{{timeStamp2String(item.time)}}</p>
                <p class="item-price price-finish">实付款<span>¥{{item.price}}</span></p>
              </div>
            </div>
            <!-- 待付款订单 -->
            <div class="order-item item-waite" v-if="item.orderState === state && item.orderState === 0">
              <!-- 订单信息 -->
              <div class="item-row">
                <p class="item-type">{{item.serviceName}}</p>
                <p class="item-state">待付款</p>
              </div>
              <div class="item-row">
                <p class="item-mentor">导师<i>-</i>{{item.tutorName}}</p>
                <p class="item-price">需支付<span>¥{{item.price}}</span></p>
              </div>
              <!-- 订单操作 -->
              <div class="item-select" v-if="item.orderState === 0">
                <span class="item-cancel" @click="cancelOrder">取消订单</span>
                <span class="no-show">{{item.orderId}}</span>
                <span class="item-pay" @click="payOrder">立即付款</span>
              </div>
            </div>
            <!-- 待收货订单 -->
            <div class="order-item item-talk" v-if="item.orderState === state && item.orderState === 1">
              <!-- 订单信息 -->
              <div class="item-row">
                <p class="item-type">{{item.serviceName}}</p>
                <p class="item-state">待收货</p>
              </div>
              <div class="item-row">
                <p class="item-mentor">导师<i>-</i>{{item.tutorName}}</p>
                <p class="item-price">实付款<span>¥{{item.price}}</span></p>
              </div>
            </div>
          </li>
        </ul>
        <!-- 无列表项 -->
        <div class="order-void" v-if="hasList">
          <p class="void-word">你还木有相关订单~</p>
          <span class="void-go" @click="goHome">去四处逛逛</span>
        </div>
      </div>
  </div>
</template>

<script>
  import headerBack from '../../components/header-back'
  import axios from 'axios'

  export default {
    data() {
      return {
        state: 0,
        hasList: false,
        count: 0,
        list: [],
        id: 1,
        orderId: 0
      }
    },
    components: {
      headerBack
    },
    mounted () {
      // 获取数据
      this.dataG()
      /* 页面加载完毕后检测是否有列表 */
      this.ifList()
    },
    updated () {
      /* 页面dom节点更新时检测是否有列表 */
      this.ifList()
    },
    methods: {
      // 改变激活样式
      changeType (event) {
        // 获取index
        let index = event.target.getAttribute('index')
        // 改变state
        this.state = parseInt(index)
      },
      // 检测是否有订单
      ifList () {
        /* 检测有无节点 */
        let hasList = document.getElementsByClassName('order-item')
        if (hasList.length === 0) {
          this.hasList = true
        } else {
          this.hasList = false
        }
      },
      // 去四处逛逛跳转
      goHome () {
        let url = '/home'
        this.$router.push({
          path: url
        })
      },
      // 拉取数据
      dataG () {
        let _this = this
        _this.state = 0
        // // 判断是否时才支付完
        // let ifpay = localStorage.getItem('ifpay')
        // // 如果是刚支付完就跳转到待收货
        // if (ifpay === 'true') {
        //   localStorage.setItem('ifpay', false)
        //   _this.state = 1
        // }
        // 获取订单列表
        let url = 'http://18146ym266.iask.in:14832/showOrderByUserId?id=180610123903234546';
        axios.get(url)
        .then(function (res) {
          console.log('拉取的订单数据 ↓')
          let data = res.data.data
          console.log(data)
          // // 拉取数据后以时间对订单进行排序
          // for (let i = 0; i < data.length; i++) {
          //   for (let j = i + 1; j < data.length; j++) {
          //     if (data[i].time < data[j].time) {
          //       let n = data[i]
          //       data[i] = data[j]
          //       data[j] = n
          //     }
          //   }
          // }
          _this.list = data
        })
        .catch(function (error) {
          console.log(error)
        })
        // if (_this.cancelorder) {
        //   _this.cancelorder = false
        // }
      },
      // 时间戳函数
      timeStamp2String (time) {
        var datetime = new Date()
        datetime.setTime(time)
        var year = datetime.getFullYear()
        var month = datetime.getMonth() + 1
        var date = datetime.getDate()
        return year + '年' + month + '月' + date + '日'
      },
      // 登陆函数
      logIn () {
        let openId = localStorage.getItem('openId')
        // 如果存在有效openId，则登陆
        if (openId && openId.length > 5) {
          let url = '/tutor/api/user/login?openId=' + openId
          axios.post(url)
          .then(function (res) {
            console.log(res)
          })
          .catch(function (err) {
            console.log(err)
          })
        }
      }
    },
    watch: {
      '$route': 'dataG'
    }
  }
</script>

<style scoped>
  .title {
    display: block;
    background-color: #e6423b;
    border: none;
    color: #fff;
    text-shadow: 1px 0px 1px #fff;
    border-radius: 5px;
    font-size: 0.5rem;
    padding: 0.2rem 0.5rem;
    margin: 0.5rem auto;
    margin-bottom: 0.5rem;
  }
  #app {
    height: auto;
  }
  .main {
    min-height: 100%;
    background-color: #F4F4F4;
  }
  /* 订单类型栏 */
  .order-type {
    background-color: #fff;
    font-size: 0.3rem;
    color: #000;
    line-height: 0.76rem;
    display: flex;
    justify-content: space-between;
    padding:0 1.22rem;
  }
  .order-type span {
    display: inline-block;
    width: 1rem;
    text-align: center;
    font-weight: bold;
  }
  .order-type .type-active {
    color: #e6423b;
    border-bottom: 0.04rem solid #e6423b;
  }
  /* 订单栏 */
  .user-order {
    padding:0.2rem 0.3rem 0.2rem 0.3rem;
  }
  /* 订单列表公共样式页面样式 */
  .order-item {
    padding: 0.3rem;
    background-color: #fff;
    margin-bottom: 0.2rem;  
    display: flex;
    flex-wrap: wrap;
    align-content: space-between;
    font-family: PingFangSC-Regular, sans-serif;
  }
  .item-row {
    flex-basis: 100%;
    display: flex;
    justify-content: space-between;
  }
  .item-type {
    font-family: PingFangSC-Semibold, sans-serif;
    font-size: 0.32rem;
    color:#333;
    font-weight: bold;
  }
  .item-state {
    font-size: 0.22rem;
    color:#bbb;
    line-height: 0.20rem;
    margin-top: 0.14rem;
  }
  .item-mentor {
    font-size: 0.22rem;
    margin-top: 0.18rem;
    color:#5c5c5c;
    font-weight: normal;
  }
  .item-mentor i {
    margin:0 0.01rem;
  }
  .item-price {
    font-size:0.24rem;
    color: #969696;
    margin-top: 0.18rem;
    line-height: 0.22rem;
  }
  .item-price span{
    font-family: PingFangSC-Semibold, sans-serif;
    font-size: 0.32rem;
    color: #ffbf71;
    margin-left: 0.20rem;
    line-height: 0.22rem;
    font-weight: bold;
  }
  /* 待付款订单样式 */
  .item-select {
    border-top: 0.01rem solid #eee;
    margin-top: 0.2rem;
    width: 100%;
    text-align: right;
    font-size: 0;
  }
  .item-select span {
    font-size: 0.24rem;
    display: inline-block;
    border:0.01rem solid #bbb;
    line-height: 0.5rem;
    width: 1.4rem;
    border-radius:0.08rem;
    text-align: center;
    font-family: PingFangSC-Regular, sans-serif;
    margin-left: 0.2rem;
    margin-top: 0.3rem;
  }
  .item-select .item-pay {
    color: #f9f9f9;
    background-color: #00caab;
  }
  .item-select .no-show {
    display: none;
  }
  /* 已完成订单样式 */
  .item-finish {
    padding:0.3rem 0.6rem 0.3rem 0.3rem;
  }
  .item-img {
    width: 1.2rem;
    height: 1rem;
    margin: -0.4rem -0.3rem 0 0;
  }
  .montor-finish {
    margin-left: 0.2rem;
    border-left:0.01rem solid #ebebeb;
    line-height: 0.3rem;
    padding-left: 0.2rem;
  }
  .item-time {
    font-size: 0.22rem;
    color: #969696;
  }
  .price-finish {
    margin-top: 0;
  }
  /* 无列表项 */
  .order-void {
    position: relative;
    top: 5rem;
    left: 50%;
    transform: translate(-50%,-50%);
    text-align: center;
  }
  .void-img {
    width: 3rem;
    height: 3rem;
    margin:0 auto;
    margin-bottom: 0.5rem;
  }
  .void-word {
    text-align: center;
    font-size: 0.34rem;
    margin-bottom:0.16rem;
  }
  .void-go {
    text-decoration:underline;
    text-align: center;
    font-size: 0.26rem;
  }
</style>