<template>
  <div class="highlight_wrapper">
    <div class="outer" :style="{borderColor: color}">
      <div class="inner" :style="{backgroundColor: color}"></div>
    </div>
    <span class="text" :style="{color: color}"><slot></slot></span>
  </div>
</template>

<script>
export default {
  props: {
    isHighlight: {
      type: Boolean,
      default: false,
      required: false,
    }
  },
  computed: {
    color() {
      return this.isHighlight === true ? '#0DFF92' : '#A2ACBF'
    }
  }
}
</script>

<style scoped lang="scss">
  .highlight_wrapper {
    display: flex;
    align-items: center;
    margin-left: 10px;

    .outer {
      background-color: transparent;
      border: 3px solid;
      width: 20px;
      height: 20px;
      border-radius: 100%;
      position: relative;
      display: inline-block;
      margin-right: 10px;
      cursor: pointer;

      .inner {
        width: 12px;
        height: 12px;
        border-radius: 100%;
        position: absolute;
        top: calc(50% - 6px);
        left: calc(50% - 6px);
      }
    }

    .text {
      cursor: pointer;
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
    .highlight_wrapper {
      margin-left: 10px;

      .outer {
        border: 3px solid;
        width: 14px;
        height: 14px;
        margin-right: 10px;

        .inner {
          width: 8px;
          height: 8px;
          top: calc(50% - 4.5px);
          left: calc(50% - 4.5px);
        }
      }

      .text {
        font-size: 13px;
      }
    }
  }

  /* Custom, iPhone Retina */
  @media only screen and (max-width: 320px) {
    /* */
  }
</style>