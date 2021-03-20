<template>
  <div class="#stocks">
    <p>type to search a stock for its data</p>
    <input type="text" v-model="ticker" />
    <button v-on:click="this.apiStocks">view stock</button>
    <h1>{{ stock }}</h1>
    <input @change="toggleFavorite()" type="checkbox" id="checkbox" v-model="stock.favorited" />
    <label for="checkbox">Favorited</label>
    <aside class="news">
      <h2>Stock News</h2>
      <button v-on:click="this.stock_news">News</button>
      <section class="news-div">
        <div v-for="story in storys" v-bind:key="story.ticker">
          <img v-bind:src="`${story.image}`" />
          <p>News For {{ story.symbol }}</p>
          <p>Published by {{ story.site }}</p>
          <p>{{ story.publishedDate }}</p>
          <p>{{ story.title }}</p>
          <p>{{ story.text }}</p>

          <a :href="`${story.url}`">Full Story Here</a>
        </div>
      </section>
    </aside>
    <button v-on:click="this.stock_chart">Charts</button>

    <div v-if="options && series">
      <apexchart width="500" type="line" :options="options" :series="series"></apexchart>
    </div>
    <div v-if="candleOptions && candleSeries" id="chart">
      <apexchart type="candlestick" height="350" :options="candleOptions" :series="candleSeries"></apexchart>
    </div>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
import VueApexCharts from "vue-apexcharts";
import Vue from "vue";
Vue.component("apexchart", VueApexCharts);
export default {
  data: function() {
    return {
      stock: "",
      apiStock: "",
      ticker: "",
      storys: [],
      //linechart
      options: null,
      series: null,
      //candlechart
      candleOptions: this.candleOptions,
      candleSeries: this.candleSeries,
    };
  },
  created: function() {
    // this.showStock();
    //this.apiStocks();
    // this.stock_news();
  },
  methods: {
    apiStocks: function() {
      axios.get("/api/stock_search?ticker=" + this.ticker).then(response => {
        this.stock = response.data;

        console.log(response.data);

        // console.log(new Date());

        console.log("Realtime stock", this.apiStocks);
        this.stock_chart();
      });
    },
    toggleFavorite: function() {
      console.log(this.stock.favorited);
      if (this.stock.favorited) {
        console.log("Add Favorite");
        var params = {
          ticker: this.ticker,
          current_price: this.price,
        };
        axios
          .post("/api/userstocks", params)
          .then(response => {
            console.log(response.data);
          })
          .catch(error => {
            this.errors = error.response.data.errors;
          });
      } else {
        console.log("Delete Favorite");
        // delete favorite here
        params = {
          stock_id: this.stock.id,
        };
        axios
          .delete("/api/userstocks", { data: params })
          .then(response => {
            console.log(response.data);
          })
          .catch(error => {
            this.errors = error.response.data.errors;
          });
      }
    },

    addToFavorites: function() {
      var params = {
        ticker: this.ticker,
        current_price: this.price,
      };
      // axios
      //   .post("/api/stocks", params)
      //   .then(response => {
      //     console.log(response.data);
      //   })
      //   .catch(error => {
      //     this.errors = error.response.data.errors;
      //   });
      axios
        .post("/api/userstocks", params)
        .then(response => {
          console.log(response.data);
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
    stock_news: function() {
      axios.get("api/stock_news?ticker=" + this.ticker).then(response => {
        this.storys = response.data;
        console.log(response.data);
      });
    },
    stock_chart: function() {
      axios.get("api/stock_chart?ticker=" + this.ticker).then(response => {
        var data = response.data;
        console.log(data);
        // this.options = {
        //   chart: {
        //     id: "vuechart-example",
        //   },
        //   xaxis: {
        //     categories: data.map(x => x.date),
        //   },
        // };
        // // console.log(this.options.xaxis.categories);
        // // this.options.xaxis.categories.filter(current => current.select);
        // this.series = [
        //   {
        //     name: "open",
        //     data: data.map(y => y.open),
        //   },
        //   // {
        //   //   name: "high",
        //   //   data: data.map(y => y.high),
        //   // },
        //   // {
        //   //   name: "low",
        //   //   data: data.map(y => y.low),
        //   // },
        //   // {
        //   //   name: "close",
        //   //   data: data.map(y => y.close),
        //   // },
        // ];
        this.chartOptions = {
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
              enabled: true,
            },
          },
        };
        this.candleSeries = [
          {
            // data: [
            //   {
            //     // x: new Date(1538778600000),
            //     // y: [6629.81, 6650.5, 6623.04, 6633.33],
            //     x: data.map(x => new Date(x.date).getTime()),
            //     y: data.map(y => [y.open, y.high, y.low, y.close]),
            //     // y: data.map(y => {
            //     //   [y.open, y.high, y.low, y.close];
            //     //   console.log(y.open, y.high, y.low, y.close);
            //     // }),
            //   },
            // ],
            data: [
              {
                x: new Date(1538778600000),
                y: [6629.81, 6650.5, 6623.04, 6633.33],
                // x: data.map(x => new Date(x.date).getTime()),
                // y: data.map(y => [y.open, y.high, y.low, y.close]),
                // y: data.map(y => {
                //   [y.open, y.high, y.low, y.close];
                //   console.log(y.open, y.high, y.low, y.close);
                // }),
              },
            ],
          },
        ];
      });
    },
  },
};
</script>
