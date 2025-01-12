<template>
    <v-app>
        <Navbar />
        <RouterView class="content_space" />
        <Footer />
    </v-app>
</template>

<script setup lang="ts">
    import Navbar from '~/components/__navbar.vue';
    import Footer from '~/components/__footer.vue';

</script>

<style scoped>
.content_space{
    margin:5rem 0;
}
</style>
