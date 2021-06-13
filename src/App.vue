<template>
  <main id="app">
    <div class="loader-wrapper" :class="{ loading: loading }">
      <div class="loader">Loading...</div>
    </div>
    <ranking />
  </main>
</template>

<script>
import ranking from './components/ranking.vue'
export default {
  name: 'App',
  components: {
    ranking,
  },
  data() {
    return {
      loading: true,
    }
  },
  methods: {},
}
</script>

<style lang="scss">
html {
  overflow: overlay;
}
#app {
  font-family: Segoe UI, Meiryo, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>

<style scoped lang="scss">
#app {
  position: relative;
  height: 100vh;
  .loader-wrapper {
    padding-top: 40vh;
    position: absolute;
    z-index: 100;
    width: 100%;
    height: 100%;
    background-color: white;
    opacity: 0;
    pointer-events: none;
    transition: opacity 0.5s;
    // &.loading {
    //   opacity: 1;
    //   pointer-events: auto;
    // }
  }
  .loader,
  .loader:before,
  .loader:after {
    background: #858799;
    animation: load1 1.2s infinite ease-in-out;
    width: 1em;
    height: 4em;
  }
  .loader {
    color: #858799;
    text-indent: -9999em;
    margin: 0 auto;
    position: relative;
    font-size: 15px;
    transform: translateZ(0);
    animation-delay: -0.2s;
  }
  .loader:before,
  .loader:after {
    position: absolute;
    top: 0;
    content: '';
  }
  .loader:before {
    left: -1.8em;
    animation-delay: -0.4s;
  }
  .loader:after {
    left: 1.8em;
  }
  @keyframes load1 {
    0%,
    80%,
    100% {
      box-shadow: 0 0;
      height: 6em;
    }
    40% {
      box-shadow: 0 -2em;
      height: 7em;
    }
  }
}
</style>
