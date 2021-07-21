<template>
  <div class="words">
    <div class="word-block" v-for="(el, index) in words" :key="el.id_word" :style="{backgroundColor: el.color}">
      <div class="number">{{ index + 1 }}</div>
      <div class="word">{{ el.word }}</div>
    </div>
  </div>
</template>

<script>
export default {
  props: ['words']
}
</script>

<style scoped lang="scss">
  .words {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    margin-top: 20px;

    .word-block {
      display: flex;
      align-items: center;
      box-shadow: 8px 8px 10px rgba(0, 0, 0, 0.7);
      border-radius: 5px;
      padding: 10px 20px;
      word-break: break-all;

      &:not(:last-child) {
        margin-bottom: 15px;
      }

      .number {
        color: #fff;
        font-weight: bold;
      }

      .word {
        text-transform: uppercase;
        font-size: 18px;
        margin-left: 20px;
        color: #fff;
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
    /* */
  }

  /* Extra Small Devices, Phones */
  @media only screen and (max-width: 480px) {
    .words {
      margin-top: 20px;

      .word-block {
        border-radius: 5px;
        padding: 10px 20px;

        &:not(:last-child) {
          margin-bottom: 15px;
        }

        .number {
          font-size: 14px;
        }

        .word {
          font-size: 14px;
          margin-left: 20px;
        }
      }
    }
  }

  /* Custom, iPhone Retina */
  @media only screen and (max-width: 320px) {
    /* */
  }
</style>