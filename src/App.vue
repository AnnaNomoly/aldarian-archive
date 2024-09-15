<template>
  <v-app style=
      "background-image: url('./bg_dark.png');
      height: 100%;
      background-position: center;
      background-repeat: no-repeat;
      background-size: cover;"
    >
    <v-app-bar style="flight: right"
      :collapse=true
      dense
      app
      color="dark-grey"
      dark
    >
      <span>Current</span>
      <v-menu
        bottom
        right
      >
        <template v-slot:activator="{ on, attrs }">
          <v-btn
            icon
            color="light-green"
            v-bind="attrs"
            v-on="on"
          >
            <v-icon>mdi-dots-vertical</v-icon>
          </v-btn>
        </template>

        <v-list>
          <v-list-item @click="logCount()">
            <v-list-item-title>Current</v-list-item-title>
          </v-list-item>

          <v-list-item>
            <v-list-item-title>v.0.11.6</v-list-item-title>
          </v-list-item>

          <v-list-item>
            <v-list-item-title>v.0.11.5</v-list-item-title>
          </v-list-item>

          <v-list-item>
            <v-list-item-title>Alpha</v-list-item-title>
          </v-list-item>
        </v-list>
      </v-menu>

    </v-app-bar>

    <v-main>
      <HelloWorld/>
    </v-main>
  </v-app>
</template>

<script>
import HelloWorld from './components/HelloWorld';

export default {
  name: 'App',

  components: {
    HelloWorld,
  },

  data: () => ({
    counter: 0
  }),

  methods: {
    increment: function() {
      this.counter += 1;
    },
    logCount: function() {
      console.log(this.counter);
    }
  },


};
</script>
