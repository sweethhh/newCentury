<template>
  <div class="app-init pay-page scroll-box">
    <header-back title="登录"></header-back>

    <div class="padding-box loginPanel">
      <svg class="icona iconButton" aria-hidden="true">
        <use xlink:href="#icon-denglu"></use>
      </svg>
      <div>
        <svg class="icona iconButtonSmall" aria-hidden="true">
          <use xlink:href="#icon-denglu"></use>
        </svg>
        <input type="text" name="userName" class="input-controller" placeholder="用户名" v-model=userName>
      </div>
      <div >
        <svg class="icona iconButtonSmall" aria-hidden="true">
          <use xlink:href="#icon-mima"></use>
        </svg>
        <input type="password" name="password" class="input-controller" placeholder="密码" v-model=password >
      </div>
      <button class="loginButton" @click="login()">登录</button>
      <a href="javascript:;">忘记密码</a>
      <a href="javascript:;" @click="$router.openPage('/register')">注册</a>
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
  import VueDB from '../../util/vue-db/vue-db'
  import qs from 'qs';


  let DB = new VueDB();

  export default{
    name: 'login',
    data(){
      return {
        userName: "",
        password: "",
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
      login(){
        var _this=this;
        if(this.userName!=""&&this.userName!=null&&this.password!=""&&this.password!=null){
          axios.post(this.getTitle.title+'/login', qs.stringify({
            userName: this.userName,
            password: this.password
          }))
          .then(function (response) {
            if(response.data.status==1){
                console.log(response);
                DB.setItem("userNameId",response.data.data.id);
                // 跳转
                _this.$router.push({name: 'mine'});
                // this.$router.open('/mine');
            }else{
              _this.alertMsg(response.data.message);
              console.log(response.data.message);
              
            }
           
          })
          .catch(function (error) {
            console.log(error);
          });
        }else{
          // 你还没有填写用户名密码
          _this.alertMsg("你还没有填写用户名密码");
        }
      }
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
    margin-top: 1rem;
    font-size: 5rem;
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
