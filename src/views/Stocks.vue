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
      <button v-on:click="this.movementStocks">News</button>
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
  </div>
</template>

<style></style>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      stock: "",
      apiStock: "",
      ticker: "",
      storys: [],
    };
  },
  created: function() {
    // this.showStock();
    //this.apiStocks();
    // this.movementStocks();
  },
  methods: {
    apiStocks: function() {
      axios.get("/api/stock_search?ticker=" + this.ticker).then(response => {
        this.stock = response.data;

        console.log(response.data);

        // console.log(new Date());

        console.log("Realtime stock", this.apiStocks);
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
    movementStocks: function() {
      axios.get("api/movement_stocks?ticker=" + this.ticker).then(response => {
        this.storys = response.data;
        console.log(response.data);
      });
    },
  },
};
</script>
