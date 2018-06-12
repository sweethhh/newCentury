<template>
  <div class="app-init pay-page scroll-box">
    <header-back title="注册"></header-back>

    <div class="padding-box loginPanel">
      <svg class="icona iconButton" aria-hidden="true">
        <use xlink:href="#icon-zhuce"></use>
      </svg>
      <div class="line">
        <svg class="icona iconButtonSmall" aria-hidden="true">
          <use xlink:href="#icon-shouji"></use>
        </svg>
        <input type="number" name="userName" class="input-controller" placeholder="手机" v-model=userName >
      </div>
      <div class="line">
        <svg class="icona iconButtonSmall" aria-hidden="true">
          <use xlink:href="#icon-yanzhengma"></use>
        </svg>
        <input type="text" name="imageCode" class="input-controller inputTel" placeholder="图片验证码" v-model=imageCode >
        <img onclick="this.src='getVerify' +'?id=' +  Math.random()" src="/getVerify" class="codeImg">
      </div>
      <div class="line">
        <svg class="icona iconButtonSmall" aria-hidden="true">
          <use xlink:href="#icon-yanzhengma"></use>
        </svg>
        <input type="text" name="code" class="input-controller inputTel" placeholder="验证码" v-model=code >
        <button class="getTelCode" @click="getCode()" :style="bgStyle" :disabled="buttonDisable">{{ getCodeText }}</button>
      </div>
      <div class="line">
        <svg class="icona iconButtonSmall" aria-hidden="true">
          <use xlink:href="#icon-mima"></use>
        </svg>
        <input type="password" name="password" class="input-controller" placeholder="密码" v-model=password >
      </div>
      <div class="line">
        <svg class="icona iconButtonSmall" aria-hidden="true">
          <use xlink:href="#icon-mima"></use>
        </svg>
        <input type="password" name="passwordConfirm" class="input-controller" placeholder="确认密码" v-model=passwordConfirm >
      </div>
      <button class="loginButton" @click="register()">注册</button>
    </div>
    <div class="loading-box" :class="{ 'active' : loaded }" v-show="hide">

      <span class="load-ani iconfont icon-jiazai"></span>

    </div>
    <div class="alert" id="alert" v-show="alertHide">
      <svg class="icona iconAlert" aria-hidden="true">
        <use xlink:href="#icon-jinggao"></use>
      </svg>
      <p>{{ alert }}</p>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import headerBack from '../../components/header-back'
  import axios from 'axios'

  export default{
    name: 'login',
    data(){
      return {
        userName: "",
        password: "",
        imageCode:"",
        imageCodeGet:"", // 从后台获取的图片验证码字符串
        code:"",
        codeGet:"",// 从后台获取的手机验证码字符串
        passwordConfirm:"",
        imageCodeSrc:"", // 从后台获取的图片验证码URL
        loaded: false,
        hide: true,
        buttonDisable:false,
        bgStyle:"background-color: #e6423b;",
        getCodeText:"获取验证码",
        alert:"",
        alertHide:false
      }
    },
    methods :{
      // 弹出窗
      alertMsg(msg){
        var alertPanel=document.getElementById("alert");
        alertPanel.style="display:block;";
        this.alert=msg
        this.alertHide=true;
        setTimeout(function(){
          this.alertHide=false;
          alertPanel.style="display:none;";
        },1000);
      },
    // 注册
      register(){
        if(this.userName==""||this.password==""||this.code==""){
          this.alertMsg("信息未填写完整");
        }else if(this.password!=this.passwordConfirm){
          // 提示两次输入密码不同
          this.alertMsg("两次输入密码不同");

        }else{
          axios.post('/register', {
            userName: this.userName,
            password: this.password,
            code: this.code,
            name:'用户'
          })
          .then(function (response) {
            if(response.data.status==1){
              // 注册成功
              this.alertMsg("注册成功");
              console.log(response);
              // 跳转
              $router.openPage('/login');
            }else{
              this.alertMsg("注册失败，请稍后重试。");
            }
            
          })
          .catch(function (error) {
            this.alertMsg(error);

            console.log(error);
          });
        }
      },
      // 获取手机验证码
      getCode(){
        if(this.userName.length!=11){
          // 弹出窗口
          this.alertMsg("手机号格式错误");
        }else if(imageCode!=imageCodeGet){
          // 弹出窗口
          this.alertMsg("图片验证码错误");
        }else{
          // 发送手机号码到后台
          axios.get('/getCode', {
            params:{
              userName: this.userName
            }
        })
        .then(function (response) {
          // 存储手机验证码
          // this.codeGet=response.data.codeGet;
          console.log(response);
          // 禁用发送按钮60s
          this.buttonDisable=true,
          this.bgStyle="background-color: #999;"
          let timeLast=60;//倒计时60s
          var timer=setInterval(function(){
            if(timeLast>0){
              timeLast--;
            }else{
              // 释放按钮
              this.buttonDisable=false,
              this.bgStyle="background-color: #e6423b;"
              timeLast=60;
            }
          },100);
          setTimeOut(clearInterval(timer),6000);
        })
        .catch(function (error) {
          console.log(error);
        });
        }
      }
    },
    mounted(){
      // 获取图片验证码
        /* axios.get('/')
          .then(response=> {

            this.imageCodeSrc = response.data.imageCodeSrc;
            this.imageCodeGet = response.data.imageCodeGet;
            this.loaded = true;
            setTimeout(()=>{
              this.hide = false
            }, 1000)
          })
          .catch(error=> {
            this.$router.replace('/error/404')
          }); */
    },
    components: {
      headerBack
    }
  }
