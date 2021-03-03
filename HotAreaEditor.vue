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
        <div class="del-box-btn" @click="delArea(index)"></div>
        <div @mousedown="down(index, 2)" class="right-box-btn"></div>
        <div @mousedown="down(index, 3)" class="bottom-box-btn"></div>
      </div>
      <div
        class="area-box"
        v-if="area.type == 'circle'"
        @mousedown="down(index, 1)"
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
      console.log(oldVal + "-----------", val);
      if (oldVal != "" && oldVal != val) {
        this.changeUrl();
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
    delArea(index) {
      let i = Number(index);
      this.areas.splice(i, 1);
    },
    down(index, type) {
      if (event.button == 0 && event.buttons == 1) {
        console.log(event)
        this.currentArea = index;
        this.editMode = type;
        this.downPoint.x = event.x;
        this.downPoint.y = event.y;
      }
      event.preventDefault()
      event.stopPropagation()
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
    addArea() {
      console.log("-----------");
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
      });
    },
    exportCode() {},
    move() {
      if (event.button == 0 && event.buttons == 1 && this.editMode != 0) {
        let xT = event.x - this.downPoint.x
        let yT = event.y - this.downPoint.y

        let currentAreaEle=this.areas[this.currentArea]

        if (this.editMode == 1) {
          console.log(currentAreaEle)
          this.areas[this.currentArea].point[0].x += xT
          this.areas[this.currentArea].point[0].y += yT
          this.areas[this.currentArea].point[1].x += xT
          this.areas[this.currentArea].point[1].y += yT
        } else if (this.editMode == 2) {
          if (currentAreaEle.type == 'rect') {
            this.areas[this.currentArea].point[1].x += xT
          } else if(currentAreaEle.type == 'circle') {
            this.areas[this.currentArea].point[0].x -= xT
            this.areas[this.currentArea].point[0].y -= xT
            this.areas[this.currentArea].radius +=xT            
          }
        } else if (this.editMode == 3) {
          if (currentAreaEle.type == 'rect') {
            this.areas[this.currentArea].point[1].y += yT
          } else if(currentAreaEle.type == 'circle') {
            this.areas[this.currentArea].radius +=xT
          }          
        }
        this.downPoint.x = event.x
        this.downPoint.y = event.y
      }
    },
    up() {
      if (event.button == 0 && event.buttons == 1) {
        this.currentArea = 0;
        this.editMode = 1; 
        this.downPoint.x = event.x;
        this.downPoint.y = event.y;
      }
    },
  },
};
</script>

<style>
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
.del-box-btn::after{
  content: '×';
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