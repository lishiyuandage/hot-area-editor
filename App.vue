<template>
  <div class="box">
      <div class="box-editor">
        <HotAreaEditor :url="url" ref="editor1" :shape="shape"></HotAreaEditor>
      </div>
      <div class="box-options">
        <input type="text" v-model="url" placeholder="图片链接">
        <button @click="exportCode">导出</button>
        <button @click="addArea">添加热区</button>
        <select v-model="shape">
          <option value="circle">圆</option>
          <option value="rect">矩形</option>
          <option value="poly">多边形</option>
        </select>
      </div>
      <div v-html="html"></div>
  </div>
</template>

<script>
import HotAreaEditor from './HotAreaEditor.vue'

export default {
  data(){
      return {
          html:'',
          shape:'circle',
          add:false,
          url:'https://th.bing.com/th/id/R0313037203832ca3cb56be092a71fe76?rik=SSjYzMbEAuZVkQ&riu=http%3a%2f%2fwww.pooban.com%2fimages%2f201511%2fgoods_img%2f2070_G_1447002951605.png&ehk=kPYBvHm58hk6Jyl%2fC5qCTWb5qMeDK55twe54ZjqwOes%3d&risl=&pid=ImgRaw'
      }
  },
  components: { HotAreaEditor },
  methods:{
    addArea(){
      this.$refs.editor1.addArea()
    },
    exportCode(){
      let code = this.$refs.editor1.exportCode()
      this.html = code.html
      console.log(code)
    }
  }
}
</script>

<style>
.box-editor,.box-options{
  width: fit-content;
  margin: 5vh auto;
}
</style>