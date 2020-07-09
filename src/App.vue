<template>
  <div id="app">
    <div v-if="exporting" class="exported">
      <p>Exported</p>
    </div>
    <div class="container">
    <div v-show="exporting === null">
      <Fabric
        :height="800"
        :width="400"
        @blob="handleImg($event)"
        :textSettings="textSettings"
        :imgUrl="'https://images.unsplash.com/photo-1593940768294-1699bb7f144b?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1234&q=80'"
        @export="handleExport($event)"
        :activeTextBoxColor="'purple'"
      />
    </div>
    </div>
  </div>
</template>

<script>
import Fabric from "./components/Fabric.vue";

export default {
  name: "App",
  components: {
    Fabric
  },
  data() {
    return {
      textSettings: {
        fill: "white",
      },
      exporting: null
    };
  },
  methods: {
    handleImg(blobUrl) {
      console.log(blobUrl)
      // const link = document.createElement("a");
      // link.href = blobUrl;
      // link.download = name;
      // link.click();
    },
    handleExport(event) {
      console.log(event)
      if(event === true) {
        this.exporting = true;
      } else {
        this.exporting = false;
      }
      
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}

canvas {
  /* border: red dashed 3px; */
}
.fabric-container {
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
}
.fabric-btn-container {
  padding: 20px 0;
}
.container {
  /* background: cornflowerblue;
  height: 800px; */
}
.exported {
  text-align: center;
}
</style>
