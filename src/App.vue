<template>
  <LogList />
</template>

<script setup>
import LogList from "./components/LogList.vue";
</script>

<style lang="scss">
body {
  margin: 0;
}
#app {
  display: flex;
  flex-flow: column;
  min-width: 100vw;
  width: 100vw;
  max-width: 100vw;
  min-height: 100vh;
  height: 100vh;
  max-height: 100vh;
  margin: 0;
  padding: 40px;
  box-sizing: border-box;
  font-family: Inter, system-ui, Avenir, Helvetica, Arial, sans-serif;
  color: #ffffffde;
  background-color: #242424;
}
</style>
