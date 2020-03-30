<template>
  <div>
    <h1>Global Covid-19</h1>
    <div class="select-rows">
      <div>
        <v-card class="ma-5" max-width="300">
          <v-list-item three-line>
            <v-list-item-content>
              <div class="overline mb-4">{{ country }}</div>
              <v-list-item-title class="headline mb-1">{{
                caseCount
              }}</v-list-item-title>
              <v-list-item-subtitle>{{ status }}</v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
        </v-card>
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
      </div>
      <v-layout justify-center>
        <v-date-picker
          v-model="date"
          class="ma-auto"
          width="290"
          @change="submit"
        ></v-date-picker>
      </v-layout>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  async asyncData(context) {
    const c = await axios.get('https://api.covid19api.com/countries')
    return {
      coun: c.data
    }
  },
  data: () => ({
    date: new Date().toISOString().substr(0, 10),
    statuses: ['deaths', 'confirmed'],
    status: 'confirmed',
    country: 'US',
    caseCount: '-'
  }),
  computed: {
    Countries() {
      return this.coun.map((el) => {
        return el.Country
      })
    },
    CountrySlug() {
      return this.coun.find((el) => {
        return el.Country === this.country
      }).Slug
    }
  },
  methods: {
    async submit() {
      const { data } = await axios.get(
        `https://api.covid19api.com/total/dayone/country/${this.CountrySlug}/status/${this.status}`
      )

      const d = Array.isArray(data)
        ? data.find((el) => el.Date.substr(0, 10) === this.date)
        : null

      this.caseCount = d ? d.Cases : '-'
    }
  }
}
</script>
