<template>
  <div
    class="hot-editor-area"
    @mousemove="move"
    @mouseup="up"
    :style="
      'background-image:url(' +
      url +
      ');height:' +
      height +
      'px;width:' +
      width +
      'px;'
    "
  >
    <div class="areas" v-for="(area, index) in areas" :key="index">
      <div
        class="area-box"
        v-if="area.type == 'rect'"
        @mousedown="down(index, 1)"
        @click="openOrCloseAttr"
        :style="
          'left:' +
          area.point[0].x +
          'px; top:' +
          area.point[0].y +
          'px; width:' +
          (area.point[1].x - area.point[0].x) +
          'px; height:' +
          (area.point[1].y - area.point[0].y) +
          'px;'
        "
      >
        <div class="area-input area-input-group" @click="noBubble" @mousedown="noBubble" @mouseup="noBubble">
          <input class="area-input" type="text" v-model="area.url" placeholder="链接/url">
          <input class="area-input" type="text" v-model="area.detail" placeholder="详细说明/alt">
          <input class="area-input" type="text" v-model="area.clazz" placeholder="类/class">
          <select class="area-input" type="text" v-model="area.target" placeholder="打开方式/target">
            <option value="_blank">_blank</option>
            <option value="_self">_self</option>
            <option value="_parent">_parent</option>
            <option value="_top">_top</option>
          </select>
        </div>
        <div class="del-box-btn" @click="delArea(index)"></div>
        <div @mousedown="down(index, 2)" class="right-box-btn"></div>
        <div @mousedown="down(index, 3)" class="bottom-box-btn"></div>
      </div>
      <div
        class="area-box"
        v-if="area.type == 'circle'"
        @mousedown="down(index, 1)"
        @click="openOrCloseAttr"
        :style="
          'border-radius:' +
          area.radius +
          'px;left:' +
          area.point[0].x +
          'px; top:' +
          area.point[0].y +
          'px; width:' +
          area.radius * 2 +
          'px; height:' +
          area.radius * 2 +
          'px;'
        "
      >
        <div class="area-input area-input-group" @click="noBubble" @mousedown="noBubble" @mouseup="noBubble">
          <input class="area-input" type="text" v-model="area.url" placeholder="链接/url">
          <input class="area-input" type="text" v-model="area.detail" placeholder="详细说明/alt">
          <input class="area-input" type="text" v-model="area.clazz" placeholder="类/class">
          <select class="area-input" type="text" v-model="area.target" placeholder="打开方式/target">
            <option value="_blank">_blank</option>
            <option value="_self">_self</option>
            <option value="_parent">_parent</option>
            <option value="_top">_top</option>
          </select>
        </div>
        <div class="del-box-btn" @click="delArea(index)"></div>
        <div @mousedown="down(index, 2)" class="right-box-btn"></div>
      </div>
    </div>
  </div>
</template>

<script>
/**
 * {
 *  name:'',
 *  version:1.0,
 *  image:{
 *     url:http://baidu.com/logo.jpg,
 *     height:50,
 *     width:200,
 *     areas:[{
 *         type: 'rect',
 *         point:[{}]
 *         radius:null,
 *         url: '',
 *         clazz: '',
 *         target:''
 *         detail: ''
 *     }]
 *    }
 * }
 */
const VERSION = 1.0;

