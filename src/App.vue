<template>
  <v-app>
    <v-main>
      <HistoryComponent />
    </v-main>
  </v-app>
</template>

<script>
import HistoryComponent from "./components/HistoryComponent";

export default {
  name: "App",

  components: {
    HistoryComponent,
  },

  data: () => ({
  }),
};
</script>
