<template>
  <div>
    <h1>Covid-19</h1>
    <div class="select-rows">
      <v-select
        v-model="country"
        class="comp"
        :items="Countries"
        label="Country"
        @change="submit"
      ></v-select>
      <v-select
        v-model="status"
        class="comp"
        :items="statuses"
        label="Status"
        @change="submit"
      ></v-select>
    </div>
    <div class="card-rows">
      <v-date-picker
        v-model="date"
        class="mt-4 comp"
        :light="true"
        width="290"
        @change="submit"
      ></v-date-picker>
      <v-card class="mx-auto comp" max-height="150" outlined>
        <v-list-item three-line>
          <v-list-item-content>
            <div class="overline mb-4">{{ country }} {{ status }}</div>
            <v-list-item-title class="headline mb-1"
              >{{ caseCount }}
            </v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-card>
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

      const d = data.find((el) => el.Date.substr(0, 10) === this.date)

      this.caseCount = d ? d.Cases : '-'
    },
    clearCount() {
      this.caseCount = '-'
    }
  }
}
</script>

<style scoped>
.select-rows {
  display: flex;
  flex-flow: row wrap;
  justify-content: center;
  align-items: center;
}

.card-rows {
  display: flex;
  flex-flow: row wrap;
}

.comp {
  margin: 10px;
}
</style>
