<template>
  <div id="app">
    <Fabric
      :height="600"
      :width="400"
      @img-modified="handleImg($event)"
      :imgUrl="'https://images.unsplash.com/photo-1592564199999-b7c0e57e062e?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=2113&q=80'"
    />
  </div>
</template>

<script>
import Fabric from "./components/Fabric.vue";

export default {
  name: "App",
  components: {
    Fabric
  },
  methods: {
    handleImg(canvas) {
      // Image Comes Out In base64
      console.log(canvas.toDataURL());
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
