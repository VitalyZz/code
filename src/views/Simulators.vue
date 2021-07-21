<template>
  <div class="simulators">
    <SimulatorBlock
      v-for="title of titles"
      :key="title.id"
      :element="title"
    />
  </div>
</template>

<script>
import SimulatorBlock from "@/components/simulators/SimulatorBlock";

export default {
  name: 'simulators',
  data() {
    return {
      titles: this.$store.getters['simulators/getTitlesOfSimulators'],
    }
  },
  components: {
    SimulatorBlock
  }
}
</script>

<style scoped lang="scss">
  .simulators {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    margin-top: 30px;
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
    //.simulators {
    //  display: flex;
    //  flex-wrap: wrap;
    //  justify-content: center;
    //}
  }

  /* Extra Small Devices, Phones */
  @media only screen and (max-width: 480px) {
    .simulators {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
    }
  }

  /* Custom, iPhone Retina */
  @media only screen and (max-width: 320px) {
    /* */
  }
</style>