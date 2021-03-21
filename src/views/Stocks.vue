<template>
  <div class="#stocks">
    <div>
      <p>type to search a stock for its data</p>
      <!-- <input type="search" v-model="ticker" placeholder="exact ticker" />
      <button v-on:click="this.apiStocks">search</button>  -->
    </div>
    <div class="overflow-auto p-3 mb-3 mb-md-0 mr-md-3 bg-dark, favorites">
      <h4>Watchlist</h4>
      <div v-for="favorite in favorites" v-bind:key="favorite.id">
        <p v-on:click="apiStocks((ticker = favorite.stock.ticker))">{{ favorite.stock.ticker }}</p>
      </div>
    </div>
    <div>
      <input type="search real" v-model="searchbar" placeholder="search " />
      <!-- TODO refresh watchlist on change -->
      <div v-for="search in searches" v-bind:key="search.symbol" v-on:click="apiStocks((ticker = search.symbol))">
        <p class="search-return">
          <span>{{ search.name }}</span>
          <span class="stock-ticker-symbol">{{ search.symbol }}</span>
        </p>
      </div>
    </div>
    <button v-on:click="this.searchBar">search</button>
    <h1>{{ stock }}</h1>
    <input @change="toggleFavorite()" type="checkbox" id="checkbox" v-model="stock.favorited" />
    <label for="checkbox">Favorited</label>
    <aside class="overflow-auto p-3 mb-3 mb-md-0 mr-md-3 bg-dark" style="max-width: 300px; max-height: 200px;">
      <h4>Stock News</h4>
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
    <!-- <button v-on:click="this.stock_chart">Charts</button> -->
    <div v-if="options && series">
      <apexchart width="500" type="line" :options="options" :series="series"></apexchart>
    </div>
    <div v-if="options2 && series2">
      <apexchart type="candlestick" height="350" :options="options2" :series="series2"></apexchart>
    </div>
  </div>
</template>

<style>
.news-div {
  overflow-x: hidden;
  /* flex: 300px; */
}
.news-div img {
  width: 100%;
  height: auto;
  color: rgb(81, 233, 43);
}
.favorites {
  width: 200px;
  position: relative;
}
h4 {
  color: rgb(81, 233, 43);
}
</style>

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
      searches: [],
      searchbar: "",
      favorites: [],
      //candlechart
      options2: null,
      series2: null,
      chart_time: "5min",
    };
  },
  created: function() {
    // this.showStock();
    //this.apiStocks();
    // this.stock_news();
    this.userFavorites();
  },
  methods: {
    // change passed in?
    userFavorites: function() {
      axios.get("/api/userstocks").then(response => {
        this.favorites = response.data;
        console.log(this.favorites);
      });
    },
    apiStocks: function(ticker) {
      axios.get("/api/stock_search?ticker=" + ticker).then(response => {
        this.stock = response.data;

        console.log(response.data);
        console.log("Realtime stock", this.apiStocks);
        this.searches = [];
        // var chart = this.chart_time;
        this.stock_chart();
      });
    },
    searchBar: function() {
      axios.get("/api/search_bar?searchbar=" + this.searchbar).then(response => {
        this.searches = response.data;
        console.log(this.searches);
        this.searchbar = "";
      });
    },
    toggleFavorite: function() {
      console.log(this.stock.favorited);
      if (this.stock.favorited) {
        console.log("Add Favorite");
        var params = {
          ticker: this.ticker,
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
      axios.get("api/stock_chart?chart_time=" + this.chart_time + "&ticker=" + this.ticker).then(response => {
        var data = response.data;

        data = data.reverse();

        this.options = {
          chart: {
            id: "vuechart-example",
          },
          xaxis: {
            categories: data.map(x => x.date),
          },
        };
        // console.log(this.options.xaxis.categories);
        // this.options.xaxis.categories.filter(current => current.filter);

        this.series = [
          {
            name: "open",
            data: data.map(y => y.open),
          },
          // {
          //   name: "high",
          //   data: data.map(y => y.high),
          // },
          // {
          //   name: "low",
          //   data: data.map(y => y.low),
          // },
          // {
          //   name: "close",
          //   data: data.map(y => y.close),
          // },
        ];
        this.options2 = {
          chart: {
            id: "candlestick-example",
            type: "candle",
          },
        };
        this.series2 = [
          {
            data: data
              .slice(Math.max(data.length - 5, 0))
              .map(item => ({ x: item.date, y: [item.open, item.high, item.low, item.close] })),
          },
        ];
      });
    },
  },
};
</script>
