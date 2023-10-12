<script setup>
import AutoComplete from 'primevue/autocomplete';
import { ref } from 'vue';
const addedCountries = ref([]);
</script>

<template>
    <h1>Combined Language Calculator</h1>
    <div>
        <h2>Selected countries</h2>

        <AutoComplete placeholder="Missing a country?" v-model="addedCountries" optionLabel="name"
            :suggestions="filteredCountries" @complete="search" multiple></AutoComplete>
    </div>
    <div>
        <h2>Your stats</h2>
    </div>
</template>


<style>
h1 {
    float: left;
}

h2 {
    float: left;
}
</style>