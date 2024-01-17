<template>
  <div class="logout">
  </div>
</template>

<script>
import axios from "axios";

export default {
  created: function() {
    console.log("Logging Out..");
    delete axios.defaults.headers.common["Authorization"];
    localStorage.removeItem("jwt");
    this.$router.push("/");
    console.log("Logged out and redirected.");
  },
};
</script>
