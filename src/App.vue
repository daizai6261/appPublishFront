<template>
  <div id="app">
    <router-view/>
    <el-dialog
        title="提示"
        :visible.sync="isAppleDevice"
        width="30%"
        :before-close="handleClose"
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
        hasShownDialog: false,
      };
    },
    mounted() {
      const userAgent = navigator.userAgent.toLowerCase();
      // 检测是否为苹果设备

      if (/iphone|ipod|ipad/.test(userAgent) && !this.hasShownDialog) {
        this.isAppleDevice = true;
        this.hasShownDialog = true;
      }
    },
    methods: {
    handleClose(done) {
      this.isAppleDevice = false;
      done();
    },
  },
  };

</script>
<style lang="scss">
  #app {
    height: 100vh;
  }
</style>
