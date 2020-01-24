<template>
  <v-container>
    <v-row text-center wrap>
      <v-col>
        <v-combobox
          v-model="users"
          item-disabled
          chips
          clearable
          label="Input AtCoder usernames"
          multiple
          solo
        >
          <template v-slot:selection="{ attrs, item }">
            <v-chip v-bind="attrs" close @click:close="remove(item)">
              <strong>{{ item }}</strong>
            </v-chip>
          </template>
        </v-combobox>
      </v-col>
    </v-row>
    <v-row>
      <v-col>
        <div id="chart">
          <Chart v-if="!chartData.datasets.isEmpty" :chart-data="chartData" />
        </div>
      </v-col>
    </v-row>
  </v-container>
</template>

<style scoped>
#chart {
  width: 80vw;
}
</style>

<script>
import axios from "axios";
import Chart from "./Chart";

export default {
  name: "Main",
  components: {
    Chart
  },
  data: () => {
    return {
      users: [],
      chartData: {
        datasets: []
      }
    };
  },
  methods: {
    update_datasets() {
      let promises = this.users.map(async (user, index) => {
        let response = await axios.get(
          "https://any-proxy.herokuapp.com/https://atcoder.jp/users/" +
            user +
            "/history/json"
        );
        return {
          label: user,
          data: response.data
            .filter(contest => {
              return contest.IsRated;
            })
            .map(contest => {
              return {
                x: Date.parse(contest.EndTime),
                y: contest.NewRating
              };
            }),
          borderColor: "hsl(" + 149 * index + ", 60%, 60%)",
          showLine: true
        };
      });
      Promise.all(promises).then(result => {
        this.chartData = { datasets: result };
      });
    },
    remove(user) {
      this.users.splice(
        this.users.findIndex(item => {
          item == user;
        }),
        1
      );
    }
  },
  watch: {
    users: function() {
      this.update_datasets();
    }
  },
  created: function() {
    this.update_datasets();
  }
};
</script>
