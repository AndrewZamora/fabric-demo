<template>
  <div id="app">
    <div v-if="exported" class="exported">
      <p>Exported</p>
    </div>
    <div class="container">
      <button @click="addTextbox({fill:'red'})">Add Text</button>
      <button @click="changeImage">Change Image</button>
      <button @click="handleExport">Export Image</button>
      <button @click="updateActiveObject">Update Selected Item</button>
      <button @click="centerActiveObject">Center Textbox</button>
      <div v-show="exported === false">
        <Fabric
          :width="300"
          :imgUrl="image"
          :textboxes="textboxes"
          :activeObject="activeObject"
          :triggerExport="triggerExport"
          @image-blob="handleImg($event)"
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
      exported: false,
      textboxes: [{ fill: "pink" }, { fill: "orange" }],
      image:
        "https://images.unsplash.com/photo-1593940768294-1699bb7f144b?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1234&q=80",
      triggerExport: false,
      imageBlob: null,
      activeObject: null
    };
  },
  methods: {
    handleImg(blobUrl) {
      console.log(blobUrl);
      this.handleExport();
      // const link = document.createElement("a");
      // link.href = blobUrl;
      // link.download = name;
      // link.click();
    },
    handleExport() {
      this.exported = true;
      this.triggerExport = !this.triggerExport;
    },
    addTextbox(newTextbox) {
      this.textboxes = [...this.textboxes, newTextbox];
    },
    changeImage() {
      this.image =
        "https://images.unsplash.com/photo-1594201741863-2bf316674ea3?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=634&q=80";
    },
    updateActiveObject() {
      this.activeObject = {
        settings: { fill: "pink", fontSize: 10 }
      };
    },
    centerActiveObject() {
      this.activeObject = {position: 'center'}
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
