<template>
  <div>
    <canvas v-for="i in product_array" :key="i" :id="'drawing'+i">
    </canvas>
  </div>
</template>

<script>
import { fabric } from 'fabric';
import { getUrlVars } from '../common';
import CanvasObject from '../components/CanvasObject.vue';

export default {
  name: 'CanvasArea',
  data: function () {
    return{
      canvas_width: 500,
      canvas_height: 500,
      number_of_pages: 1,
      product_array: [],
      product_pages: []
    }
  },
  created: function () {
    this.canvas_width = getUrlVars()["width"];
    this.canvas_height = getUrlVars()["height"];
    this.number_of_pages = getUrlVars()["pages"];
    for (var i = 0; i< this.number_of_pages; i++){
      this.product_array[i] = i;
    }
  },

  mounted: function () {
    for (var i=0; i<this.number_of_pages; i++){
      this.product_pages[i] = new fabric.Canvas('drawing'+i, {
        width: this.canvas_width,
        height: this.canvas_height,
        backgroundColor: '#fff'
      });
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
  .canvas-container{
    border: 1px solid #555;
  }
</style>
