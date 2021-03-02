<template>
  <div class="box">
      <HotAreaEditor :url="url"></HotAreaEditor>
  </div>
</template>

<script>
import HotAreaEditor from './HotAreaEditor.vue'

export default {
  data(){
      return {
          url:'https://www.bing.com/th?id=OHR.VolcanoLlaima_ZH-CN3436127573_1920x1080.jpg&rf=LaDigue_1920x1080.jpg&pid=HpEdgeAn'
      }
  },
  components: { HotAreaEditor },

}
</script>

<style>
</style>