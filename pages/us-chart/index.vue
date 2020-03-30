<template>
  <div class="small">
    <v-select
      v-model="state"
      outlined
      class="mx-2"
      :items="states"
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
import { groupBy, remove, sumBy } from 'lodash'
import LineChart from '@/components/LineChart.vue'

export default {
  components: {
    LineChart
  },
  async asyncData(context) {
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

    const full = conOld.data
      .concat(deaOld.data)
      .concat(aggCon)
      .concat(aggDea)

    return {
      full
    }
  },
  data() {
    return {
      statuses: ['deaths', 'confirmed'],
      status: 'confirmed',
      datacollection: null,
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
      state: 'New York'
    }
  },
  computed: {},
  mounted() {
    this.fillData()
  },
  methods: {
    submit() {
      this.fillData()
    },
    fillData() {
      const d = this.full.filter((el) => {
        return el.Province === this.state && this.status === el.Status
      })
      const casesArr = []
      const datesArr = []
      if (d && Array.isArray(d)) {
        d.forEach((element) => {
          casesArr.push(element.Cases)
          datesArr.push(element.Date.substr(2, 10))
        })
      }

      this.datacollection = {
        labels: datesArr,
        datasets: [
          {
            label: `${this.state} / ${this.status}`,
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
