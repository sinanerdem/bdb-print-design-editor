<template>
  <canvas id="drawing">
  </canvas>
</template>

<script>
import { fabric } from 'fabric'
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  mounted: function () {
    var canvas = new fabric.Canvas('drawing', {
      width: 300,
      height: 300,
      isDrawingMode: false,
      backgroundColor: '#fff'
    });
    canvas.add(new fabric.Rect({left: 0, top: 0, fill: '#000', width: 100, height: 100}));
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
