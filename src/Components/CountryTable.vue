<template>
<div>
           
      <h2 class="text-white text-center mb-4" style="text-transform:capitalize">{{selectCountry}} ülkesine ait veriler</h2>
    <table class="table">
        <thead>
        <tr>
            <th scope="col">#</th>
            <th scope="col">Date</th>
            <th scope="col">Confirmed</th>
            <th scope="col">Deaths</th>
            <th scope="col">Recovered</th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="(item, index) in data" :key="index">
            <th scope="row">{{index + 1}}</th>
            <td>{{item.Date | dateFormat}}</td>
            <td>{{item.Confirmed | numberFormat}}</td>
            <td>{{item.Deaths | numberFormat}}</td>
            <td>{{item.Recovered | numberFormat}}</td>
        </tr>
        </tbody>
      
    </table>
    </div>
</template>

<script>
    export default {
        props: ['data','selectCountry']
    }
</script>