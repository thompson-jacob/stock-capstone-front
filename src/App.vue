<template>
  <div id="app">
    <div id="nav" class="navbar navbar-light bg-dark font-light">
      <!-- <router-link class="nav-router-link" to="/">Home</router-link> -->
      <!-- | -->
      <!-- <router-link class="nav-router-link" to="/about">About</router-link>
      | -->
      <h3 class="logo">DiamondHands</h3>

      <router-link class="nav-router-link" to="/stocks">Stocks</router-link>
      |
      <router-link class="nav-router-link" to="/signup">Signup</router-link>
      |
      <router-link class="nav-router-link" to="/login">Login</router-link>
      |
      <router-link class="nav-router-link" to="/logout">Logout</router-link>
      <!-- |
      <router-link class="nav-router-link" to="/linechart">LineChart</router-link> -->
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
  color: #3a86d3;
  font-family: "Orbitron", sans-serif;
  letter-spacing: 0.5px;
}

#nav {
  padding: 30px;
  color: #f0f4f8;
}

.nav-router-link {
  text-decoration: none;
  transition-timing-function: 0.5s ease-in-out;
}

.nav-router-link:hover {
  transform: scale(1.1);
}

#nav a {
  font-weight: bold;
  /* color: #0dec88; */
  color: rgb(81, 233, 43);
}

#nav a.router-link-exact-active {
  color: #0dec88;
  text-decoration-line: underline;
}
#orbitron {
  font-family: "Orbitron", sans-serif;
}

#pointer {
  cursor: pointer;
}
#logo {
  color: #666b69;
}
</style>
