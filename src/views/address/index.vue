<template>
  <div class="main">
    <div class="classification-header">
      <header-back title="收货地址管理"></header-back>
    </div>
    <!-- 收货地址列表 -->
  </div>
</template>
<script type="text/ecmascript-6">
  import headerBack from '../../components/header-back'
  import axios from 'axios'

  export default {
    name: 'mine-page',
    data() {
      return {

      }
    },
    methods: {
      // 获取收货地址列表
      getAddress() {
        let url = 'http://18146ym266.iask.in:14832/showAllDelivery?id=180610123903234546';
        axios.get(url)
        .then(function (res) {
          console.log('拉取的地址数据 ↓')
          let data = res.data.data
          console.log(data)
        })
        .catch(function (error) {
          console.log(error)
        })
      }
    },
    components: {
      headerBack
    }
  }
</script>