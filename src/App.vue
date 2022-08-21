<template>
  <div id="app">
    <Particles/>
  </div>
</template>

<script>
import Particles from "./components/Particles";

export default {
  name: "App",
  components: {
    Particles
  }
};
</script>

<style>
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

body {
  font-family: Monospace, "Avenir", Helvetica, Arial, sans-serif;
  background-color: #000;
  margin: 0px;
  padding: 0;
  overflow: hidden;
}

.press {
  top: 2px;
  left: 10px;
  position: absolute;
  text-align: center;
  color: #fff;
  font-size: 24pt;
}
</style>
