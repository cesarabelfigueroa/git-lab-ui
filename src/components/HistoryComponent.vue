<style scoped>
.strong {
  font-weight: bold;
}

.added {
  color: green;
}

.deleted {
  color: red;
}
</style>
<template>
  <v-container>
    <v-card>
      <v-card-title class="blue white--text text-h5">
        Changes History
      </v-card-title>
      <v-row class="pa-4" justify="space-between">
        <v-col cols="6">
          <v-treeview
            :active.sync="active"
            :items="items"
            :load-children="fetchUsers"
            activatable
            color="warning"
            open-on-click
            transition
          >
            <template v-slot:prepend="{ item }">
              <v-icon v-if="!item.children"> mdi-source-commit </v-icon>
              {{ item.id }}
            </template>
          </v-treeview>
        </v-col>

        <v-divider vertical></v-divider>

        <v-col class="d-flex text-center">
          <v-scroll-y-transition mode="out-in">
            <div
              v-if="!selected"
              class="text-h6 grey--text text--lighten-1 font-weight-light"
              style="align-self: center"
            >
              Select a Commit
            </div>
            <v-card
              v-else
              :key="selected.id"
              class="pt-6 mx-auto"
              flat
              max-width="400"
            >
              <div>
                <v-avatar v-if="selected.author" size="200">
                  <v-img :src="`${selected.author.avatar}`"></v-img>
                </v-avatar>
                <h3 class="text-h5 mb-2">
                  {{ selected.author.username }}
                </h3>
                <div class="blue--text mb-2">
                  {{ selected.message }}
                </div>
                <div class="blue--text subheading font-weight-bold">
                  {{ selected.username }}
                </div>
              </div>
              <v-divider></v-divider>
              <v-row class="text-left" tag="div">
                <v-col class="text-right" cols="12">
                  <div>Date: {{ selected.datetime }}</div>
                </v-col>
                <v-col class="text-right mr-4 mb-2" cols="12">
                  <div v-for="(file, key) in selected.files" v-bind:key="key">
                    <div class="strong">Status: {{ file.status }}</div>
                    <div>Filename: {{ file.filename }}</div>
                    <div class="added">
                      <v-icon> mdi-plus</v-icon>
                      {{ file.additions }}
                    </div>
                    <div class="deleted">
                      <v-icon> mdi-minus</v-icon> {{ file.deletions }}
                    </div>
                    <v-divider vertical></v-divider>
                  </div>
                </v-col>
              </v-row>
            </v-card>
          </v-scroll-y-transition>
        </v-col>
      </v-row>
    </v-card>
  </v-container>
</template>

<script>
import axios from "axios";
import moment from "moment";

export default {
  name: "HistoryComponent",

  data: () => ({
    active: [],
    avatar: null,
    open: [],
    commits: [],
  }),

  computed: {
    items() {
      return [
        {
          name: "Commits",
          children: this.commits,
        },
      ];
    },
    selected() {
      if (!this.active.length) return undefined;
      const id = this.active[0];
      return this.commits.find((commit) => commit.id === id);
    },
  },

  methods: {
    async fetchUsers(item) {
      return axios.get("http://localhost:3000/#").then((response) => {
        this.parseData(item, response.data);
      });
    },
    parseData(item, data) {
      item.children.push(
        ...data.map((value) => {
          value.datetime = moment(value.datetime).format(
            "DD/MM/YYYY h:mm:ss a"
          );
          return value;
        })
      );
    },
  },
};
</script>
