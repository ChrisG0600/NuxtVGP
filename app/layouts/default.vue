<template>
    <v-app>
        <Navbar />
        <RouterView class="wrapper" />
        <Footer />
    </v-app>
</template>

<script setup lang="ts">
    import Navbar from '~/components/__navbar.vue';
    import Footer from '~/components/__footer.vue';

</script>

<style scoped>
.wrapper {
    min-height: 100vh;
}
</style>
