<template>
  <div id="app">
    <TaskBoard />
  </div>
</template>

<script>
import TaskBoard from "./components/TaskBoard.vue";

export default {
  components: {
    TaskBoard,
  },
};
</script>

<style>
body {
  background-color: #fff;
  font-family: Roboto, "Helvetica Neue", sans-serif;
  margin: 0;
  padding: 0;
}
h1, h2, h3, button {
  margin: 0;
  padding: 0;
}
</style>
