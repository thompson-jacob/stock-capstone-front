<template>
  <div class="dashboard">
    <h1>{{ stock.ticker }}</h1>
    <trading-vue
      :data="chart"
      :width="this.width"
      :height="this.height"
      :toolbar="true"
      :color-back="colors.colorBack"
      :color-grid="colors.colorGrid"
      :color-text="colors.colorText"
    ></trading-vue>
  </div>
</template>

<style></style>

<script>
import TradingVue from "trading-vue-js";

export default {
  name: "dashboard",
  components: { TradingVue },
  data() {
    return {
      ohclv,
    };
  },
};
</script>
