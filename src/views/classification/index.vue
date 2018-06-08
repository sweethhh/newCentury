<template>
  <div class="app-init classification">
    <div class="classification-header">
      <header-back title="活动"></header-back>
    </div>

    <div class="wrap-box scroll-box">
      <div class="activityItem" @click="$router.openPage('/activity')"  v-for="item in activity">
        <img :src="item.image">
        <p class="discrib">{{ item.content }}</p>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import headerBack from '../../components/header-back'
  import _ from 'lodash'
  import VueDB from '../../util/vue-db/vue-db'
  import axios from 'axios'


  let DB = new VueDB();

  export default{
    name: 'classification',
    data(){
      return {
        active: 0,
        dataItem: [
        ],
        offset: [],
        activity : []
      }
    },
    components: {
      headerBack
    },
    methods: {

    },
    mounted() {
      axios.get('./static/server/activity.json')
        .then(response=> {
         this.activity=response.data.data;
         DB.setItem('activityId',response.data.data.id);
        })
        .catch(error=> {
          this.$router.replace('/error/404')
        });
    },
    
  }
</script>

<style type="text/sass" lang="sass">
  @import "../../assets/sass/util"
  .classification
    background-color: #fff

    .wrap-box
      position: absolute
      width: 100%
      top: getIphonese(100px)
      left: 0px
      bottom: $footerHeight

    .left-menu
      width: getIphonese(133px)
      left: 0px
      top: 0px
      bottom: 0px
      border-right: 1px solid #efefef
      overflow-x: hidden
      @include box-sizing

      ul
        padding-bottom: 0.44rem
      li.item
        margin-top: getIphonese(56px)
        text-align: center
        -webkit-transition: all .1s ease
        transition: all .1s ease
      li.item.active
        color: #fb7d34
        transform: scale(1.2)
    .right-box
      left: getIphonese(133px)
      top: 0px
      right: 0px
      bottom: 0px
      .item
        padding-top: 0.8rem
      .title
        text-align: center
        padding-bottom: 0.2rem
        span
          position: relative
          display: inline-block

          &:after,&:before
            display: inline-block
            width: getIphonese(34px)
            height: 1px
            top: 50%
            background-color: #e0e0e0
            position: absolute
            content: ''
          &:after
            left: getIphonese(-50px)
          &:before
            right: getIphonese(-50px)
      .shop-item-wrap
        .shop-item
          text-align: center
          float: left
          width: 33.3%
          color: #757575
          margin-bottom: 0.1rem
          @include f12px
          img
            width: getIphonese(80px)
            padding-bottom: 0.1rem
</style>
<style type="text/css">
  .activityItem{
    width: 100%;
    margin-bottom: 7px;
    /*border-bottom: 1px solid rgba(153,153,153,0.3);*/
    box-shadow: 1px 0px 5px rgba(153,153,153,0.4);
  }
  .activityItem p{
    height: 1rem;
    line-height: 1rem;
    width: 100%;
    text-align: center;
    color: #999;
    font-size: 0.5rem;
  }
  .activityItem img{
    width: 100%;
    height: 3rem;
  }
</style>
