<template>
  <div id="app">
    <router-view/>
  </div>
</template>
<script>

  export default{
    data() {
      return {
        isAppleDevice: false,
      };
    },
  };

</script>
<style lang="scss">
  #app {
    height: 100vh;
  }
</style>
