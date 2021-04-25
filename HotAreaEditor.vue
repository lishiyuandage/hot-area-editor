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
      <!-- 矩形 -->
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
        <div
          class="area-input area-input-group"
          @click="noBubble"
          @mousedown="noBubble"
          @mouseup="noBubble"
        >
          <input
            class="area-input"
            type="text"
            v-model="area.url"
            placeholder="链接/url"
          />
          <input
            class="area-input"
            type="text"
            v-model="area.detail"
            placeholder="详细说明/alt"
          />
          <input
            class="area-input"
            type="text"
            v-model="area.clazz"
            placeholder="类/class"
          />
          <select
            class="area-input"
            type="text"
            v-model="area.target"
            placeholder="打开方式/target"
          >
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
      <!-- 圆 -->
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
        <div
          class="area-input area-input-group"
          @click="noBubble"
          @mousedown="noBubble"
          @mouseup="noBubble"
        >
          <input
            class="area-input"
            type="text"
            v-model="area.url"
            placeholder="链接/url"
          />
          <input
            class="area-input"
            type="text"
            v-model="area.detail"
            placeholder="详细说明/alt"
          />
          <input
            class="area-input"
            type="text"
            v-model="area.clazz"
            placeholder="类/class"
          />
          <select
            class="area-input"
            type="text"
            v-model="area.target"
            placeholder="打开方式/target"
          >
            <option value="_blank">_blank</option>
            <option value="_self">_self</option>
            <option value="_parent">_parent</option>
            <option value="_top">_top</option>
          </select>
        </div>
        <div class="del-box-btn" @click="delArea(index)"></div>
        <div @mousedown="down(index, 2)" class="right-box-btn"></div>
      </div>
      <!-- 多边形 -->
      <div
        v-if="area.type == 'poly'"
        class="area-box"
        :style="
          'top:' +
          area.point.reduce((i, j) => (i.y < j.y ? i : j)).y +
          'px;left:' +
          area.point.reduce((i, j) => (i.x < j.x ? i : j)).x +
          'px'
        "
      >
        <svg
          class="area-box-svg"
          :width="
            area.point.reduce((i, j) => (i.x > j.x ? i : j)).x -
            area.point.reduce((i, j) => (i.x < j.x ? i : j)).x
          "
          :height="
            area.point.reduce((i, j) => (i.y > j.y ? i : j)).y -
            area.point.reduce((i, j) => (i.y < j.y ? i : j)).y
          "
        >
          <polygon
            @mousedown="down(index, 1)"
            @click="openOrCloseAttr"
            :points="
              area.point
                .map(
                  (i) =>
                    i.x -
                    area.point.reduce((i, j) => (i.x < j.x ? i : j)).x +
                    ',' +
                    (i.y - area.point.reduce((i, j) => (i.y < j.y ? i : j)).y)
                )
                .join(',')
            "
          />
        </svg>
        <div
          class="area-input area-input-group"
          @click="noBubble"
          @mousedown="noBubble"
          @mouseup="noBubble"
        >
          <input
            class="area-input"
            type="text"
            v-model="area.url"
            placeholder="链接/url"
          />
          <input
            class="area-input"
            type="text"
            v-model="area.detail"
            placeholder="详细说明/alt"
          />
          <input
            class="area-input"
            type="text"
            v-model="area.clazz"
            placeholder="类/class"
          />
          <select
            class="area-input"
            type="text"
            v-model="area.target"
            placeholder="打开方式/target"
          >
            <option value="_blank">_blank</option>
            <option value="_self">_self</option>
            <option value="_parent">_parent</option>
            <option value="_top">_top</option>
          </select>
        </div>
        <div class="del-box-btn" @click="delArea(index)"></div>
      </div>
      <!-- end -->
    </div>
    <!-- svg editor -->
    <svg v-if="svgEditorOpen" class="svg-area-editor" :width="width" :height="height" @click="addPoint">
      <polygon :points="svgPoint.map( i => i.x + ',' + i.y ).join(',')"/>
      <rect v-for="(point, index) in svgPoint" :key="index" width="4" height="4" :y="point.y" :x="point.x"  @click="delPoint(index)"/>
    </svg>
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
const VERSION = 1.1;

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
      svgPoint:[],
      currentArea: 0,
    };
  },

  name: "HotAreaEditor",

  props: {
    svgEditorOpen:{type:Boolean,default:false},
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
        this.areas = [];
      }
    },

    shape(val) {
      if (val != "rect" && val != "circle" && val != "poly") {
        throw new Error(
          "请设置合法的形状;the shape：" + val + " is not support."
        );
      }
    },
  },

  methods: {
    openOrCloseAttr() {
      let ele = null;
      if (event.target.nodeName == "polygon") {
        ele =
          event.target.parentElement.querySelector(".area-input-group") ||
          event.target.parentElement.parentElement.querySelector(
            ".area-input-group"
          );
      } else {
        ele = event.target.querySelector(".area-input-group");
      }
      let display = ele.style.display;
      console.log(display);
      ele.style.display = display == "block" ? "none" : "block";
      event.stopPropagation();
    },
    noBubble() {
      event.stopPropagation();
    },
    delArea(index) {
      let i = Number(index);
      this.areas.splice(i, 1);
      event.stopPropagation();
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
      };
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
    // -----------------svg-----
    openOrCloseSvgEditor(){
      let display = this.$el.querySelector(".svg-area-editor").style.display
      this.$el.querySelector(".svg-area-editor").style.display = display == 'block' ? 'none' : 'block'
    },
    delPoint(index){
      let i = Number(index);
      this.svgPoint.splice(i, 1);
      event.stopPropagation();
    },
    addPoint(){
      let rect =this.$el.getBoundingClientRect()
      let continarX = rect.x
      let continarY = rect.y
      let eventX = event.x
      let eventY = event.y
      this.svgPoint.push({
        x:eventX-continarX,
        y:eventY-continarY
      })
    },
    // -----------------svg--end----
    addArea() {
      let point = [
        { x: 0, y: 0 },
        { x: 50, y: 50 },
      ];
      if (this.shape == "poly") {
        if(this.svgPoint.length < 3){
          throw new Error("多边形不能少与3个点;poly must have over 3 points.")
        }
        point = this.svgPoint
        this.svgPoint = []
      }
      this.areas.push({
        type: this.shape,
        point: point,
        radius: 25,
        url: "",
        clazz: "",
        detail: "",
        target: "_blank",
      });
    },
    exportCode() {
      let code = Math.random();
      let json = {
        name: "hot-area-" + code,
        version: this.version,
        image: {
          url: this.url,
          height: this.height,
          width: this.width,
          areas: JSON.parse(JSON.stringify(this.areas)),
        },
      };

      let html =
        `<img src='` +
        json.image.url +
        `' data-name='` +
        json.name +
        `' data-version='` +
        json.version +
        `' usemap='#map` +
        code +
        `'>
      <map name='map` +
        code +
        `' id='map` +
        code +
        `'>`;
      for (let area of json.image.areas) {
        let coords = this.getCoords(area);
        html +=
          `<area shape='` +
          area.type +
          `' coords='` +
          coords +
          `' alt='` +
          area.detail +
          `' href='` +
          area.url +
          `' target='` +
          area.target +
          `' class='` +
          area.clazz +
          `'>`;
      }
      html += `</map>`;

      return {
        json: json,
        html: html,
      };
    },
    getCoords(area) {
      let coords = "";
      switch (area.type) {
        case "rect":
          coords =
            area.point[0].x +
            "," +
            area.point[0].y +
            "," +
            area.point[1].x +
            "," +
            area.point[1].y;
          break;
        case "poly":
          coords = area.point.map((i) => i.x + "," + i.y).join(",");
          break;
        default:
          coords =
            area.point[0].x +
            area.radius +
            "," +
            (area.point[0].y + area.radius) +
            "," +
            area.radius;
      }
      return coords;
    },
    move() {
      if (event.button == 0 && event.buttons == 1 && this.editMode != 0) {
        let xT = event.x - this.downPoint.x;
        let yT = event.y - this.downPoint.y;

        let currentAreaEle = this.areas[this.currentArea];

        if (this.editMode == 1) {
          if (currentAreaEle.type == "poly") {
            for (let point of this.areas[this.currentArea].point) {
              point.x += xT;
              point.y += yT;
            }
          } else {
            this.areas[this.currentArea].point[0].x += xT;
            this.areas[this.currentArea].point[0].y += yT;
            this.areas[this.currentArea].point[1].x += xT;
            this.areas[this.currentArea].point[1].y += yT;
          }
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
.area-input {
  width: 200px;
}
.area-input-group {
  display: none;
  position: absolute;
  left: -210px;
}
.areas {
  width: fit-content;
  height: fit-content;
}
.hot-editor-area {
  position: relative;
}
.svg-area-editor{
  position: absolute;
  left: 0px;
  top:0px;
  background: rgba(128, 18, 86, 0.3);
}
.svg-area-editor rect{
  position: absolute;
  fill:rgb(255, 255, 255);
  stroke-width:1;
  stroke:rgb(0,0,0)
}
.svg-area-editor polygon{
  fill: rgba(150, 235, 238, 0.3);
  stroke: rgb(150, 235, 238);
  stroke-width: 1;
}
.area-box-svg polygon{
  fill: rgba(150, 235, 238, 0.3);
  stroke: rgb(150, 235, 238);
  stroke-width: 1;
}
.area-box-svg {
  position: absolute;
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