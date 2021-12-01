<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-logo-dark.png"
          transition="scale-transition"
          width="40"
        />

        <v-text
        
        class="shrink mt-1 hidden-sm-and-down"
        >Calculadora SAC/PRICE</v-text>

        
      </div>

      <v-spacer></v-spacer>

      <v-btn
        href="https://github.com/AlexGenuino/CalculadoraPriceAndSac"
        target="_blank"
        text
      >
        <span class="mr-2">GitHub</span>
        <v-icon>gh-ico</v-icon>
        <mdiGithub/>
      </v-btn>
      <mdiGithub/>
    </v-app-bar>

    <v-main>
      <Home/>
    </v-main>
  </v-app>
</template>

<script>
import Home from './components/Home';
import { mdiGithub } from '@mdi/js';

export default {
  name: 'App',

  components: {
    Home,
    mdiGithub
  },

  data: () => ({
    //
  }),
};
</script>
