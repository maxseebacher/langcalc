<script setup>
import AutoComplete from 'primevue/autocomplete';
import Button from 'primevue/button';
import { languagesRaw } from '@/languages';
import { ref } from 'vue';
import axios from 'axios';
import Chart from 'primevue/chart';


const languages = ref();
const filteredLanguages = ref();
const selectedLanguage = ref();
const selectedLanguages = ref([]);
Promise.resolve(languagesRaw).then((data) => (languages.value = data))

const search = (event) => {
    setTimeout(() => {
        if (!event.query.trim().length) {
            filteredLanguages.value = [...languages.value];
        } else {
            filteredLanguages.value = languages.value.filter((language) => {
                return language.name.toLowerCase().startsWith(event.query.toLowerCase());
            });
        }
    }, 50);
}

async function addlanguage() {
    if (selectedLanguage.value.name.length == 0) return;
    selectedLanguages.value.push({ name: selectedLanguage.value.name });
    await obtainLanguageData();
    chartData.value = setChartData();
}
function removelanguage(name) {
    let newList = [];
    selectedLanguages.value.forEach((data) => {
        if (data.name !== name) newList.push(data);
    })
    selectedLanguages.value = newList;
    newList = [];
    countries.value.forEach((country) => {
        if (country.lang === name) {
            selectedPeople.value -= country.population;
        } else {
            newList.push(country);
        }
    });
    countries.value = newList;
    chartData.value = setChartData();
}
const countries = ref([]);
const selectedPeople = ref(0);
const totalPeople = ref(7888000000);

const chartData = ref();
const setChartData = () => {
    return {
        labels: ['Spoken', 'Not spoken'],
        datasets: [
            {
                data: [selectedPeople.value / totalPeople.value * 100, 100 - (selectedPeople.value / totalPeople.value * 100)],
                backgroundColor: ["green", "red"],
                hoverBackgroundColor: ["lightgreen", "#FFCCCB"]
            }
        ]
    };
};

async function obtainLanguageData() {
    await axios.get("https://restcountries.com/v3.1/lang/" + selectedLanguage.value.name).then((response) => {
        response.data.forEach((country) => {
            if (country === undefined) return;
            selectedPeople.value += country.population;
            countries.value.push({ lang: selectedLanguage.value.name, name: country.name.official, population: country.population });
        })
    })
}

const chartOptions = ref({
    plugins: {
        legend: {
            labels: {
                usePointStyle: true
            }
        }
    }
});
</script>

<template>
    <h1>Combined Language Calculator</h1>
    <div>
        <h2>Selected languages</h2>
        <AutoComplete placeholder="Enter languages" v-model="selectedLanguage" optionLabel="name"
            :suggestions="filteredLanguages" @complete="search"></AutoComplete>
        <Button label="Add" severity="success" @click="addlanguage()" />
        <li v-for="language in selectedLanguages" :key="language">
            {{ language.name }} <Button label="Remove" severity="danger" @click="removelanguage(language.name)" />
        </li>
    </div>
    <div>
        <h2>Your stats</h2><br>
        You can speak to {{ selectedPeople / totalPeople * 100 }}% of world's population. Congratulations! <br>
        <li v-for="country in countries" :key="country">
            {{ country.name }}
        </li>
        <div class="card flex justify-content-center">
            <Chart type="pie" :data="chartData" :options="chartOptions" class="w-full md:w-30rem" />
        </div>

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