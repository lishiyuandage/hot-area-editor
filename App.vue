<template>
  <div class="box">
      <HotAreaEditor :url="url" ref="editor1" :shape="shape"></HotAreaEditor>
      <button @click="addArea">添加热区</button>
      <select v-model="shape">
        <option value="circle">圆</option>
        <option value="rect">矩形</option>
        <option value="poly">多边形</option>
      </select>
  </div>
</template>

<script>
import HotAreaEditor from './HotAreaEditor.vue'

export default {
  data(){
      return {
          shape:'circle',
          add:false,
          url:'https://th.bing.com/th/id/R0313037203832ca3cb56be092a71fe76?rik=SSjYzMbEAuZVkQ&riu=http%3a%2f%2fwww.pooban.com%2fimages%2f201511%2fgoods_img%2f2070_G_1447002951605.png&ehk=kPYBvHm58hk6Jyl%2fC5qCTWb5qMeDK55twe54ZjqwOes%3d&risl=&pid=ImgRaw'
      }
  },
  components: { HotAreaEditor },
  methods:{
    addArea(){
      console.log(this)
      this.$refs.editor1.addArea()
    },
    changeAreaType(){

    }
  }
}
</script>

<style>
</style>