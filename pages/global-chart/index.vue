<template>
  <div class="small">
    <v-select
      v-model="country"
      outlined
      class="mx-2"
      :items="Countries"
      label="Country"
      @change="submit"
    ></v-select>
    <v-select
      v-model="status"
      outlined
      class="mx-2"
      :items="statuses"
      label="Status"
      @change="submit"
    ></v-select>
    <line-chart :chart-data="datacollection"></line-chart>
    <button @click="fillData()">Line Chart</button>
  </div>
</template>

<script>
import axios from 'axios'
import LineChart from '@/components/LineChart.vue'

export default {
  components: {
    LineChart
  },
  async asyncData(context) {
    const c = await axios.get('https://api.covid19api.com/countries')
    return {
      coun: c.data
    }
  },
  data() {
    return {
      statuses: ['deaths', 'confirmed'],
      status: 'confirmed',
      datacollection: { labels: [], datasets: [] },
      country: 'United States of America'
    }
  },
  computed: {
    Countries() {
      return this.coun
        .map((el) => {
          return el.Country
        })
        .sort()
    },
    CountrySlug() {
      return this.coun.find((el) => {
        return el.Country === this.country
      }).Slug
    }
  },
  mounted() {
    this.fillData()
  },
  methods: {
    submit() {
      this.fillData()
    },
    async fillData() {
      const { data } = await axios.get(
        `https://api.covid19api.com/total/dayone/country/${this.CountrySlug}/status/${this.status}`
      )
      let label = `${this.country} / ${this.status} per 1,000,000 pop`
      let pop
      try {
        pop = await axios.get(
          `https://restcountries.eu/rest/v2/name/${this.country.trim()}?fullText=true`
        )
      } catch (err) {
        pop = null
        label = `Missing population data for ${this.country} / ${this.status}`
      }
      const casesArr = []
      const datesArr = []
      if (data && Array.isArray(data)) {
        data.forEach((element) => {
          const norm = pop
            ? Math.round((element.Cases / pop.data[0].population) * 1000000)
            : element.Cases
          casesArr.push(norm)
          datesArr.push(element.Date.substr(5, 5))
        })
      }

      this.datacollection = {
        labels: datesArr,
        datasets: [
          {
            backgroundColor:
              this.status === 'confirmed' ? '#add8e6' : '#ffcccb',
            label,
            data: casesArr
          }
        ]
      }
    }
  }
}
</script>

<style>
.small {
  max-width: 500px;
  margin: 50px auto;
}
</style>
