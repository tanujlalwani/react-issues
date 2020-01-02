<template>
  <div class="search-bar">
    <input
      class="search-bar__input"
      :class="{ 'search-bar__input--active': query }"
      v-model="query"
      @input="search"
      placeholder="Search for an issue..."
    />
    <ul v-if="no_results" class="search-bar__issue-list">
      <li class="issue">
        <span class="no-issue__title">
          <span v-if="this.api_limit_exceeded">Github API limit exceeded.</span>
          <span v-else>No issues found.</span>
        </span>
      </li>
    </ul>
    <ul v-else class="search-bar__issue-list" v-show="query">
      <li
        class="issue"
        v-for="(issue, index) in issues"
        :key="index"
        :class="{ 'issue--selected': index == selected_issue}"
        @mouseover="selected_issue = index"
        @click="openSelected()"
      >
        <span class="issue__title" :title="issue.title">{{ issue.title }}</span>
        <div v-if="issue.labels" class="issue__labels">
          <span
            class="label"
            v-for="label in issue.labels"
            :key="label.id"
            :style="{ 'background-color': '#'+label.color } "
          >{{ label.name }}</span>
        </div>
        <span class="issue__info">
          #{{ issue.number }}
          <span v-if="issue.state=='open'">opened {{ getDays(issue.created_at) }}</span>
          <span v-else>closed {{ getDays(issue.closed_at) }}</span>
        </span>
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
      api_response: null,
      api_limit_exceeded: false,
      selected_issue: -1
    };
  },
  computed: {
    search_api_url() {
      return `https://api.github.com/search/issues?q=${this.query}+repo%3Afacebook%2Freact`;
    },
    issues() {
      if (
        this.query &&
        this.api_response &&
        this.api_response.total_count > 0
      ) {
        return this.api_response.items.slice(0, 7);
      } else {
        return [];
      }
    },
    no_results() {
      return this.query != "" &&
        this.api_response &&
        this.api_response.total_count <= 0
        ? true
        : false;
    }
  },
  watch: {
    query: function() {
      if (this.query == "") {
        this.api_response = null;
        this.api_limit_exceeded = false;
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
          .then(response => {
            if (response.status == 200) {
              this.api_limit_exceeded = false;
              return response.json();
            } else if (response.status == 403) {
              this.api_limit_exceeded = true;
              return {
                total_count: 0
              };
            }
          })
          .then(data => {
            this.api_response = data;
          });
      }
    }, 10),
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
        return Math.floor(days_diff) + " days ago";
      } else {
        return "on " + this.formatDate(created_date);
      }
    },
    openSelected() {
      window.open(this.issues[this.selected_issue].html_url, "_blank");
    },
    handleKeyEvent(e) {
      let keys = [];
      keys[e.keyCode] = true;

      // Arrow Down
      if (keys[40]) {
        this.selected_issue = (this.selected_issue + 1) % this.issues.length;
        e.preventDefault();
      }

      // Arrow Up
      else if (keys[38]) {
        if (this.selected_issue <= 0) {
          this.selected_issue = this.issues.length - 1;
        } else {
          this.selected_issue--;
        }
        e.preventDefault();
      }

      // Enter
      if (keys[13]) {
        this.openSelected();
        e.preventDefault();
      }

      // Escape
      else if (keys[27]) {
        this.query = null;
        this.selected_issue = -1;
        this.api_response = null;
        e.preventDefault();
      }
    }
  },
  mounted() {
    document.addEventListener("keydown", this.handleKeyEvent, false);
  }
};
</script>

<style>
</style>
