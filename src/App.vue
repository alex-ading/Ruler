<template>
  <Ruler :width="width" :height="height" :rulerWidth="10" :span="100" :gap="20" :spanColor="spanColor" :unit="unit" :showGrid="false"></Ruler>
  <div style="position: absolute; top: 20px; left: 20px;">
    <!-- <p style="width: 40px; height: 60px; border: 1px solid black;">test</p>
    <p style="width: 40px; height: 60px; border: 1px solid black;">test</p> -->
  </div>
</template>

<script lang="ts">
import { defineComponent, onMounted, ref } from 'vue'
import Ruler from './components/Ruler.vue'

export default defineComponent({
  name: 'App',
  components: {
    Ruler
  },
  setup() {
    let width = ref(210);
    let height = ref(297);
    let spanColor = ref('#b7b7b7');
    let unit = ref('mm');

    // onMounted(() => {
    //   setTimeout(() => {
    //     width.value = 800;
    //     height.value = 500;
    //     spanColor.value = 'yellow'
    //   }, 2000)

    //   setTimeout(() => {
    //     width.value = 600;
    //     height.value = 400;
    //     spanColor.value = '#b7b7b7'
    //   }, 4000)
    // })

    return {
      width,
      height,
      spanColor,
      unit
    }
  }
})
</script>

<style>
body {
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
</style>
