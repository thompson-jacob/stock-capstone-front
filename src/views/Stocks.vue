<template>
  <div>
    <hr />
    <marquee-text
      @mouseenter="paused = !paused"
      @mouseleave="paused = !paused"
      :paused="paused"
      :duration="70"
      class="stock-marquee"
    >
      <span>Most Active Today</span>
      <span v-for="active in actives" v-bind:key="active.ticker">
        <span @click="apiStocks((ticker = active.ticker))" class="badge badge-primary pointer nav-router-link">
          {{ active.ticker }}
        </span>
        <span>{{ active.changesPercentage }}</span>
      </span>
    </marquee-text>

    <marquee-text>
      <span :reverse="true" :duration="99" v-for="sector in sectors" v-bind:key="sector.sector">
        <span class="badge badge-primary">{{ sector.sector }}</span>
        <span>{{ sector.changesPercentage }}</span>
      </span>
    </marquee-text>
    <hr />
    <!-- 3 column row under marquee -->
    <div class="row">
      <!-- scroll bar for news ticker -->
      <aside class="overflow-auto p-3 mb-3 mb-md-0  mr-md-3 bg-dark col-3" style="max-width: 300px; height: 700px;">
        <h4>Stock News</h4>
        <!-- <button v-on:click="this.stock_news">News</button> -->
        <section class="news-div">
          <div v-for="story in storys" v-bind:key="story.ticker">
            <img v-bind:src="`${story.image}`" />
            <p>News For {{ story.symbol }}</p>
            <p>Published by {{ story.site }}</p>
            <p>{{ story.publishedDate }}</p>
            <p>{{ story.title }}</p>
            <p>{{ story.text }}</p>

            <a :href="`${story.url}`" target="_blank">Full Story Here</a>
          </div>
        </section>
      </aside>
      <!-- searchBar and stockChart -->
      <div class="col-6">
        <div>
          <p>type to search a stock for its data</p>
          <!-- <input type="search" v-model="ticker" placeholder="exact ticker" />
      <button v-on:click="this.apiStocks">search</button>  -->
        </div>
        <div>
          <input type="search real" v-model="searchbar" placeholder="search " />
          <button v-on:click="this.searchBar">search</button>

          <!-- TODO refresh watchlist on change -->
          <!-- <select
            v-if="searches.length > 0"
            v-model="selectedSearch"
            v-on:change="apiStocks((ticker = selectedSearch.symbol))"
          >
            <option disabled value="">Please select one</option>
            <option v-for="search in searches" v-bind:key="search.symbol" v-bind:value="search">
              {{ search.name }}
            </option>
          </select> -->

          <div v-for="search in searches" v-bind:key="search.symbol" v-on:click="apiStocks((ticker = search.symbol))">
            <p class="search-return">
              <span>{{ search.name }}</span>
              <span class="stock-ticker-symbol">{{ search.symbol }}</span>
            </p>
          </div>
          <!-- <button v-on:click="this.searchBar">search</button> -->
        </div>

        <div v-if="stock.companyName">
          <input @change="toggleFavorite()" type="checkbox" id="checkbox" v-model="stock.favorited" />
          <label for="checkbox">Favorited</label>
          <h1>{{ stock.companyName }} / {{ stock.ticker }}</h1>
          <img class="mb-3" v-bind:src="`${stock.image}`" />
        </div>
        <div v-if="options && series">
          <div>
            <button v-on:click="stock_chart(`1min`)">
              1min
            </button>
            <button v-on:click="stock_chart(`5min`)">
              5min
            </button>
            <button v-on:click="stock_chart(`15min`)">
              15min
            </button>
            <button v-on:click="stock_chart(`30min`)">
              30min
            </button>
            <button v-on:click="stock_chart(`1hour`)">
              1 hour
            </button>
            <button v-on:click="stock_chart(`4hour`)">
              4 hr
            </button>
          </div>
          <apexchart width="100%" type="line" :options="options" :series="series"></apexchart>
        </div>
        <div v-if="options2 && series2">
          <apexchart type="candlestick" width="100%" :options="options2" :series="series2"></apexchart>
        </div>
        <!-- <div v-if="stock.companyName">
          <input @change="toggleFavorite()" type="checkbox" id="checkbox" v-model="stock.favorited" />
          <label for="checkbox">Favorited</label>
          <h1>{{ stock.companyName }} / {{ stock.ticker }}</h1>
          <img v-bind:src="`${stock.image}`" /> -->
        <div v-if="stock.companyName">
          <p>Company Profile</p>
          <p>Exchange: {{ stock.exchangeShortName }}</p>
          <p>Sector: {{ stock.sector }}</p>
          <p>Last Price: {{ stock.price }}</p>
          <p>Average Daily Volume: {{ stock.volAvg }}</p>
          <p>IPO Date: {{ stock.ipoDate }}</p>
          <p>Country: {{ stock.country }}</p>
          <p>{{ stock.description }}</p>
        </div>
        <!-- <input @change="toggleFavorite()" type="checkbox" id="checkbox" v-model="stock.favorited" />
        <label for="checkbox">Favorited</label> -->
      </div>
      <!-- watchlist and technicals -->
      <div class="overflow-auto p-3 mb-3 mb-md-0 mr-md-3 ml-0 bg-dark col-3">
        <h4>Watchlist</h4>
        <div v-for="favorite in favorites" v-bind:key="favorite.id">
          <p v-on:click="apiStocks((ticker = favorite.stock.ticker))" class="nav-router-link ">
            {{ favorite.stock.ticker }}
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
.nav-router-link {
  cursor: pointer;
}
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

/* .chart {
  font-family: "Orbitron", sans-serif;
} */
</style>

<script>
import axios from "axios";
import VueApexCharts from "vue-apexcharts";
import MarqueeText from "vue-marquee-text-component";
import Vue from "vue";
Vue.component("apexchart", VueApexCharts);
Vue.component(
  "marquee-text",
  // {
  //   props: { type: Number, default: 3 },
  // },
  MarqueeText
);
Vue.component("marquee-text", MarqueeText);
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
      actives: [],
      //candlechart
      options2: null,
      series2: null,
      selectedSearch: {},
      sectors: {},
      paused: false,
    };
  },
  created: function() {
    this.userFavorites();
    this.mostActiveStocks();
    this.sectorChanges();
    this.stock_news();
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
        this.stock_news(ticker);
        this.stock_chart("5min");
      });
    },
    mostActiveStocks: function() {
      axios.get("/api/most_active").then(response => {
        this.actives = response.data;
      });
    },
    sectorChanges: function() {
      axios.get("api/sector_changes").then(response => {
        var data = response.data.sectorPerformance;
        var data1 = data.slice(1, 9);
        // var data2 = data.slice(6,)
        // var data2 = data.slice(Math.max(data.length - data.length / 2, 0))

        this.sectors = data1;
        console.log("sectors", this.sectors);
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
            this.userFavorites();
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
            this.userFavorites();
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
        console.log("news", this.storys);
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
            categories: data.slice(Math.max(data.length - data.length / 5, 0)).map(x => x.date),
          },
        };
        // console.log(this.options.xaxis.categories);
        // this.options.xaxis.categories.filter(current => current.filter);

        this.series = [
          {
            name: "open",
            data: data.slice(Math.max(data.length - data.length / 5, 0)).map(y => y.open),
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
