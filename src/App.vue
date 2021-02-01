<template>
<div>
  <div class="board-container">
    <StringGrid :input="input.toUpperCase()" :columns="20" :rows="7" />
  </div>
  <div class="controls">
    <input type="text" v-model="temp"><br><br>
    <button @click="input = temp">Submit</button>
    <button @click="goFullscreen">Fullscreen</button>
  </div>
</div>
</template>

<script>
import StringGrid from '@/components/StringGrid.vue'

export default {
  name: 'App',
  components: {
    StringGrid
  },
  data() {
    return {
      temp: '',
      input: '',
      // input: 'Welcome to Erik\'s Solari Board',
    }
  },
  methods: {
    goFullscreen() {
      document.body.requestFullscreen()
    }
  }
}
</script>

<style lang="scss">
@import '~@/scss/design.scss';
html, body {
  padding: 0;
  margin: 0;
  background-color: $grey;
}
.board-container {
  display: flex;
  flex-wrap: wrap;
  text-align: center;
  justify-content: center;
  margin-top: 50px;
}
</style>