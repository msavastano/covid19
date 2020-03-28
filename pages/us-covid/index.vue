<template>
  <div>
    <h1>US Covid-19</h1>
    <div class="select-rows">
      <v-select
        v-model="state"
        outlined
        class="mx-2"
        :items="states"
        label="State"
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
    <div class="card-rows">
      <v-date-picker
        v-model="date"
        class="mt-4"
        width="290"
        @change="submit"
      ></v-date-picker>
      <v-card class="mx-auto" max-height="150" outlined>
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
import { groupBy, remove, sumBy } from 'lodash'
export default {
  async asyncData(context) {
    const coun = await axios.get('https://api.covid19api.com/countries')

    const conOld = await axios.get(
      'https://api.covid19api.com/dayone/country/us/status/confirmed'
    )

    const deaOld = await axios.get(
      'https://api.covid19api.com/dayone/country/us/status/deaths'
    )

    const deaNew = remove(deaOld.data, (el) => {
      return el.Date > '2020-03-22T00:00:00Z'
    })

    const conNew = remove(conOld.data, (el) => {
      return el.Date > '2020-03-22T00:00:00Z'
    })

    const gCon = conNew.map((el) => {
      const regex = /^([^,])+/g
      el.Province = el.Province.match(regex)[0]
      return el
    })

    const dCon = deaNew.map((el) => {
      const regex = /^([^,])+/g
      el.Province = el.Province.match(regex)[0]
      return el
    })

    const dateDea = groupBy(dCon, 'Province')

    const dateCon = groupBy(gCon, 'Province')

    const stateCon = Object.keys(dateCon).map((key) => {
      return groupBy(dateCon[key], 'Date')
    })

    const stateDea = Object.keys(dateDea).map((key) => {
      return groupBy(dateDea[key], 'Date')
    })

    const aggCon = stateCon
      .map((state) => {
        let cs
        return Object.keys(state).map((date, i) => {
          cs = sumBy(state[date], (o) => {
            return o.Cases
          })
          return {
            Province: state[date][0].Province,
            Status: 'confirmed',
            Date: state[date][0].Date,
            Cases: cs
          }
        })
      })
      .flat()

    const aggDea = stateDea
      .map((state) => {
        let cs
        return Object.keys(state).map((date, i) => {
          cs = sumBy(state[date], (o) => {
            return o.Cases
          })
          return {
            Province: state[date][0].Province,
            Status: 'deaths',
            Date: state[date][0].Date,
            Cases: cs
          }
        })
      })
      .flat()

    const full = aggCon
      .concat(conOld.data)
      .concat(deaOld.data)
      .concat(aggDea)

    return {
      full,
      coun: coun.data
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
  computed: {
    Provinces() {
      return this.full.map((el) => {
        return el.Province
      })
    }
  },
  methods: {
    submit() {
      // async submit() {
      // const { data } = await axios.get(
      //   `https://api.covid19api.com/dayone/country/us/status/${this.status}`
      // )

      // const d = data.find((el) => {
      //   return el.Date.substr(0, 10) === this.date && el.Province === this.state
      // })

      const d = this.full.find((el) => {
        return (
          el.Date.substr(0, 10) === this.date &&
          el.Province === this.state &&
          this.status === el.Status
        )
      })

      this.caseCount = d ? d.Cases : '-'
    },
    clearCount() {
      this.caseCount = '-'
    }
  }
}
</script>
