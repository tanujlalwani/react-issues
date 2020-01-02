<template>
  <div class="app fill-viewport">
    <router-view />
  </div>
</template>

<style lang="scss">
/* CSS Reset */
@import "./styles/_reset.scss";

/* Font Imports */
@import url("https://rsms.me/inter/inter.css");
html {
  font-family: "Inter", sans-serif;
}

/* Font Settings */

* {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

/* Layout Settings */

* {
  box-sizing: border-box;
  -webkit-overflow-scrolling: touch;
}

/* CSS Global Style Variables */

:root {
  --font-family: "Inter", sans-serif;
  --font-style-default: normal;
  --font-size-default: 14px;
  --font-size-small: 12px;
  --font-weight-normal: 400;
  --font-weight-bold: 600;
}

/* App Layout */

html,
body,
.app {
  background-color: var(--primary-color);
  z-index: 0;

  font: var(--font-style-default) var(--font-weight-normal)
    var(--font-size-default) var(--font-family);
}

.app {
  overflow: hidden;
}

.fill-viewport {
  width: 100vw;
  height: 100vh;
}
</style>
