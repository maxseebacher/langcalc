<script setup>
import AutoComplete from 'primevue/autocomplete';
import Button from 'primevue/button';
import { languagesRaw } from '@/languages';
import { ref  } from 'vue';
import axios from 'axios';
import Chart from 'primevue/chart';
import VueWorldMap from "vue-world-map";
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import Accordion from 'primevue/accordion';
import AccordionTab from 'primevue/accordiontab';

import 'primevue/resources/themes/lara-light-purple/theme.css'

let counter = 0;
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
    counter++;
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
            countryCodes.value[country.code] = 1
            selectedPeople.value -= country.population;
        } else {
            newList.push(country);
        }
    });
    countries.value = newList;
    chartData.value = setChartData();
    counter--;
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

const countryCodes = ref({max:1,seeb:1e9});

async function obtainLanguageData() {
    await axios.get("https://restcountries.com/v3.1/lang/" + selectedLanguage.value.name).then((response) => {
        response.data.forEach((country) => {
            if (country === undefined) return;
            selectedPeople.value += country.population;
            countries.value.push({ lang: selectedLanguage.value.name, name: country.name.official, population: country.population, code: country.cca2 });
            countryCodes.value[country.cca2] = 1e9;
            
        })
    });
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
const map = ref({
  defaultCountryFillColor: "red",
  highColor: "green",
  lowColor: "red",
  countryStrokeColor: "black",
});
</script>

<template>
    <div class="row">
        <div class="sidebar">
            <h1>Combined Language Calculator</h1>
            <div>
                <h2>Selected languages</h2>
                <AutoComplete placeholder="Enter languages" dropdown v-model="selectedLanguage" optionLabel="name"
                    :suggestions="filteredLanguages" @complete="search"></AutoComplete>
                <Button label="Add" severity="success" @click="addlanguage()" />
                <DataTable :value="selectedLanguages">
                    <Column field="name" header="Name">
                        <template #body="slotProps">
                            {{slotProps.data.name}} <Button label="Remove" severity="danger" @click="removelanguage(slotProps.data.name)" />
                        </template>
                    </Column>
                </DataTable>
            </div>
            <div>
                <h2>Your stats</h2> <br> <br> <br> <br>
                You can speak to {{ (selectedPeople / totalPeople * 100).toFixed(2) }}% of world's population. Congratulations! <br>
                <Accordion :activeIndex="0">
                    <AccordionTab header="Countries">
                        <DataTable :value="countries">
                        <Column field="name" header=""></Column>
                    </DataTable>
                    </AccordionTab>
                </Accordion>
                <div class="card flex justify-content-center">
                    <Chart type="pie" :data="chartData" :options="chartOptions" class="w-full md:w-30rem" />
                </div>

            </div>
        </div>
        <div class="map">
        <vue-world-map id="worldmap" v-bind:countryData="countryCodes" :key="counter"
            v-bind:defaultCountryFillColor="map.defaultCountryFillColor" v-bind:highColor="map.highColor"
            v-bind:countryStrokeColor="map.countryStrokeColor" v-bind:lowColor="map.lowColor"></vue-world-map>
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
h3 {
  margin: 40px 0 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

.map {
  float: left;
  width: 80%;
  height: 800px;
}

.sidebar {
  float: left;
  width: 20%;
}

.row:after {
  content: "";
  display: table;
  clear: both;
}
</style>