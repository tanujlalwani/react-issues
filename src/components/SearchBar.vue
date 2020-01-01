<template>
  <div class="search-bar">
    <input class="search-bar__input" v-model="query" @input="search" />
    <ul v-if="issues" class="search-bar__results-list">
      <li class="search-bar__results-list__item" v-for="issue in issues" :key="issue.id">
        <a :href="issue.html_url" target="_blank">
          <div>
            <span>{{ issue.title }}</span>
            <div>
              <span
                v-for="label in issue.labels"
                :key="label.id"
                :style="{ 'background-color': '#'+label.color } "
              >{{ label.name }}</span>
            </div>
            <span>#{{ issue.number }} opened {{ getDays(issue.created_at) }} by {{ issue.user.login }}</span>
          </div>
        </a>
      </li>
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
    issues() {
      if (this.query && this.api_response) {
        return this.api_response.items.slice(0, 7);
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
    }, 50),
    formatDate: function(date) {
      var monthNames = [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December"
      ];

      var day = date.getDate();
      var monthIndex = date.getMonth();
      var year = date.getFullYear();

      return day + " " + monthNames[monthIndex] + " " + year;
    },
    getDays(created_at) {
      let now_date = new Date();
      let created_date = new Date(created_at);
      let days_diff =
        (now_date.getTime() - created_date.getTime()) / (1000 * 3600 * 24);

      if (days_diff < 1) {
        return "today";
      } else if (days_diff == 1) {
        return "yesterday";
      } else if (days_diff <= 10) {
        return days_diff + " days ago";
      } else {
        return "on " + this.formatDate(created_date);
      }
    }
  }
};
</script>

<style>
</style>
