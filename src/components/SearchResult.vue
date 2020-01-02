<template>
  <li class="issue">
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
</template>

<script>
export default {
  props: ["issue"],
  methods: {
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
    }
  }
};
</script>

<style lang="scss">
.issue {
  padding: 7px 15px;
  border-bottom: solid 1px #e1e4e8;
  background-color: #fff;

  cursor: default;

  &:last-child {
    border-width: 0px;
    padding-bottom: 10px;
  }

  .issue__title {
    font-size: 15px;
    font-weight: 600;
    color: #000;
    display: block;

    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;

    margin-bottom: 4px;
  }

  .issue__labels {
    margin-bottom: 3px;

    .label {
      font-size: 10px;
      font-weight: 500;
      color: #000;

      padding: 5px;
      margin-right: 5px;
      border-radius: 5px;
    }
  }

  .issue__info {
    font-size: 10px;
    color: #444;
  }
}

.issue--selected {
  background-color: #f6f8fa;
}
</style>
