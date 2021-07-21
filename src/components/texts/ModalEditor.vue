<template>
  <div class="main">
    <textarea v-model="updatedText">{{ text }}</textarea>
    <div @click="$emit('closeModalEditor', updatedText)" class="close"></div>
  </div>
  <div class="dark" @click="$emit('closeModalEditor', updatedText)"></div>
</template>

<script>
export default {
  props: ['text'],
  emits: ['closeModalEditor'],
  data() {
    return {
      updatedText: this.text
    }
  }
}
</script>

<style scoped lang="scss">
  .main {
    display: block;
    background-color: rgba(0, 0, 0, 0.7);
    z-index: 10;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 80%;
    height: 80%;
    border-radius: 5px;

    textarea {
      padding: 10px 20px;
      height: 100%;
      width: 100%;
      background-color: #36404A;
      border: none;
      border-radius: 5px;
      color: #fff;
      font-size: 20px;
      font-weight: 300;
      outline: none;
      resize: none;
    }

    .close {
      position: absolute;
      color: #fff;
      top: -20px;
      right: -58px;
      cursor: pointer;
      width: 20px;
      height: 20px;
      background-color: #FF4D4D;
      padding: 6px;
      border-radius: 100%;
      border: 3px solid #fff;
      transition: all .3s;

      &:before, &:after {
        position: absolute;
        content: '';
        width: 3px;
        height: 20px;
        background-color: #fff;
        top: calc(50% - 10px);
        right: calc(50% - 2px);
      }

      &:before {
        transform: rotate(-45deg);
      }

      &:after {
        transform: rotate(45deg);
      }

      &:hover {
        border-color: #FF4D4D;
        transition: all .3s;
      }

      &:hover:after, &:hover:before {
        background-color: #fff;
        transition: all .3s;
      }
    }
  }

  .dark {
    display: block;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    position: absolute;
    top: 0;
    left: 0;
    z-index: 5;
  }

  /* Large Devices, Wide Screens */
  @media only screen and (max-width: 1200px) {
    .main {
      textarea {
        padding: 10px 20px;
        height: 100%;
        width: 100%;
        font-size: 18px;
        font-weight: 300;
      }
    }
  }

  /* Medium Devices, Desktops */
  @media only screen and (max-width: 992px) {
    /* */
  }

  /* Small Devices, Tablets */
  @media only screen and (max-width: 768px) {
    .main {
      background-color: rgba(0, 0, 0, 0.7);
      z-index: 10;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      width: 70%;
      height: 70%;
      border-radius: 5px;

      textarea {
        padding: 10px 20px;
        height: 100%;
        width: 100%;
        font-size: 18px;
        font-weight: 300;
      }
    }
  }

  /* Extra Small Devices, Phones */
  @media only screen and (max-width: 480px) {
    .main {
      background-color: rgba(0, 0, 0, 0.7);
      z-index: 10;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      width: 60%;
      height: 60%;
      border-radius: 5px;

      textarea {
        padding: 10px 20px;
        height: 100%;
        width: 100%;
        font-size: 16px;
        font-weight: 300;
      }
    }
  }

  /* Custom, iPhone Retina */
  @media only screen and (max-width: 320px) {
    /* */
  }
</style>