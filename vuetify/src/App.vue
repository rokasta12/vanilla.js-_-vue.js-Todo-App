<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
        <v-row justify="center">
          <v-date-picker
            v-model="date"
            :allowed-dates="allowedDates"
            class="mt-4"
            min="2016-06-15"
            max="2018-03-20"
          ></v-date-picker>
        </v-row>
    </div>
    <router-view/>
  </div>
</template>

<script>
export default {
  data: () => ({
    date: '2021-09-27'
  }),

  methods: {
    allowedDates: (val) => {
      return val <= new Date().toISOString().substr(0, 10)
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}
</style>
