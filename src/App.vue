<script setup>
import CharityPerEdition from './components/CharityPerEdition.vue';
</script>

<template>
  <v-container>
    <header>
      <v-row>
        <v-col>
          <img
            align="center"
            alt="Callisto Network"
            class="logo"
            src="./assets/logo.png"
            height="50"
          />
        </v-col>
      </v-row>
    </header>

    <main>
      <div>
        <v-card class="pa-6 card rounded-xl">
          <CharityPerEdition />
        </v-card>
      </div>
    </main>

    <footer align="center">
      All rights reserved by Callisto Network © 2020
    </footer>
  </v-container>
</template>
