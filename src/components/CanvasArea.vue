<template>
  <div>
    <div
      v-if="!(canvas_width && canvas_height && number_of_pages)"
    >URL of this page should be like: "www.example.com/?width=800&amp;height=500&amp;pages=5"</div>
    <div v-if="number_of_pages">
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
      <div id="library">
        <button id="library-btn" class="btn" @click="$modal.show('imageUpload')">Library</button>
        <modal name="imageUpload">
          <div id="existing-images">
            <img
              v-for="(item,index) in images"
              :key="'img'+index"
              :src="item"
              @click="onImageClicked"
            >
          </div>
          <form id="image-upload">
            <input type="file" @change="onFileChanged" ref="imageUpload">
          </form>
        </modal>
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
      canvas_width: 1,
      canvas_height: 1,
      control: String,
      number_of_pages: Number,
      canvas_objects: [],
      canvas_activity: [],
      active_canvas_id: 0,
      images: []
    };
  },
  created: function() {
    this.canvas_width = parseInt(getUrlVars()["width"]);
    this.canvas_height = parseInt(getUrlVars()["height"]);
    this.number_of_pages = parseInt(getUrlVars()["pages"]);
    if (this.number_of_pages) {
      for (var i = 0; i < this.number_of_pages; i++) {
        this.canvas_activity[i] = 0;
        this.canvas_objects[i] = "c" + i;
      }
      this.canvas_activity[0] = 1;
    }
  },
  methods: {
    onFileChanged(event) {
      var input = event.target;
      if (input.files && input.files[0]) {
        var reader = new FileReader();
        reader.onload = e => {
          this.images.push(e.target.result);
        };
        reader.readAsDataURL(input.files[0]);
      }
    },
    onImageClicked: function(event) {
      var img = event.target;
      this.$refs.canvasObjects[this.active_canvas_id].addImage(img);
      this.$modal.hide("imageUpload");
    },
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
#object-controls div div {
  border-bottom: 1px solid #ccc;
  display: block;
  margin: 5px 0;
  padding: 10px 10px;
}
#object-controls label {
  margin-right: 5px;
}
.vue_component__upload--image {
  clear: both;
}
#library {
  clear: both;
}
#existing-images img {
  border: 1px solid #ccc;
  width: 100px;
}
.v--modal-box {
  padding: 15px !important;
}
</style>
