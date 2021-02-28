<template>
    <div class="container">
        <h2 class="text-white text-center mb-4">Covid19 Stats APP</h2>
        <div class="form-group">
            <select v-model="selectedCountry" class="form-control" v-if="countries" @change="getDataByCountry">
                <option value="">Global</option>
                <option v-for="country in countries" :value="country.Slug" :key="country.Slug">{{country.Country}}
                </option>
            </select>
        </div>
        <div v-if="data">
            <div class="row justify-content-around">
                <div class="col-md-4">
                    <div class="stats-item btn-info">
                        <span class="item-title">Confirmed</span>
                        <span class="item-count">{{confirmed | numberFormat}}</span>
                    </div>
                </div>
                <div class="col-md-4">
                    <div class="stats-item btn-danger">
                        <span class="item-title">Deaths</span>
                        <span class="item-count">{{deaths | numberFormat}}</span>
                    </div>
                </div>
                <div class="col-md-4">
                    <div class="stats-item btn-success">
                        <span class="item-title">Recovered</span>
                        <span class="item-count">{{recovered | numberFormat}}</span>
                    </div>
                </div>
            </div>
            <hr>

            <div class="message-alert mt-3 text-white text-center">
                <div v-if="isLoading">Yükleniyor...</div>
            </div>
            <component :is="tableType" :data="tableData" :selectCountry="selectedCountry" :isLoading="isLoading"></component>
        </div>
    </div>
</template>

<script>
    import axios from 'axios';
    import CountriesTable from './Components/CountriesTable';
    import CountryTable from './Components/CountryTable';

    export default {
        components: {
            CountriesTable,
            CountryTable
        },
        data() {
            return {
                isLoading : true,
                countries: {},
                selectedCountry: '',
                data: {},
                countryData: {},
                confirmed: null,
                deaths: null,
                recovered: null,
            }
        },
        computed: {

            // ülke seçimi yapılmamışsa, tüm ülkelere ait veri tablosu(CountriesTable) gelir. Seçim yapılmış ise ülkenin verileri gelir (CountryTable)
            tableType() {
                return this.selectedCountry === '' ? 'CountriesTable' : 'CountryTable'
            },

            // ülke seçimi yapılmış ise ülkeye ait verileri gönderir. yok ise global'deki verileri gönderir.
            tableData() {
                return this.selectedCountry !== '' && this.countryData ? this.countryData : this.data;
            }
        },
        methods: {
            getCountries() {
                return axios.get('https://api.covid19api.com/countries')
                    .then(response => {
                        this.countries = response.data;
                    })
            },

            // tüm ülkelerin verisi
            getSummaryData() {
                return axios.get('https://api.covid19api.com/summary')
                    .then(response => {
                        this.isLoading = true;

                        const data = response.data;
                        this.data = data.Countries;
                        this.confirmed = data.Global.TotalConfirmed;
                        this.deaths = data.Global.TotalDeaths;
                        this.recovered = data.Global.TotalRecovered;

                        this.isLoading = false;
                    })
                    .catch(err => {
                        alert(err)
                    })

            },

            // seçilen ülkenin verilerini alır.
            getDataByCountry() {

                let country = this.data.filter(country => {
                    return country.Slug == this.selectedCountry;
                });

                country = country[0];

                this.confirmed = country.TotalConfirmed;
                this.deaths = country.TotalDeaths;
                this.recovered = country.TotalRecovered;

                axios.get('https://api.covid19api.com/dayone/country/' + this.selectedCountry)
                    .then(response => {
                        this.countryData = response.data;
                        console.log(this.countryData)
                    })
            }
        },
        mounted() {
            this.getCountries()
            this.getSummaryData()
        }
    }
</script>

<style>

@import url('https://fonts.googleapis.com/css2?family=Kumbh+Sans:wght@300;400&display=swap');


    * {
        outline: 0;
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }
    body {
        background:#0D1117;
        font-family: 'Kumbh Sans', sans-serif;
    }

    table {
        background: #fff;
    }

    .container {
        margin-top: 40px;
    }

    .stats-item {
        padding: 20px;
        text-align: center;
        margin-bottom: 20px;
    }

    .item-title {
        display: block;
        color: #fff;
        font-size: 17px;
    }

    .item-count {
        font-weight: bold;
        font-size: 30px;
    }
</style>