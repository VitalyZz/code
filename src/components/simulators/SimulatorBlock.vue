<template>
  <div class="simulator">
    <div class="title">{{ element.name }}</div>
    <router-link :to="'/simulator/' + element.id" class="forward-btn">Перейти</router-link>
  </div>
</template>

<script>
export default {
  props: ['element']
}
</script>

<style scoped lang="scss">
  .simulator {
    margin-top: 5px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    padding: 70px 40px;
    width: 270px;
    height: 220px;
    box-sizing: border-box;
    position: relative;
    align-items: center;
    background: rgba(0, 0, 0, 0.3);
    border-radius: 5px;

    .title {
      font-size: 22px;
      font-weight: bold;
      color: #fff;
      text-align: center;
    }

    .forward-btn {
      position: absolute;
      bottom: 20px;
      padding: 5px 25px;
      border: 3px solid #FFFFFF;
      border-radius: 30px;
      background-color: transparent;
      font-weight: bold;
      font-size: 18px;
      color: #fff;
      cursor: pointer;
      transition: .3s;
      
      &:hover {
        background-color: #fff;
        color: #36404A;
      }
    }
  }

  /* Large Devices, Wide Screens */
  @media only screen and (max-width: 1200px) {
    /* */
  }

  /* Medium Devices, Desktops */
  @media only screen and (max-width: 992px) {
    /* */
  }

  /* Small Devices, Tablets */
  @media only screen and (max-width: 768px) {
    .simulator {
      margin-bottom: 30px;
      width: 250px;
      height: 200px;

      .title {
        font-size: 20px;
      }

      .forward-btn {
        padding: 5px 25px;
        border-radius: 30px;
        font-size: 16px;
      }
    }
  }

  /* Extra Small Devices, Phones */
  @media only screen and (max-width: 480px) {
    /* */
  }

  /* Custom, iPhone Retina */
  @media only screen and (max-width: 320px) {
    /* */
  }
</style>