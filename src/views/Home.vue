<template>
  <div class="home">
    <!-- <img alt="React logo" src="../assets/logo.png" /> -->
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