export default {
  data() {
    return {
      version: VERSION,
      initCall: null,
      downPoint: { x: 0, y: 0 },
      height: 0,
      width: 0,
      areas: [],
      editMode: 0,
      currentArea: 0,
    };
  },

  name: "HotAreaEditor",

  props: {
    url: { type: String, default: "" },
    shape: { type: String, default: "rect" },
    add: { type: Boolean, default: false },
    json: {
      type: Object,
      default: () => {
        return null;
      },
    },
  },

  mounted() {
    console.log(
      "------------热区编辑器初始化verison:" + this.version + "-------------"
    );
    this.initArea();
  },

  watch: {
    url(oldVal, val) {
      if (oldVal != "" && oldVal != val) {
        this.changeUrl();
        this.areas = []
      }
    },

    shape(val) {
      // && val != "poly"
      if (val != "rect" && val != "circle") {
        throw new Error(
          "请设置合法的形状;the shape：" + val + " is not support."
        );
      }
    },
  },

  methods: {
    openOrCloseAttr(){
      let display = event.target.querySelector(".area-input-group").style.display;
      if(display == 'block'){
        event.target.querySelector(".area-input-group").style.display = 'none';
      }else{
        event.target.querySelector(".area-input-group").style.display = 'block';
      }
      event.stopPropagation()
    },
    noBubble(){
      event.stopPropagation()
    },
    delArea(index) {
      let i = Number(index);
      this.areas.splice(i, 1);
    },
    down(index, type) {
      if (event.button == 0 && event.buttons == 1) {
        this.currentArea = index;
        this.editMode = type;
        this.downPoint.x = event.x;
        this.downPoint.y = event.y;
      }
      event.preventDefault();
      event.stopPropagation();
    },
    changeUrl() {
      let image = new Image();
      image.src = this.url;
      image.onload = () => {
        this.height = image.height;
        this.width = image.width;
      }
    },
    initArea() {
      if (this.json != null) {
        console.log("hot-area-editor init json");
        this.url = this.json.image.url;
        this.height = this.json.image.height;
        this.width = this.json.image.width;
        this.areas = JSON.parse(JSON.stringify(this.json.image.areas));
      } else if (this.url != "") {
        console.log("hot-area-editor init url");
        this.changeUrl();
      }
    },
    addArea() {
      this.areas.push({
        type: this.shape,
        point: [
          { x: 0, y: 0 },
          { x: 50, y: 50 },
        ],
        radius: 25,
        url: "",
        clazz: "",
        detail: "",
        target: "_blank",
      });
    },
    exportCode() {
      let code = Math.random()

      let json = {
        name: "hot-area-" + code,
        version: this.version,
        image: {
          url:this.url,
          height:this.height,
          width:this.width,
          areas:JSON.parse(JSON.stringify(this.areas))
        }
      }
      
      let html = `<img src='`+json.image.url+`' data-name='`+json.name+`' data-version='`+json.version+`' usemap='#map`+code+`'>
      <map name='map`+code+`' id='map`+code+`'>`
      for(let area of json.image.areas){
        let coords = area.type == 'rect' ? area.point[0].x+','+area.point[0].y+','+area.point[1].x+','+area.point[1].y : (area.point[0].x+area.radius)+','+(area.point[0].y+area.radius)+','+area.radius
        html += `<area shape='`+area.type+`' coords='`+coords+`' alt='`+area.detail+`' href='`+area.url+`' target='`+area.target+`' class='`+area.clazz+`'>`
      }   
      html += `</map>`

      return {
        json: json,
        html: html
      }
    },
    move() {
      if (event.button == 0 && event.buttons == 1 && this.editMode != 0) {
        let xT = event.x - this.downPoint.x;
        let yT = event.y - this.downPoint.y;

        let currentAreaEle = this.areas[this.currentArea];

        if (this.editMode == 1) {
          this.areas[this.currentArea].point[0].x += xT;
          this.areas[this.currentArea].point[0].y += yT;
          this.areas[this.currentArea].point[1].x += xT;
          this.areas[this.currentArea].point[1].y += yT;
        } else if (this.editMode == 2) {
          if (currentAreaEle.type == "rect") {
            this.areas[this.currentArea].point[1].x += xT;
          } else if (currentAreaEle.type == "circle") {
            this.areas[this.currentArea].point[0].x -= xT;
            this.areas[this.currentArea].point[0].y -= xT;
            this.areas[this.currentArea].radius += xT;
          }
        } else if (this.editMode == 3) {
          if (currentAreaEle.type == "rect") {
            this.areas[this.currentArea].point[1].y += yT;
          } else if (currentAreaEle.type == "circle") {
            this.areas[this.currentArea].radius += xT;
          }
        }
        this.downPoint.x = event.x;
        this.downPoint.y = event.y;
      }
    },
    up() {
      if (event.button == 0 && event.buttons == 0) {
        this.currentArea = -1;
        this.editMode = 0;
        this.downPoint.x = event.x;
        this.downPoint.y = event.y;
      }
    },
  },
};
</script>

<style>
.area-input{
  width: 200px;
}
.area-input-group{
  display: none;
  position: absolute;
  left: -210px;
}
.areas{
  width: fit-content;
  height: fit-content;
}
.hot-editor-area {
  position: relative;
}
.area-box {
  border: 1px rgb(150, 235, 238) solid;
  background: rgba(150, 235, 238, 0.3);
  cursor: move;
  position: absolute;
}
.del-box-btn {
  position: absolute;
  cursor: pointer;
  top: -7px;
  left: calc(50% - 7px);
  border: black 1px solid;
  background: #fff;
  line-height: 12px;
  width: 12px;
  height: 12px;
  text-align: center;
  vertical-align: center;
  border-radius: 100%;
}
.del-box-btn::after {
  content: "×";
  position: absolute;
  left: 0px;
}

.right-box-btn {
  font-size: smaller;
  position: absolute;
  top: calc(50% - 7px);
  left: calc(100% - 7px);
  border: black 1px solid;
  background: #fff;
  width: 12px;
  cursor: e-resize;
  height: 12px;
  text-align: center;
  vertical-align: center;
  border-radius: 100%;
}

.bottom-box-btn {
  font-size: smaller;
  position: absolute;
  top: calc(100% - 7px);
  left: calc(50% - 7px);
  border: black 1px solid;
  background: #fff;
  cursor: n-resize;
  width: 12px;
  height: 12px;
  text-align: center;
  vertical-align: center;
  border-radius: 100%;
}
</style>