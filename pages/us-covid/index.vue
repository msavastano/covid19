<template>
  <div>
    <h1>US Covid-19</h1>
    <div class="select-rows">
      <v-select
        v-model="state"
        outlined
        class="comp"
        :items="states"
        label="State"
        @change="submit"
      ></v-select>
      <v-select
        v-model="status"
        outlined
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
            <div class="overline mb-4">{{ state }} {{ status }}</div>
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
    states: [
      'Alabama',
      'Alaska',
      'American Samoa',
      'Arizona',
      'Arkansas',
      'California',
      'Colorado',
      'Connecticut',
      'Delaware',
      'District of Columbia',
      'Federated States of Micronesia',
      'Florida',
      'Georgia',
      'Guam',
      'Hawaii',
      'Idaho',
      'Illinois',
      'Indiana',
      'Iowa',
      'Kansas',
      'Kentucky',
      'Louisiana',
      'Maine',
      'Marshall Islands',
      'Maryland',
      'Massachusetts',
      'Michigan',
      'Minnesota',
      'Mississippi',
      'Missouri',
      'Montana',
      'Nebraska',
      'Nevada',
      'New Hampshire',
      'New Jersey',
      'New Mexico',
      'New York',
      'North Carolina',
      'North Dakota',
      'Northern Mariana Islands',
      'Ohio',
      'Oklahoma',
      'Oregon',
      'Palau',
      'Pennsylvania',
      'Puerto Rico',
      'Rhode Island',
      'South Carolina',
      'South Dakota',
      'Tennessee',
      'Texas',
      'Utah',
      'Vermont',
      'Virgin Island',
      'Virginia',
      'Washington',
      'West Virginia',
      'Wisconsin',
      'Wyoming'
    ],
    state: 'New York',
    caseCount: '-'
  }),
  methods: {
    async submit() {
      const { data } = await axios.get(
        `https://api.covid19api.com/dayone/country/us/status/${this.status}`
      )

      const d = data.find((el) => {
        return el.Date.substr(0, 10) === this.date && el.Province === this.state
      })

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
