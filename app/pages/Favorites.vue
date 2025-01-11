<template>
  <v-container class="wrapper">
    <v-row>
      <v-col cols="12">
        <h2>Your Favorite Launches</h2>
      </v-col>
      <v-col cols="12" sm="6" md="4" v-for="launch in favoriteLaunches" :key="launch.id">
        <v-card class="mx-auto launch-card" max-width="400">
          <v-card-title>
            {{ launch.mission_name }}
            <v-icon small @click="removeFavorite(launch)" class="red--text">mdi-heart</v-icon>
          </v-card-title>
          <v-card-subtitle>{{ launch.launch_date }}</v-card-subtitle>
          <v-card-text>
            <p><strong>Launch Site:</strong> {{ launch.launch_site }}</p>
            <p><strong>Rocket Name:</strong> {{ launch.rocket_name }}</p>
            <p>{{ launch.details.slice(0, 100) }}{{ launch.details.length > 100 ? '...' : '' }}</p>
          </v-card-text>
        </v-card>
      </v-col>
      <v-col cols="12" v-if="favoriteLaunches.length === 0">
        <p>No favorites added yet. Browse launches and add your favorites!</p>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup>
import { useFavoritesStore } from '../stores/favoriteStore';

const favoritesStore = useFavoritesStore();

// Computed property to fetch favorite launches
const favoriteLaunches = favoritesStore.favoriteLaunches;

// Method to remove a favorite launch
const removeFavorite = (launch) => {
    favoritesStore.removeFavorite(launch);
};
</script>

<style scoped>
.wrapper {
    margin-top: 25px;
    margin-bottom: 20px;
}
</style>
