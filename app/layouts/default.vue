<template>
    <v-app>
        <Navbar />
        <RouterView class="wrapper" /> <!-- This will render the matched route component -->
        <Footer /> <!-- Ensure Footer is placed inside v-app -->
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
