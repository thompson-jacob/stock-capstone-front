<script>
import { Candle } from "vue-chartjs";
export default {
  extends: Candle,
  props: {
    chartData: {
      type: Array,
      required: false,
    },
    chartLabels: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      options: {
        scales: {
          yAxes: [
            {
              ticks: {
                beginAtZero: false,
              },
              gridLines: {
                display: true,
              },
            },
          ],
          xAxes: [
            {
              gridLines: {
                display: false,
              },
            },
          ],
        },
        legend: {
          display: false,
        },
        responsive: true,
        maintainAspectRatio: false,
      },
    };
  },
  mounted() {
    this.renderChart(
      {
        labels: this.chartLabels,
        datasets: [
          {
            label: "prices",
            borderColor: "#249EBF",
            pointBackgroundColor: "white",
            borderWidth: 1,
            pointBorderColor: "#249EBF",
            backgroundColor: "transparent",
            data: this.chartData,
          },
        ],
      },
      this.options
    );
  },
};
</script>
