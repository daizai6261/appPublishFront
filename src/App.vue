<template>
  <div id="app">
    <router-view/>
    <el-dialog
        title="提示"
        :visible.sync="isAppleDevice"
        width="30%"
    >
      <span>当前只支持安卓设备。</span>
    </el-dialog>
  </div>
</template>
<script>

  export default{
    data() {
      return {
        isAppleDevice: false,
      };
    },
    mounted() {
      const userAgent = navigator.userAgent.toLowerCase();
      // 检测是否为苹果设备
      if (! /iphone|ipod|ipad/.test(userAgent)) {
        this.isAppleDevice = true;
      }
    },
  };

</script>
<style lang="scss">
  #app {
    height: 100vh;
  }
</style>
