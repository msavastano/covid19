<template>
  <div class="small">
    <v-container fluid>
      <v-row align="center">
        <v-col class="d-flex" cols="12">
          <v-select
            v-model="state"
            outlined
            class="mx-2"
            :items="StateNames"
            label="State"
            @change="submit"
          ></v-select>
        </v-col>
      </v-row>
    </v-container>
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
    const tests = await axios.get(
      'https://covidtracking.com/api/v1/states/daily.json'
    )
    const colors = []
    for (let i = 0; i < 10; i++) {
      const rbg = []
      for (let j = 0; j < 3; j++) {
        rbg.push(Math.floor(Math.random() * 254 + 1).toString())
      }
      colors.push(rbg)
    }
    return {
      colors,
      tests
    }
  },
  data() {
    return {
      datacollection: { labels: [], datasets: [] },
      states: [
        {
          name: 'Alabama',
          pop: 4903185,
          abbr: 'AL'
        },
        {
          name: 'Alaska',
          pop: 731545,
          abbr: 'AK'
        },
        {
          name: 'Arizona',
          pop: 7278717,
          abbr: 'AZ'
        },
        {
          name: 'Arkansas',
          pop: 3017825,
          abbr: 'AR'
        },
        {
          name: 'California',
          pop: 39512223,
          abbr: 'CA'
        },
        {
          name: 'Colorado',
          pop: 5758736,
          abbr: 'CO'
        },
        {
          name: 'Connecticut',
          pop: 3565287,
          abbr: 'CT'
        },
        {
          name: 'Delaware',
          pop: 973764,
          abbr: 'DE'
        },
        {
          name: 'District of Columbia',
          pop: 705749,
          abbr: 'DC'
        },
        {
          name: 'Florida',
          pop: 21477737,
          abbr: 'FL'
        },
        {
          name: 'Georgia',
          pop: 10617423,
          abbr: 'GA'
        },
        {
          name: 'Hawaii',
          pop: 1415872,
          abbr: 'HI'
        },
        {
          name: 'Idaho',
          pop: 1787147,
          abbr: 'ID'
        },
        {
          name: 'Illinois',
          pop: 12671821,
          abbr: 'IL'
        },
        {
          name: 'Indiana',
          pop: 6732219,
          abbr: 'IN'
        },
        {
          name: 'Iowa',
          pop: 3155070,
          abbr: ''
        },
        {
          name: 'Kansas',
          pop: 2913314,
          abbr: 'KS'
        },
        {
          name: 'Kentucky',
          pop: 4467673,
          abbr: 'KY'
        },
        {
          name: 'Louisiana',
          pop: 4648794,
          abbr: 'LA'
        },
        {
          name: 'Maine',
          pop: 1344212,
          abbr: 'ME'
        },
        {
          name: 'Maryland',
          pop: 6045680,
          abbr: 'MD'
        },
        {
          name: 'Massachusetts',
          pop: 6949503,
          abbr: 'MA'
        },
        {
          name: 'Michigan',
          pop: 9986857,
          abbr: 'MI'
        },
        {
          name: 'Minnesota',
          pop: 5639632,
          abbr: 'MN'
        },
        {
          name: 'Mississippi',
          pop: 2976149,
          abbr: 'MS'
        },
        {
          name: 'Missouri',
          pop: 6137428,
          abbr: 'MO'
        },
        {
          name: 'Montana',
          pop: 1068778,
          abbr: 'MT'
        },
        {
          name: 'Nebraska',
          pop: 1934408,
          abbr: 'NE'
        },
        {
          name: 'Nevada',
          pop: 3080156,
          abbr: 'NV'
        },
        {
          name: 'New Hampshire',
          pop: 1359711,
          abbr: 'NH'
        },
        {
          name: 'New Jersey',
          pop: 8882190,
          abbr: 'NJ'
        },
        {
          name: 'New Mexico',
          pop: 2096829,
          abbr: 'NM'
        },
        {
          name: 'New York',
          pop: 19453561,
          abbr: 'NY'
        },
        {
          name: 'North Carolina',
          pop: 10488084,
          abbr: 'NC'
        },
        {
          name: 'North Dakota',
          pop: 762062,
          abbr: 'ND'
        },
        {
          name: 'Ohio',
          pop: 11689100,
          abbr: 'OH'
        },
        {
          name: 'Oklahoma',
          pop: 3956971,
          abbr: 'OK'
        },
        {
          name: 'Oregon',
          pop: 4217737,
          abbr: 'OR'
        },
        {
          name: 'Pennsylvania',
          pop: 12801989,
          abbr: 'PA'
        },
        {
          name: 'Rhode Island',
          pop: 1059361,
          abbr: 'RI'
        },
        {
          name: 'South Carolina',
          pop: 5148714,
          abbr: 'SC'
        },
        {
          name: 'South Dakota',
          pop: 884659,
          abbr: 'SD'
        },
        {
          name: 'Tennessee',
          pop: 6833174,
          abbr: 'TN'
        },
        {
          name: 'Texas',
          pop: 28995881,
          abbr: 'TX'
        },
        {
          name: 'Utah',
          pop: 3205958,
          abbr: 'UT'
        },
        {
          name: 'Vermont',
          pop: 623989,
          abbr: 'VT'
        },
        {
          name: 'Virginia',
          pop: 8535519,
          abbr: 'VA'
        },
        {
          name: 'Washington',
          pop: 7614893,
          abbr: 'WA'
        },
        {
          name: 'West Virginia',
          pop: 1792065,
          abbr: 'WV'
        },
        {
          name: 'Wisconsin',
          pop: 5822434,
          abbr: 'WI'
        },
        {
          name: 'Wyoming',
          pop: 578759,
          abbr: 'WY'
        }
      ],
      state: 'Arizona',
      rollDays: '7',
      testing: []
    }
  },
  computed: {
    StateNames() {
      return this.states.map((el) => {
        return el.name
      })
    }
  },
  mounted() {
    this.fillData()
  },
  methods: {
    getData(d, abbr, DAYS) {
      return {}
    },
    submit() {
      this.fillData()
    },
    fillData() {
      const DAYS = parseInt(this.rollDays)

      const abbr = this.states.find((st) => {
        return st.name === this.state
      }).abbr

      const one = this.tests.data.filter((el) => {
        return el.state === abbr
      })

      const days = one
        .map((e) => {
          const date = String(e.date).slice(4)
          const dateArr = date.split('')
          dateArr.splice(2, 0, '-')
          return dateArr.join('')
        })
        .reverse()

      let prevTotalTestResultsIncrease
      let prevPositiveIncrease
      const hosp = one
        .map((e, i) => {
          let totalTestResultsIncrease
          if (!e.totalTestResultsIncrease || e.totalTestResultsIncrease < 0) {
            totalTestResultsIncrease = prevTotalTestResultsIncrease
          } else {
            totalTestResultsIncrease = e.totalTestResultsIncrease
            prevTotalTestResultsIncrease = e.totalTestResultsIncrease
          }

          let positiveIncrease
          if (!e.positiveIncrease || e.positiveIncrease < 0) {
            positiveIncrease = prevPositiveIncrease
          } else {
            positiveIncrease = e.positiveIncrease
            prevPositiveIncrease = e.positiveIncrease
          }

          return (
            (Math.round((positiveIncrease / totalTestResultsIncrease) * 1000) /
              1000) *
            100
          )
        })
        .reverse()

      this.datacollection = {
        labels: days,
        datasets: [
          {
            fillOpacity: 0.3,
            backgroundColor: `rgba(${this.colors[1][0]}, ${this.colors[1][1]}, ${this.colors[1][2]}, 0.08)`,
            label: `${this.state} - ${DAYS} day average of positive test rates`,
            data: hosp,
            yAxisID: 'yaxisA'
          },
          {
            fillOpacity: 0.3,
            yAxisID: 'yaxisB',
            backgroundColor: `rgba(${this.colors[1][0]}, ${this.colors[1][1]}, ${this.colors[1][2]}, 0.08)`,
            label: 'B',
            data: [
              0.4,
              1,
              1,
              1,
              0,
              1,
              0.2,
              1,
              1,
              0.6,
              1,
              0.2,
              1,
              1,
              0,
              1,
              1,
              1,
              1,
              0.3,
              1,
              1,
              1,
              1,
              0,
              1,
              1,
              1,
              1,
              0,
              1,
              0.66,
              1,
              1,
              0,
              1,
              1,
              1,
              1,
              0.2,
              1,
              1,
              1,
              1,
              0,
              1,
              1,
              0.2,
              1,
              0,
              1,
              1,
              1,
              1,
              0,
              1,
              1,
              1,
              1,
              0.9,
              1,
              1,
              1,
              1,
              0,
              1,
              1,
              1,
              1,
              0,
              1,
              1,
              0.5,
              1,
              0,
              1,
              1,
              1,
              1,
              0.3,
              1,
              1,
              1,
              1,
              0,
              1,
              1,
              1,
              1,
              0,
              1,
              1,
              1,
              1,
              0.5,
              1,
              0.2,
              1,
              1,
              0.6,
              1,
              0.2,
              1,
              1,
              1,
              0.2,
              1,
              1,
              0.6,
              1,
              0.2,
              1,
              1,
              0.2,
              1,
              1,
              0.6,
              1,
              0.2,
              1,
              1,
              0.2,
              1,
              1,
              0.6,
              1,
              0.2,
              1,
              1
            ]
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
