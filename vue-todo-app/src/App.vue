<template>
  <div >
    <router-view />
  </div>
</template>

<style>
body {
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      background-color: rgb(39, 39, 39);
      color: white;
    }
    .container {
      max-width: 1200px;
      margin: 0 auto;
      background-color: white;
      padding: 50px;
      border-radius: 5px;
      min-height: 500px;
    }
    .d-flex {
      display: flex;
      flex-direction: column;
    }
    button,
    input {
      padding: 10px 15px;
    }
    button {
      margin-left: 10px;
    }
    ul li {
      display: flex;
      justify-content: space-between;
      margin-top: 10px;
      color:black
    }
    ul li span.delete {
      padding: 5px;
      background-color: rgb(252, 118, 118);
      border-radius: 5px;
    }
</style>
