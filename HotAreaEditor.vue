<template>
  <div
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
    <div v-for="(area, index) in areas" :key="index">
      {{ area }}
    </div>
  </div>
</template>

<script>
/**
 * {
 *  actName:'',
 *  version:1.0,
 *  img:{
 *     url:http://baidu.com/logo.jpg,
 *     height:50,
 *     width:200,
 *     areas:[{
 *         type: 'rect',
 *         x: 0,
 *         y: 0,
 *         width: this.realImage.width,
 *         height: this.realImage.height,
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
    };
  },

  name: "HotAreaEditor",

  props: {
    url: { type: String,default:"" },
    json: {
      type: Object,
      default: () => {
        return null;
      },
    },
  },
  mounted() {
    console.log('------------热区编辑器初始化-------------')
    this.initArea()
  },
  methods: {
    initArea(){
        if(this.json != null){
            console.log("hot-area-editor init json")

        }else if(this.url != ""){
            console.log("hot-area-editor init url")

        }
    },
    exportCode() {},
    move() {},
    up() {},
  },
};
</script>

<style>
</style>