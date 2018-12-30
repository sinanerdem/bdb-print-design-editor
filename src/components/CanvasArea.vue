<template>
  <div>
    <div id="page-buttons">
      <button v-for="i in number_of_pages" :key="i" @click="selectPage(i-1)">Page {{i}}</button>
    </div>
    <div id="shape-buttons">
      <button class="shape-btn rectangle" @click="addRect()">Rectangle</button>
      <button class="shape-btn circle" @click="addCircle()">Circle</button>
      <button class="shape-btn triangle" @click="addTriangle()">Triangle</button>
      <button class="shape-btn text" @click="addText()">Text</button>
    </div>
    <CanvasObject
      v-for="(item,index) in canvas_objects"
      :key="item"
      :active="canvas_activity[index]"
      :object_id="item"
      :canvas_height="canvas_height"
      :canvas_width="canvas_width"
      ref="canvasObjects"
      @object_selected="objectSelected"
    ></CanvasObject>
    <div id="object-controls">
      <div id="shape-controls" v-show="control == 'shape'" ref="shapeControls">
        <div>
          <label for="fill-color">Fill color</label>
          <input
            type="color"
            class="shape-ctrl"
            id="fill-color"
            @input="fillColor($event.target.value)"
          >
        </div>
        <div>
          <label for="border-color">Border color</label>
          <input
            type="color"
            class="shape-ctrl"
            id="border-color"
            @input="borderColor($event.target.value)"
          >
        </div>
        <div>
          <label for="border-size">Border size</label>
          <select class="text-ctrl" id="border-size" @input="borderSize($event.target.value)">
            <option v-for="item in 20" :key="'border'+item">{{item}}</option>
          </select>
        </div>
      </div>
      <div id="text-controls" v-show="control == 'text'" ref="textControls">
        <div>
          <label for="font-size">Font size</label>
          <select class="text-ctrl" id="font-size" @input="setFontSize($event.target.value)">
            <option v-for="item in font_sizes" :key="'font'+item">{{item}}</option>
          </select>
        </div>
        <div>
          <label for="font-family">Font family</label>
          <select class="text-ctrl" id="font-family" @input="setFontFamily($event.target.value)">
            <option v-for="item in font_families" :key="'fontfamily'+item">{{item}}</option>
          </select>
        </div>
        <div>
          <label for="fill-color">Fill color</label>
          <input
            type="color"
            class="shape-ctrl"
            id="fill-color"
            @input="fillColor($event.target.value)"
          >
        </div>
      </div>
    </div>
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
      canvas_width: 500,
      canvas_height: 500,
      control: String,
      number_of_pages: 1,
      canvas_objects: [],
      canvas_activity: [],
      active_canvas_id: 0,
      font_sizes: [
        9,
        10,
        11,
        12,
        13,
        14,
        16,
        18,
        20,
        22,
        24,
        28,
        32,
        36,
        40,
        44,
        48,
        60,
        72,
        80,
        90
      ],
      font_families: [
        "Times New Roman",
        "Arial",
        "Comic Sans",
        "Verdana",
        "Courier New",
        "Trebuchet MS",
        "Book Antiqua",
        "Georgia",
        "Copperplate",
        "Papyrus"
      ]
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
    },
    objectSelected: function(obj) {
      if (!obj){
        this.control = "none";
      } else if (["rect", "circle", "triangle"].includes(obj.type)) {
        this.control = "shape";
        this.$refs.shapeControls.querySelector("#fill-color").value = obj.fill;
        this.$refs.shapeControls.querySelector("#border-color").value =
          obj.stroke;
        this.$refs.shapeControls.querySelector("#border-size").value =
          obj.strokeWidth;
      } else if ("i-text" == obj.type) {
        this.control = "text";
        this.$refs.textControls.querySelector("#font-size").value =
          obj.fontSize;
        this.$refs.textControls.querySelector("#font-family").value =
          obj.fontFamily;
        this.$refs.textControls.querySelector("#fill-color").value = obj.fill;
      }

      this.$refs.textControls;
    },
    addRect: function() {
      var rect = new fabric.Rect({
        fill: "#00ff00",
        left: 20,
        top: 20,
        width: 90,
        height: 60,
        stroke: "#00ff00",
        strokeWidth: 1
      });
      this.$refs.canvasObjects[this.active_canvas_id].canvas.add(rect);
    },
    addCircle: function() {
      var circle = new fabric.Circle({
        fill: "#ff0000",
        radius: 50,
        left: 20,
        top: 20,
        stroke: "#ff0000",
        strokeWidth: 1
      });
      this.$refs.canvasObjects[this.active_canvas_id].canvas.add(circle);
    },
    addTriangle: function() {
      var triangle = new fabric.Triangle({
        fill: "#0000ff",
        left: 20,
        top: 20,
        width: 60,
        height: 60,
        stroke: "#0000ff",
        strokeWidth: 1
      });
      this.$refs.canvasObjects[this.active_canvas_id].canvas.add(triangle);
    },
    addText: function() {
      var text = new fabric.IText("your text here", {
        fill: "#000000",
        fontFamily: "Times New Roman",
        fontSize: "14",
        left: 20,
        top: 20
      });
      this.$refs.canvasObjects[this.active_canvas_id].canvas.add(text);
    },
    fillColor: function(color) {
      var canvas = this.$refs.canvasObjects[this.active_canvas_id].canvas;
      var active_object = canvas.getActiveObject();
      active_object.set("fill", color);
      canvas.renderAll();
    },
    borderColor: function(color) {
      var canvas = this.$refs.canvasObjects[this.active_canvas_id].canvas;
      var active_object = canvas.getActiveObject();
      active_object.set("stroke", color);
      canvas.renderAll();
    },
    borderSize: function(num) {
      var canvas = this.$refs.canvasObjects[this.active_canvas_id].canvas;
      var active_object = canvas.getActiveObject();
      active_object.set("strokeWidth", parseInt(num));
      canvas.renderAll();
    },
    setFontSize: function(size) {
      var canvas = this.$refs.canvasObjects[this.active_canvas_id].canvas;
      var active_object = canvas.getActiveObject();
      active_object.set("fontSize", parseInt(size));
      canvas.renderAll();
    },
    setFontFamily: function(family) {
      var canvas = this.$refs.canvasObjects[this.active_canvas_id].canvas;
      var active_object = canvas.getActiveObject();
      active_object.set("fontFamily", family);
      canvas.renderAll();
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
