<template>
  <div class="#stocks">
    <p>type to search a stock for its data</p>
    <input type="text" v-model="ticker" />
    <button v-on:click="this.apiStocks">view stock</button>
    <h1>{{ stock }}</h1>
    <button v-on:click="this.addToFavorites">Favorite stock</button>
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
      price: this.stock.price,
    };
  },
  created: function() {
    // this.showStock();
    //this.apiStocks();
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
    addToFavorites: function() {
      var params = {
        stock_id: this.id,
        ticker: this.ticker,
        current_price: this.price,
      };
      axios
        .post("/api/stocks", params)
        .then(response => {
          console.log(response.data);
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
      axios
        .post("/api/userstocks", params)
        .then(response => {
          console.log(response.data);
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
  },
};
</script>
