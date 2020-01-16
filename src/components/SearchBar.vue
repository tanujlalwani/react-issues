<template>
  <div class="search-bar">
    <div
      class="search-bar__input-container"
      :class="{ 'search-bar__input-container--active': query }"
    >
      <input
        class="search-bar__input"
        v-model="query"
        @input="search"
        placeholder="Search for an issue..."
      />
      <span class="search-bar__autocomplete">{{ autocomplete }}</span>
    </div>
    <ul v-if="no_results" class="search-bar__issue-list">
      <li class="issue">
        <span class="no-issue__title">
          <span v-if="this.api_limit_exceeded">Github API limit exceeded.</span>
          <span v-else>No issues found.</span>
        </span>
      </li>
    </ul>
    <ul v-else class="search-bar__issue-list" v-show="query">
      <search-result
        v-for="(issue, index) in issues"
        :key="index"
        :class="{ 'issue--selected': index == selected_issue}"
        :issue="issue"
        :index="index"
      ></search-result>
    </ul>
  </div>
</template>

<script>
import SearchResult from "@/components/SearchResult.vue";

export default {
  components: {
    SearchResult
  },
  data() {
    return {
      query: null,
      api_response: null,
      api_limit_exceeded: false,
      selected_issue: -1,
      inputElement: null
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
        // return this.api_response.items.slice(0, 7);
        return this.api_response.items.filter(issue =>
          issue.title.startsWith(this.query)
        );
      } else {
        return [];
      }
    },
    autocomplete() {
      if (
        this.selected_issue != -1 &&
        this.query != "" &&
        this.api_response &&
        !this.api_limit_exceeded
      ) {
        return this.issues[this.selected_issue].title.slice(this.query.length);
      } else {
        return "";
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
    search: function() {
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
    },
    openSelected() {
      window.open(this.issues[this.selected_issue].html_url, "_blank");
    },
    selectIssue(index) {
      this.selected_issue = index;
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
        this.inputElement.style.width = "auto";
        e.preventDefault();
      }
    },
    resizeInput() {
      if (!this.inputElement) {
        this.inputElement = document.getElementsByClassName(
          "search-bar__input"
        )[0];
      }

      if (this.inputElement.value.length) {
        this.inputElement.style.width = this.inputElement.value.length + "ch";
      } else {
        this.inputElement.style.width = "auto";
      }
    }
  },
  mounted() {
    document.addEventListener("keydown", this.handleKeyEvent, false);
    document.addEventListener("input", this.resizeInput, false);
  }
};
</script>

<style lang="scss" scoped>
@import url("https://rsms.me/inter/inter.css");

.search-bar {
  font-family: "Inter", sans-serif;

  .search-bar__input-container {
    width: 35vw;
    padding: 15px;

    border: solid 1px #e1e4e8;
    border-radius: 10px;

    background-color: rgb(241, 241, 241);
    transition: background-color 0.5s ease;

    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;

    .search-bar__input {
      font-size: 15px;
      font-weight: 500;
      font-family: "Inter", sans-serif;
      user-select: none;

      border: none;
      display: inline;
      font-family: inherit;
      font-size: inherit;
      padding: none;
      background-color: transparent;

      &::placeholder {
        color: #aaa;
      }

      &:focus {
        outline: none;
      }
    }

    .search-bar__autocomplete {
      font-size: 15px;
      font-weight: 500;
      font-family: "Inter", sans-serif;
      user-select: none;

      width: 25vw;

      color: #999;

      position: relative;
      left: -0.5ch;
    }
  }

  .search-bar__input-container--active {
    border-bottom-width: 0px;
    border-radius: 10px 10px 0px 0px;

    user-select: text;
    background-color: #fff;
  }

  .search-bar__issue-list {
    width: 35vw;
    list-style: none;
    overflow: hidden;

    border: solid 1px #e1e4e8;
    border-radius: 0px 0px 10px 10px;

    position: absolute;
    margin: 0;
    padding: 0;
  }
}
</style>
