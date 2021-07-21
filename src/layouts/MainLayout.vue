<template>
  <div class="container">
    <router-view/>
  </div>
</template>

<script>
export default {

}
</script>

<style scoped lang="scss">
  .container {
    min-height: calc(100vh - 70px);
    padding: 70px 20px 0 20px;
    display: flex;
    margin: 0 auto;
    flex-direction: column;
    max-width: 1300px;
    height: 100%;
    position: relative;
  }
</style>