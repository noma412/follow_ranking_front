<template>
  <main id="app">
    <div class="loader-wrapper" :class="{ loading: loading }">
      <div class="loader">Loading...</div>
    </div>
    <div
      class="error-modal-wrapper"
      :class="{ error: error }"
      @click="error = false"
    >
      <div class="error-modal">
        <p v-html="errorText"></p>
        <v-btn class="btn" color="#E3F2FD"
          >OK<span style="padding-left:1px;">!</span></v-btn
        >
      </div>
    </div>
    <ranking @load="loadEvent" @error="errorEvent" />
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
      error: false,
      errorText: '',
    }
  },
  methods: {
    loadEvent(param) {
      this.loading = param
    },
    errorEvent(param) {
      this.errorText = param
      this.error = true
    },
  },
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
    &.loading {
      opacity: 1;
      pointer-events: auto;
    }
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
  .error-modal-wrapper {
    width: 100vw;
    height: 100vh;
    background-color: rgba(0, 0, 0, 0.4);
    position: fixed;
    z-index: 150;
    transition: opacity 0.4s;
    cursor: pointer;
    opacity: 0;
    pointer-events: none;
    &.error {
      opacity: 1;
      pointer-events: auto;
    }
    .error-modal {
      position: absolute;
      top: 30%;
      left: 50%;
      transform: translateX(-50%);
      width: 480px;
      background-color: white;
      border-radius: 20px;
      padding-top: 30px;
      padding-bottom: 30px;
      display: flex;
      flex-direction: column;
      align-items: center;
      p {
        font-size: 18px;
        line-height: 1.5;
        text-align: center;
      }
      .v-btn {
        width: 100px;
        height: 40px;
        padding: 0 0 2px !important;
        font-size: 18px;
        margin-top: 20px;
      }
    }
  }
}
</style>
