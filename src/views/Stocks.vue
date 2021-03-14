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
    // this.showStock();
    this.apiStocks();
  },
  methods: {
    showStock: function() {
      axios.get("/api/stocks/").then(response => {
        this.stock = response.data;
        console.log("Realtime stock", this.stock);
      });
    },
    apiStocks: function() {
        axios.get("/api/stocks/1").then(response => {
        this.stock = response.data;
        console.log("Realtime stock", this.apiStocks);
      });
    },
  },
};
</script>
