<template>
  <div class="app-init pay-page scroll-box">
    <header-back title="活动详情"></header-back>

    <div class="padding-box">
      <div class="activityItem">
        <img :src="image">
        <p class="discrib">{{ content }}</p>
        <button class="joinActivity">参加活动</button>
      </div>
    </div>

  </div>
</template>

<script type="text/ecmascript-6">
  import headerBack from '../../components/header-back'
  import axios from 'axios'
  import VueDB from '../../util/vue-db/vue-db'


  let DB = new VueDB();

  export default{
    name: 'activity',
    data(){
      return{
        image : "http://onm9xh80o.bkt.clouddn.com/banner.PNG",
        content :"限时促销"
      }
    },
    mounted(){
      var activityId = DB.getItem ('activityId');
      axios.get('/user', {
            params: {
              id: activityId
            }
        })
        .then(response=> {
         this.image=response.data.data.image;
         this.content=response.data.data.content;
        })
        .catch(error=> {
          this.$router.replace('/error/404')
        });
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
  .joinActivity{
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
  .activityItem{
    padding-bottom: 0.2rem;
  }
</style>
