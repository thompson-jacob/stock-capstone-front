<template>
  <div id="app">
    <div id="nav" class="navbar navbar-dark bg-dark font-light">
      <router-link to="/">Home</router-link>
      |
      <router-link to="/about">About</router-link>
      |
      <router-link to="/stocks">Stocks</router-link>
      |
      <router-link to="/signup">Signup</router-link>
      |
      <router-link to="/login">Login</router-link>
      |
      <router-link to="/logout">Logout</router-link>
      |
      <router-link to="/linechart">LineChart</router-link>
    </div>
    <router-view />
  </div>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  /* background: rgb(22, 15, 41) !important; */
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #7999b9;
}

#nav a.router-link-exact-active {
  color: #0dec88;
}
</style>
