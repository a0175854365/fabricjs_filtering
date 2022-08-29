<template>
  <div>
    <div>
      <div>
        <button @click="getBigger">[ + ]</button> / 
        <button @click="getSmaller">[ - ]</button>
      </div>
      <div>
        <input type="checkbox" @click="addFilter('Invert', $event)" /> Invert
        <input
          type="checkbox"
          @click="addFilter('Grayscale', $event)"
        />Grayscale
        <input
          type="checkbox"
          @click="addFilter('BlackWhite', $event)"
        />BlackWhite
        <input type="checkbox" @click="addFilter('Sepia', $event)" />Sepia
      </div>
      <div>
        <input type="checkbox" id="gammaCheckbox" @click="addGamma($event)" />Gamma
        <label
          >Red:
          <input
            type="range"
            id="gamma-red"
            value="1"
            min="0.2"
            max="2.2"
            step="0.003921"
            @change="changeGamma('Red', $event)"
        /></label>
        <br />
        <label
          >Green:
          <input
            type="range"
            id="gamma-green"
            value="1"
            min="0.2"
            max="2.2"
            step="0.003921"
            @change="changeGamma('Green', $event)"
        /></label>
        <br />
        <label
          >Blue:
          <input
            type="range"
            id="gamma-blue"
            value="1"
            min="0.2"
            max="2.2"
            step="0.003921"
            @change="changeGamma('Blue', $event)"
        /></label>
      </div>
    </div>
    <h2>c2</h2>
    <canvas id="c" width="800" height="600"></canvas>
    <hr />
    <h2>img</h2>
    <!-- <img src="./assets/images/eye.jpg" id="my-image" />
    <img src="./assets/images/px4.jpeg" id="my-image2" /> -->
  </div>
</template>

<script>
import { fabric } from "fabric";

export default {
  name: "App",
  components: {},
  data() {
    return {
      canvas: "",
    };
  },
  mounted() {
    let self = this;
    let canvas = new fabric.Canvas("c");

    fabric.Image.fromURL(
      "http://localhost:8080/img/px4.eb58b516.jpeg",
      function (oImg) {
        oImg = oImg.set({ left: 0, top: 0}).scale(0.5);
        canvas.add(oImg);
        self.canvas = canvas;
      }
    );
  },
  methods: {
    getBigger() {
      var obj = this.canvas.getActiveObject();
      console.log(obj);
      console.log(obj.width);
      let newScale = obj.scaleX * 1.01;
      let left = obj.left;
      let center = obj.width/2 * obj.scaleX;
      console.log(left, center);
      obj.set({ center: obj.center + center, top: obj.top}).scale(newScale);
      this.canvas.renderAll();
    },
    getSmaller () {
      console.log("getSmaller");
      var obj = this.canvas.getActiveObject();
      console.log(obj.scaleX);
      let newScale = obj.scaleX * 0.99;
      obj.set({ left: obj.left, top: obj.top}).scale(newScale);
      this.canvas.renderAll();
    },
    addFilter(filterName, event) {
      switch (filterName) {
        case "Invert":
          this.applyFilter(
            1,
            event.target.checked && new fabric.Image.filters.Invert()
          );
          break;
        case "Grayscale":
          this.applyFilter(
            0,
            event.target.checked && new fabric.Image.filters.Grayscale()
          );
          break;
        case "BlackWhite":
          this.applyFilter(
            19,
            event.target.checked && new fabric.Image.filters.BlackWhite()
          );
          break;
        case "Sepia":
          this.applyFilter(
            3,
            event.target.checked && new fabric.Image.filters.Sepia()
          );
          break;
      }
    },
    addGamma(event) {
      var v1 = parseFloat(document.getElementById("gamma-red").value);
      var v2 = parseFloat(document.getElementById("gamma-green").value);
      var v3 = parseFloat(document.getElementById("gamma-blue").value);
      this.applyFilter(
        17,
        event.target.checked &&
          new fabric.Image.filters.Gamma({
            gamma: [v1, v2, v3],
          })
      );
    },
    changeGamma(color, event) {
      var v1 = parseFloat(document.getElementById("gamma-red").value);
      var v2 = parseFloat(document.getElementById("gamma-green").value);
      var v3 = parseFloat(document.getElementById("gamma-blue").value);
      switch (color) {
        case "Red":
          v1 = event.target.value;
          break;
        case "Green":
          v2 = event.target.value;
          break;
        case "Blue":
          v3 = event.target.value;
          break;
      }
      let gammeChecked = document.getElementById("gammaCheckbox").checked;
      this.applyFilter(
        17,
        gammeChecked &&
          new fabric.Image.filters.Gamma({
            gamma: [v1, v2, v3],
          })
      );
    },
    applyFilter(index, filter) {
      var obj = this.canvas.getActiveObject();
      obj.filters[index] = filter;
      obj.applyFilters();
      this.canvas.renderAll();
    },
  },
};
</script>

<style>
canvas {
  border: solid 1px gray;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