</script>

<style type="text/sass" lang="sass">
  .pay-page
    background-color: #fff
    .padding-box
      padding: 0.4rem
      color: #333
      .color
        color: orange
      ul
        padding-left: 0.8rem
      li
        list-style: disc
</style>
<style type="text/css">
  .loginPanel{
    width: 100%;
    text-align: center;
    background-color: #fff;
    padding: 0 !important;
  }
  .loginPanel .iconButton{
    margin-top: 0.5rem;
    font-size: 4rem;
    color: #e6423b;
  }
  .loginPanel .iconButtonSmall{
    font-size: 0.7rem;
    display: inline-block;
    color: #999;
  }
  .loginPanel .input-controller{
    font-size: 0.4rem;
    padding: .2rem;
    display: inline-block;
    background-color: #fff;
    border: 1px solid #999;
    border-radius: 5px;
    width: 6rem;
    margin: 0.2rem auto;

  }
  .loginButton{
    width: 70%;
    display: block;
    margin: 0.5rem auto;
    border: 1px solid #e6423b;
    background-color: #e6423b;
    color: #fff;
    border-radius:5px;
    font-size: 0.5rem;
    height: 1rem;
    line-height: 1rem;
  }
  .loginPanel a{
    color: #999;
    display: inline-block;
    margin:0 1.7rem; 
    padding: 0;
  }
  .inputTel{
    width: 4rem !important;
  }
  .getTelCode{
    width: 2rem;
    border: none;
    color: #fff;
    padding: 0.18rem 0.1rem;
    border-radius: 5px;
    font-size: 0.2rem;
    background-color: #e6423b;
  }
  .codeImg{
    display: inline-block;
    width: 1.9rem;
    height: 0.8rem;
    margin-top: 0.2rem;
    border: 1px solid #eee;
  }
  .getCode{
    display: inline-block;
    font-size: 0.24rem;
    width: 2rem;
    padding: 0.18rem 0.1rem;
    margin: 0 !important;
  }
  .line{
    line-height: 0.8rem;
  }
  .alert{
    padding: 0.5rem;
    position: absolute;
    z-index: 200;
    top:5rem;
    left: 2.7rem;
    text-align: center;
    width: 3.5rem;
    border-radius: 5px;
    background-color: rgba(0,0,0,0.7);
    color: #fff;
    font-size: 2.5rem;
  }
  .alert svg{
    margin-top: -10px;
  }
  .alert p{
    font-size: 0.5rem;
  }
</style>
