<template>
  <div class="search-bar">
    <input />
  </div>
</template>

<script>
import debounce from "../../node_modules/lodash/debounce";
export default {
  data() {
    return {
      query: null,
      api_response: null
    };
  },
  methods: {
    search: debounce(function() {
      if (this.query) {
        fetch(this.search_api_url, {
          method: "GET",
          headers: {
            authorization: "token 029caf468d6280e5e85a83e7393c7056a423b7e9"
          }
        })
          .then(response => response.json())
          .then(data => {
            this.api_response = data;
          })
          .catch(err => {
            // eslint-disable-next-line no-console
            console.log(err);
          });
      }
    }, 100)
  }
};
</script>

<style>
</style>
