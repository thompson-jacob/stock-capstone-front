<template>
  <area-chart ref="users-chart" id="users-chart" :data="data" :library="library"></area-chart>
</template>

<script>
export default {
  data() {
    return {
      gradient1: null,
      gradient2: null,
      data: [
        {
          name: "GitHub Commits",
          data: {
            Jan: 21,
            Feb: 30,
            Mar: 10,
            Apr: 15,
            May: 29,
            Jun: 18,
            Jul: 28,
            Aug: 0,
            Sep: 70,
            Oct: 38,
            Nov: 22,
            Dec: 39
          }
        },
        {
          name: "GitLab Commits",
          library: {
            // backgroundColor: this.getBG1()
          },
          data: {
            Jan: 10,
            Feb: 30,
            Mar: 40,
            Apr: 42,
            May: 50,
            Jun: 52,
            Jul: 62,
            Aug: 11,
            Sep: 33,
            Oct: 43,
            Nov: 83,
            Dec: 32
          }
        }
      ],
      library: {
        backgroundColor: {
          linearGradient: [0, 0, 99, 99],
          stops: [[0, "rgb(255, 255, 255)"], [99, "rgb(2, 200, 255)"]]
        }
      }
    };
  },
  async mounted() {
    await this.getBG1();
    // var chart_ctx = document
    //   .getElementById("users-chart")
    //   .querySelectorAll("canvas")[0]
    //   .getContext("2d");
    // var gradient1 = chart_ctx.createLinearGradient(0, 0, 0, 200);
    // gradient1.addColorStop(0, "rgba(255, 0,0, 1)");
    // gradient1.addColorStop(0.5, "rgba(255, 0, 0, 0.5)");
    // gradient1.addColorStop(1, "rgba(255, 0, 0, 0)");
    // this.gradient1 = gradient1;
    // var gradient2 = chart_ctx.createLinearGradient(0, 0, 0, 600);
    // gradient2.addColorStop(0, "rgba(255, 177,33, 1)");
    // gradient2.addColorStop(0.5, "rgba(255, 177, 33, 0.5)");
    // gradient2.addColorStop(1, "rgba(255, 177, 33, 0)");
    // this.gradient2 = gradient2;
  },
  methods: {
    getBG1() {
      var chart_ctx = document
        .getElementById("users-chart")
        .querySelectorAll("canvas")[0]
        .getContext("2d");

      debugger;

      var gradient1 = chart_ctx.createLinearGradient(0, 0, 0, 200);
      gradient1.addColorStop(0, "rgba(255, 0,0, 1)");
      gradient1.addColorStop(0.5, "rgba(255, 0, 0, 0.5)");
      gradient1.addColorStop(1, "rgba(255, 0, 0, 0)");
      this.gradient1 = gradient1;
      console.log(gradient1);
      return gradient1;
    }
  }
};
</script>

<style>
</style>
