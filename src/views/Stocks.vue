<template>
  <div class="#stocks">
    <h1>{{ stock }}</h1>
    <p>this is test data</p>
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
    };
  },
  created: function() {
    this.showStocks();
    // this.apiStocks();
  },
  methods: {
    showStocks: function() {
      axios.get("/api/stocks/").then(response => {
        this.stock = response.data;
        console.log("Realtime stock", this.stock);
      });
    },
    apiStocks: function() {
      axios
        // .get("https://financialmodelingprep.com/api/v3/quote-short/AAPL?apikey=")
        .then(response => {
          this.stock = response.data;
          console.log("Realtime stock", this.apiStocks);
        });
    },
  },
};
</script>
