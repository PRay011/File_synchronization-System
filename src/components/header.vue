<template>
  <div class="container">
    <el-text class="mx-1" size="large">File Synchronization System</el-text>
    <el-text class="mx-1" size="large" style="margin-left: 50%;">Your Device ID:</el-text>
    <el-text class="mx-1" size="large" style="margin-left: 1%;">{{ device_id }}</el-text>
  </div>
  <el-divider/>
</template>

<script>
import axios from "axios"

export default {
  data() {
    return {
      device_id: 'xxxxxxxx-xxxx- xxxx-xxxxxxxxxxxxxxxx'
    }
  },
  mounted: function () {
    this.ready()
  },
  methods: {
    ready() {
      let that = this
      let config = {
        method: 'get',
        url: '/api/deviceId',
      };
      axios(config)
          .then(function (response) {
            let res = response.data;
            that.device_id = res.data;
          })
          .catch(function (error) {
            console.log(error);
          });
    }
  },
}
</script>

<style scoped>
@import '../assets/css/header.css';
</style>
 