<template>
  <div id="chart">
    <apexchart type="candlestick" height="350" :options="chartOptions" :series="candleSeries"></apexchart>
  </div>
</template>

<script>
import VueApexCharts from "vue-apexcharts";
import Vue from "vue";
Vue.component("apexchart", VueApexCharts);
export default {
  data: function() {
    return {
      candleSeries: [
        {
          data: [
            {
              x: new Date(1538778600000),
              y: [6629.81, 6650.5, 6623.04, 6633.33],
            },
          ],
        },
      ],
      chartOptions: {
        chart: {
          type: "candlestick",
          height: 350,
        },
        title: {
          text: "CandleStick Chart",
          align: "left",
        },
        xaxis: {
          type: "datetime",
        },
        yaxis: {
          tooldip: {
            enabled: false,
          },
        },
      },
    };

  },
};
</script>
