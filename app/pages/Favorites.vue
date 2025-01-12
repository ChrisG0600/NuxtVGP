<template>
    <title>SpaceX | Favorites</title>
    <v-container class="content">
        <h1>Your Favorite Launches</h1>
        <v-row v-if="favoriteLaunches.length > 0">
            <v-col cols="12" sm="6" md="4" v-for="launch in favoriteLaunches" :key="launch.id">
                <v-card>
                    <v-card-title>
                        {{ launch.mission_name }}
                        <v-icon :class="favoriteIconClass(launch)" @click="toggleFavorite(launch)"
                            class="cursor-pointer">
                            {{ favoriteIcon(launch) }}
                        </v-icon>
                    </v-card-title>
                    <v-card-subtitle>{{ launch.launch_date_local }}</v-card-subtitle>
                    <v-card-text>
                        <p><strong>Launch Site:</strong> {{ launch.launch_site.site_name }}</p>
                        <p>
                            <strong>Rocket Name:</strong> {{ launch.rocket.rocket_name }}
                            <v-icon small @click="openRocketDetails(launch.rocket)">mdi-information-outline</v-icon>
                        </p>
                        <div>
                            {{ !expandedDetails[launch.mission_name]
                                ? launch.details.slice(0, 100) + (launch.details.length > 100 ? '...' : '')
                                : launch.details }}
                            <span v-if="launch.details.length > 100" @click="toggleDetails(launch.mission_name)"
                                class="text-primary cursor-pointer">
                                {{ expandedDetails[launch.mission_name] ? 'See Less' : 'See More' }}
                            </span>
                        </div>
                    </v-card-text>
                </v-card>
            </v-col>
        </v-row>
        <div v-else>
            <p>No favorite launches yet. Go back to <NuxtLink to="/">Home</NuxtLink> to add some!</p>
        </div>
        <!-- Rocket Modal -->
        <v-dialog v-model="isModalOpen" max-width="600px">
            <v-card>
                <v-card-title>
                    {{ selectedRocket.name }}
                    <v-spacer></v-spacer>
                </v-card-title>
                <v-card-text>
                    <p><strong>Description:</strong> {{ selectedRocket.description }}</p>
                    <p><strong>First Flight:</strong> {{ selectedRocket.first_flight }}</p>
                    <p><strong>Height:</strong> {{ selectedRocket.height.feet }} feet</p>
                    <p><strong>Diameter:</strong> {{ selectedRocket.diameter.feet }} feet</p>
                    <p><strong>Mass:</strong> {{ selectedRocket.mass.kg }} kg</p>
                    <p><strong>Stages:</strong> {{ selectedRocket.stages }}</p>
                </v-card-text>
                <v-card-actions>
                    <v-btn color="primary" text @click="closeModal">Close</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-container>
</template>

<script setup>
import { ref, computed } from 'vue';
import { useFavoritesStore } from '../stores/favoriteStore';

const favoritesStore = useFavoritesStore();

// Make favoriteLaunches reactive and synced with the store
const favoriteLaunches = computed(() => favoritesStore.favoriteLaunches);

// See More Functionality   
const expandedDetails = ref({});
const toggleDetails = (missionName) => {
    expandedDetails.value[missionName] = !expandedDetails.value[missionName];
};

// Rocket Modal
const isModalOpen = ref(false);
const selectedRocket = ref({});
const openRocketDetails = (rocket) => {
    selectedRocket.value = rocket;
    isModalOpen.value = true;
};
const closeModal = () => {
    isModalOpen.value = false;
};

const favoriteIcon = (launch) => {
    return favoriteLaunches.value.some((fav) => fav.id === launch.id)
        ? 'mdi-heart'
        : 'mdi-heart-outline';
};

const favoriteIconClass = (launch) => {
    return favoriteLaunches.value.some((fav) => fav.id === launch.id)
        ? 'text-error'
        : '';
};

// Toggle favorite status for a launch
const toggleFavorite = (launch) => {
    if (favoriteLaunches.value.some((fav) => fav.id === launch.id)) {
        favoritesStore.removeFavorite(launch);
    } else {
        favoritesStore.addFavorite(launch);
    }
};
</script>

<style scoped>
.content {
    margin-top: 5rem;
    padding: 5rem;
}

.text-primary {
    color: #3f51b5;
    cursor: pointer;
}
</style>
