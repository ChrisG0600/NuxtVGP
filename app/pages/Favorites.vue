<template>
    <v-container class="wrapper">
        <div class="icon-container">
            <v-icon class="icon">mdi-wrench</v-icon>
            <div class="description">
                <p>This page is currently under maintenance.</p>
                <p> Return to Home Page
                    <NuxtLink to="/" class="nav-link">Click Me</NuxtLink>
                </p>
            </div>
        </div>
    </v-container>
</template>

<style scoped>
.wrapper {
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
    color: black;
}

.icon-container {
    margin: 10px;
    padding: 15px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    font-size: 24px;
}

.icon {
    margin-bottom: 20px;
    font-size: 10rem;
    color: #282828;
}

.description {
    font-size: 1.5rem;
    margin-bottom: 20px;
    line-height: 3.5rem;
}

.nav-link {
    background-color: #6f42c1;
    padding: 10px 20px;
    color: whitesmoke;
    border-radius: 5px;
    text-decoration: none;
    transition: background-color 0.3s, transform 0.3s;
}

.nav-link:hover {
    background-color: #4b2c82;
    transform: scale(1.1);
}
</style>
