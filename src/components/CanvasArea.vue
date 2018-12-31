<template>
  <div>
    <div id="page-buttons">
      <button v-for="i in number_of_pages" :key="i" @click="selectPage(i-1)">Page {{i}}</button>
    </div>
    <CanvasObject
      v-for="(item,index) in canvas_objects"
      :key="item"
      :active="canvas_activity[index]"
      :object_id="item"
      :canvas_height="canvas_height"
      :canvas_width="canvas_width"
      ref="canvasObjects"
    ></CanvasObject>
  </div>
</template>

<script>
import Vue from "vue";
import { getUrlVars } from "../common";
import { fabric } from "fabric";
import CanvasObject from "../components/CanvasObject.vue";

export default {
  name: "CanvasArea",
  components: {
    CanvasObject
  },
  data: function() {
    return {
      canvas_width: Number,
      canvas_height: Number,
      control: String,
      number_of_pages: 1,
      canvas_objects: [],
      canvas_activity: [],
      active_canvas_id: 0
    };
  },
  created: function() {
    this.canvas_width = parseInt(getUrlVars()["width"]);
    this.canvas_height = parseInt(getUrlVars()["height"]);
    this.number_of_pages = parseInt(getUrlVars()["pages"]);
    for (var i = 0; i < this.number_of_pages; i++) {
      this.canvas_activity[i] = 0;
      this.canvas_objects[i] = "c" + i;
    }
    this.canvas_activity[0] = 1;
  },
  mounted: function() {},
  methods: {
    selectPage: function(i) {
      for (var k = 0; k < this.number_of_pages; k++) {
        Vue.set(this.canvas_activity, k, 0);
      }
      Vue.set(this.canvas_activity, i, 1);
      this.active_canvas_id = i;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
#page-buttons {
  margin-left: 90px;
  margin-bottom: 10px;
}
.canvas-container {
  border: 1px solid #555;
  float: left;
}
#shape-buttons {
  float: left;
  border: 1px solid #ccc;
  padding: 5px;
  margin: 5px;
}
#shape-buttons button {
  display: block;
  margin-bottom: 10px;
  width: 100%;
}
#object-controls {
  border: 1px solid #ccc;
  padding: 5px;
  margin: 5px;
  float: left;
}
#object-controls div div{
  border-bottom: 1px solid #ccc;
  display: block;
  margin: 5px 0;
  padding: 10px 10px;
}
#object-controls label{
  margin-right: 5px;
}
</style>
