<template>
  <div v-show="active" :class="canvasClass" @mouseup="objectSelected()">
    <canvas :id="'drawing'+object_id"></canvas>
  </div>
</template>

<script>
import { fabric } from 'fabric';

export default {
  name: 'CanvasObject',
  props: {
    object_id: String,
    canvas_width: Number,
    canvas_height: Number,
    active: Number
  },
  data: function () {
    return{
      canvas: Object
    }
  },
  created: function () {
  },
  mounted: function () {
      this.canvas = new fabric.Canvas('drawing'+this.object_id, {
        width: this.canvas_width,
        height: this.canvas_height,
        backgroundColor: '#fff'
      });
  },
  methods: {
    objectSelected: function(i) {
        this.$emit('object_selected', this.canvas.getActiveObject());
    }
  },
  computed: {
    canvasClass: function () {
      switch(this.object_id) {
        case "c0":
          return "canvas_blue"
          break;
        case "c1":
          return "canvas_red"
          break;
        case "c2":
          return "canvas_green"
          break;
        default:
          return "canvas_black"
      }
      return
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
</style>
