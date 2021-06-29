<template>
  <v-app>
    <v-app-bar app color="primary" dark dense>
      <div class="d-flex align-center"></div>
      印象と実態
      <v-spacer></v-spacer>
    </v-app-bar>
    <v-main>
      <johari-expression />
    </v-main>
  </v-app>
</template>

<script>
import JohariExpression from "./components/JohariExpression";

export default {
  name: "App",

  components: {
    JohariExpression,
  },

  data: () => ({
    //
  }),
};
</script>
