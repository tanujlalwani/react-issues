<template>
  <div class="home fill-viewport">
    <span class="react-logo">
      <i class="fab fa-react"></i>
    </span>
    <search-bar></search-bar>
  </div>
</template>

<script>
import SearchBar from "@/components/SearchBar.vue";

export default {
  name: "home",
  components: {
    SearchBar
  }
};
</script>

<style lang="scss" scoped>
.home {
  .search-bar {
    display: block;
    max-width: 35vw;
    margin: 10% auto 0 auto;

    z-index: 100;
  }

  .react-logo {
    position: fixed;
    text-align: center;
    width: 100%;
    height: 100%;
    margin: 0 auto;
    top: 45%;

    z-index: 0;

    i {
      animation: rotating 60s linear infinite;
      font-size: 120px;
      color: rgb(49, 224, 255);
    }
  }

  @keyframes rotating {
    from {
      -webkit-transform: rotate(0deg);
    }
    to {
      -webkit-transform: rotate(360deg);
    }
  }
}
</style>
