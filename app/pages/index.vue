<template>
    <title>SpaceX</title>
    <v-container class="wrapper">
        <!--Header, Sort Order and Year Buttons -->
        <h5 class="header">List of SpaceX Mission
            <v-btn @click="toggleYearSortOrder" class="sort-years" outlined>
                Sort Years ({{ yearSortOrder === 'asc' ? 'Ascending' : 'Descending' }})
            </v-btn>
        </h5>
        <!-- Year Buttons -->
        <v-row>
            <v-col cols="12" sm="4" v-for="year in sortedYears" :key="year">
                <v-btn @click="selectYear(year)" class="mb-2" outlined>{{ year }}</v-btn>
            </v-col>
        </v-row>

        <!-- Cards for Selected Year -->
        <v-row v-if="selectedYear">
            <v-col cols="12">
                <h2>Launches for {{ selectedYear }}</h2>
            </v-col>
            <v-col cols="12" sm="6" md="4" v-for="launch in sortedLaunches" :key="launch.mission_name">
                <v-card class="mx-auto launch-card" max-width="400">
                    <v-card-title>
                        {{ launch.mission_name }}
                        <v-icon small :class="favoriteIconClass(launch)" @click="toggleFavorite(launch)">
                            {{ favoriteIcon(launch) }}
                        </v-icon>
                    </v-card-title>
                    <v-card-subtitle>{{ launch.launch_date }}</v-card-subtitle>
                    <v-card-text>
                        <p><strong>Launch Site:</strong> {{ launch.launch_site }}</p>
                        <p>
                            <strong>Rocket Name:</strong>
                            {{ launch.rocket_name }}
                            <v-icon small @click="openRocketDetails(launch.rocket)">mdi-information-outline</v-icon>
                        </p>
                        <div>
                            {{ !expandedDetails[launch.mission_name]
                                ? launch.details.slice(0, 100) + (launch.details.length > 100 ? '...' : '')
                                : launch.details
                            }}
                            <span v-if="launch.details.length > 100" @click="toggleDetails(launch.mission_name)"
                                class="text-primary cursor-pointer">
                                {{ expandedDetails[launch.mission_name] ? 'See Less' : 'See More' }}
                            </span>
                        </div>
                    </v-card-text>
                </v-card>
            </v-col>
        </v-row>

        <!-- Modal for Rocket Details -->
        <v-dialog v-model="isModalOpen" max-width="600px">
            <v-card>
                <v-card-title>
                    {{ selectedRocket.name }}
                    <v-spacer></v-spacer>
                </v-card-title>
                <v-card-text>
                    <p><strong>Description:</strong> {{ selectedRocket.description }}</p>
                    <p><strong>First Flight:</strong> {{ selectedRocket.first_flight }}</p>
                    <p><strong>Height:</strong> {{ selectedRocket.height.meters }} meters ({{ selectedRocket.height.feet
                        }} feet)</p>
                    <p><strong>Diameter:</strong> {{ selectedRocket.diameter.meters }} meters ({{
                        selectedRocket.diameter.feet }} feet)</p>
                    <p><strong>Mass:</strong> {{ selectedRocket.mass.kg }} kg ({{ selectedRocket.mass.lb }} lb)</p>
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
import { ref, computed, watchEffect } from 'vue';
import { gql } from 'graphql-tag';
import { useQuery } from '@vue/apollo-composable';
import { useFavoritesStore } from '../stores/favoriteStore';

const launches = ref([]);
const expandedDetails = ref({});
const selectedYear = ref(null);
const yearSortOrder = ref('asc');
const sortOrder = ref('asc');
const isModalOpen = ref(false);
const selectedRocket = ref({});



// Use Pinia store
const favoritesStore = useFavoritesStore();

const toggleDetails = (missionName) => {
    expandedDetails.value[missionName] = !expandedDetails.value[missionName];
};

const GET_LAUNCHES = gql`
  query GetLaunches {
    launchesPast(limit: 50) {
      id
      mission_name
      launch_date_utc
      launch_site {
        site_name_long
      }
      rocket {
        rocket_name
        rocket {
          name
          description
          first_flight
          height {
            meters
            feet
          }
          diameter {
            meters
            feet
          }
          mass {
            kg
            lb
          }
          stages
        }
      }
      details
    }
  }
`;

const { result } = useQuery(GET_LAUNCHES);

const convertUTCToLocalDate = (utcDate) => {
    if (!utcDate) return 'N/A';
    const localDate = new Date(utcDate);
    return localDate.toLocaleDateString();
};

watchEffect(() => {
    if (result.value && result.value.launchesPast) {
        launches.value = result.value.launchesPast.map((launch) => ({
            id: launch.id,
            mission_name: launch.mission_name || 'N/A',
            launch_date: convertUTCToLocalDate(launch.launch_date_utc),
            launch_site: launch.launch_site?.site_name_long || 'N/A',
            rocket_name: launch.rocket?.rocket_name || 'N/A',
            rocket: launch.rocket?.rocket || {},
            details: launch.details || 'No details available',
        }));
    }
});

const uniqueYears = computed(() => {
    const years = launches.value.map(launch => new Date(launch.launch_date).getFullYear());
    return [...new Set(years)];
});

const sortedYears = computed(() => {
    const sorted = [...uniqueYears.value];
    sorted.sort((a, b) => {
        if (yearSortOrder.value === 'asc') {
            return a - b;
        } else {
            return b - a;
        }
    });
    return sorted;
});

const filteredLaunches = computed(() => {
    if (!selectedYear.value) {
        return launches.value;
    }
    return launches.value.filter((launch) =>
        new Date(launch.launch_date).getFullYear() === selectedYear.value
    );
});

const sortedLaunches = computed(() => {
    const sorted = [...filteredLaunches.value];
    sorted.sort((a, b) => {
        if (sortOrder.value === 'asc') {
            return new Date(a.launch_date) - new Date(b.launch_date);
        } else {
            return new Date(b.launch_date) - new Date(a.launch_date);
        }
    });
    return sorted;
});

const toggleYearSortOrder = () => {
    yearSortOrder.value = yearSortOrder.value === 'asc' ? 'desc' : 'asc';
};

const openRocketDetails = (rocket) => {
    selectedRocket.value = rocket;
    isModalOpen.value = true;
};

const closeModal = () => {
    isModalOpen.value = false;
};

const selectYear = (year) => {
    selectedYear.value = year;
};

const toggleFavorite = (launch) => {
    const isFavorite = favoritesStore.favoriteLaunches.some(fav => fav.id === launch.id);
    if (isFavorite) {
        favoritesStore.removeFavorite(launch);
    } else {
        favoritesStore.addFavorite(launch);
    }
};

const favoriteIconClass = (launch) => {
    return favoritesStore.favoriteLaunches.some(fav => fav.id === launch.id) ? 'text-error' : 'grey--text';
};

const favoriteIcon = (launch) => {
    return favoritesStore.favoriteLaunches.some(fav => fav.id === launch.id) ? 'mdi-heart' : 'mdi-heart-outline';
};
</script>


<style scoped>
.wrapper {
    margin:8rem 10% 8.5rem;
}

.header{
    font-size: 24px;
    margin:1rem 0;
}

.sort-years{
    margin-left: 35%;
}

.launch-card {
    box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px;
}

.grey--text {
    color: #9e9e9e;
}
</style>
