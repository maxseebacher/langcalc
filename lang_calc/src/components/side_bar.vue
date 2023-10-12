<script setup>
import AutoComplete from 'primevue/autocomplete';
import Button from 'primevue/button';
import {countriesRaw} from '@/countries';
import {ref} from 'vue';

const countries = ref();
const filteredCountries = ref();
const selectedCountry = ref();
const selectedCountries = ref([]);
Promise.resolve(countriesRaw).then((data) => (countries.value = data))
const search = (event) => {
    setTimeout(() => {
        if (!event.query.trim().length) {
            filteredCountries.value = [...countries.value];
        } else {
            filteredCountries.value = countries.value.filter((country) => {
                return country.name.toLowerCase().startsWith(event.query.toLowerCase());
            });
        }
    }, 50);
}

function addCountry(){
    selectedCountries.value.push({name:selectedCountry.value.name});
}
function removeCountry(name){
    const newList = [];
    selectedCountries.value.forEach((data) => {
        if(data.name !== name) newList.push(data);
    })
    selectedCountries.value = newList;
}


</script>

<template>
    <h1>Combined Language Calculator</h1>
    <div>
        <h2>Selected countries</h2>
        <AutoComplete placeholder="Enter countries" v-model="selectedCountry" optionLabel="name" :suggestions="filteredCountries" @complete="search" ></AutoComplete>
        <Button label="Add" severity="success" @click="addCountry()"/>
        <li v-for="country in selectedCountries" :key="country">
            {{ country.name }} <Button label="Remove" severity="danger" @click="removeCountry(country.name)"/>
        </li>
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