<template>
  <ColorPalette />
</template>

<script>
import ColorPalette from "./components/ColorPalette.vue";

export default {
  name: "App",
  components: {
    ColorPalette,
  },
};
</script>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
}
#app {
  height: 100%;
}
body {
  height: 100vh;
  overflow: hidden;
}
</style>
