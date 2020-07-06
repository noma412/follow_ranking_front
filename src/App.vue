<template>
  <main id="app">
    <follow msg="ランキング(フォロー)"/>
  </main>
</template>

<script>
import follow from './components/follow.vue'

export default {
  name: 'App',
  components: {
    follow
  }
}
</script>

<style lang="scss">
#app {
  font-family: Segoe UI, Meiryo, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
