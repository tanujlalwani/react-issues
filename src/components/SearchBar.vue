<template>
  <div class="search-bar">
    <input class="search-bar__input" v-model="query" @input="search" />
    <ul v-if="search_results" class="search-bar__results-list">
      <li
        class="search-bar__results-list__item"
        v-for="result in search_results"
        :key="result.id"
      >{{ result.title }}</li>
    </ul>
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
  computed: {
    search_api_url() {
      return `https://api.github.com/search/issues?q=${this.query}+repo%3Afacebook%2Freact`;
    },
    search_results() {
      if (this.query) {
        return this.api_response.items;
      } else {
        return [];
      }
    }
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
