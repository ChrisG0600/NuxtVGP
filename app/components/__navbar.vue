<template>
    <v-app-bar class="navbar bg-grey-darken-4">
        <v-toolbar-title>SpaceX Explorer</v-toolbar-title>
        <v-spacer></v-spacer>
        <NuxtLink to="/" class="nav-link">Home</NuxtLink>
        <NuxtLink to="/favorites" class="nav-link">Favorites</NuxtLink>
    </v-app-bar>
</template>

<script setup>
</script>

<style scoped>
.navbar {
    color: white;
    padding: 5px 20px;
}

.nav-link {
    margin: 0 20px;
    color: inherit;
    text-decoration: none;
}

.nav-link:hover {
    text-decoration: underline;
}
</style>
