<template>
  <div class="#stocks">
    <div class="row">
      <div class="overflow-auto p-3 mb-3 mb-md-0 mr-md-3 bg-dark col-2">
        <h4>Watchlist</h4>
        <div v-for="favorite in favorites" v-bind:key="favorite.id">
          <p v-on:click="apiStocks((ticker = favorite.stock.ticker))">{{ favorite.stock.ticker }}</p>
        </div>
      </div>
      <div class="col-7">
        <div>
          <p>type to search a stock for its data</p>
          <!-- <input type="search" v-model="ticker" placeholder="exact ticker" />
      <button v-on:click="this.apiStocks">search</button>  -->
        </div>
        <div>
          <input type="search real" v-model="searchbar" placeholder="search " />
          <!-- TODO refresh watchlist on change -->
          <select
            v-if="searches.length > 0"
            v-model="selectedSearch"
            v-on:change="apiStocks((ticker = selectedSearch.symbol))"
          >
            <option disabled value="">Please select one</option>
            <option v-for="search in searches" v-bind:key="search.symbol" v-bind:value="search">
              {{ search.name }}
            </option>
          </select>
          <div v-for="search in searches" v-bind:key="search.symbol" v-on:click="apiStocks((ticker = search.symbol))">
            <p class="search-return">
              <span>{{ search.name }}</span>
              <span class="stock-ticker-symbol">{{ search.symbol }}</span>
            </p>
          </div>
        </div>
        <button v-on:click="this.searchBar">search</button>
        <div v-if="stock.companyName">
          <h1>{{ stock.companyName }} / {{ stock.ticker }}</h1>
          <img v-bind:src="`${stock.image}`" />
          <p>Company Profile</p>
          <p>Exchange: {{ stock.exchangeShortName }}</p>
          <p>Sector: {{ stock.sector }}</p>
          <p>Last Price: {{ stock.price }}</p>
          <p>Average Daily Volume: {{ stock.volAvg }}</p>
          <p>IPO Date: {{ stock.ipoDate }}</p>
          <p>Country: {{ stock.country }}</p>
          <p>{{ stock.description }}</p>
        </div>
        <input @change="toggleFavorite()" type="checkbox" id="checkbox" v-model="stock.favorited" />
        <label for="checkbox">Favorited</label>
        <div v-if="options && series">
          <div>
            <button value="1min" v-on:click="stock_chart(`1min`)">
              1min
            </button>
            <button value="5min" v-on:click="stock_chart(`5min`)">
              5min
            </button>
            <button value="15min" v-on:click="stock_chart(`15min`)">
              15min
            </button>
            <button value="30min" v-on:click="stock_chart(`30min`)">
              30min
            </button>
            <button value="1hour" v-on:click="stock_chart(`1hour`)">
              1 hour
            </button>
            <button value="4hour" v-on:click="stock_chart(`4hour`)">
              4 hr
            </button>
          </div>
          <apexchart width="100%" type="line" :options="options" :series="series"></apexchart>
        </div>
        <div v-if="options2 && series2">
          <apexchart type="candlestick" width="100%" :options="options2" :series="series2"></apexchart>
        </div>
      </div>
      <aside class="overflow-auto p-3 mb-3 mb-md-0 mr-md-3 bg-dark col-12" style="max-width: 300px; max-height: 200px;">
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
  height: 400px;
  color: rgb(81, 233, 43);
}
.favorites {
  width: 200px;
  position: relative;
}
h4 {
  color: rgb(81, 233, 43);
}
/* .chart {
  font-family: "Orbitron", sans-serif;
} */
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
      selectedSearch: {},
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
        this.stock_chart("30min");
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
    stock_chart: function(time) {
      console.log("charttime:", time);
      axios.get("api/stock_chart?chart_time=" + time + "&ticker=" + this.ticker).then(response => {
        var data = response.data;

        data = data.reverse();
        console.log("data", data);

        this.options = {
          chart: {
            id: "vuechart-example",
          },
          xaxis: {
            categories: data.slice(Math.max(data.length - data.length / 20, 0)).map(x => x.date),
          },
        };
        // console.log(this.options.xaxis.categories);
        // this.options.xaxis.categories.filter(current => current.filter);

        this.series = [
          {
            name: "open",
            data: data.slice(Math.max(data.length - data.length / 20, 0)).map(y => y.open),
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
            font: "orbitron, sanserif",
          },
        };
        this.series2 = [
          {
            data: data
              .slice(Math.max(data.length - data.length / 20, 0))
              .map(item => ({ x: item.date, y: [item.open, item.high, item.low, item.close] })),
          },
        ];
      });
    },
  },
};
</script>
