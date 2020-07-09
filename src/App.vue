<template>
  <div id="app">
    <Fabric
      :height="800"
      :width="400"
      @img-modified="handleImg($event)"
      :imgUrl="'https://images.unsplash.com/photo-1587613552674-134b268886b3?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1301&q=80'"
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
