<template>
  <div class="small">
    <v-select
      v-model="state"
      outlined
      class="mx-2"
      :items="StateNames"
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
import { groupBy, sumBy } from 'lodash'
import LineChart from '@/components/LineChart.vue'

export default {
  components: {
    LineChart
  },
  async asyncData(context) {
    const conNew = await axios.get(
      'https://api.covid19api.com/dayone/country/us/status/confirmed'
    )

    const deaNew = await axios.get(
      'https://api.covid19api.com/dayone/country/us/status/deaths'
    )

    const gCon = conNew.data.map((el) => {
      const regex = /^([^,])+/g
      el.Province = el.Province.match(regex)[0]
      return el
    })

    const dCon = deaNew.data.map((el) => {
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

    let aggCon = stateCon.map((state) => {
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

    aggCon = [].concat(...aggCon)

    let aggDea = stateDea.map((state) => {
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

    aggDea = [].concat(...aggDea)

    const full = aggCon.concat(aggDea)

    return {
      full
    }
  },
  data() {
    return {
      statuses: ['deaths', 'confirmed'],
      status: 'confirmed',
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
      state: 'New York'
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
    submit() {
      this.fillData()
    },
    async fillData() {
      const d = this.full.filter((el) => {
        return el.Province === this.state && this.status === el.Status
      })
      // const casesArr = []
      const datesArr = []
      const pop = this.states.find((st) => {
        return st.name === this.state
      }).pop

      const abbr = this.states.find((st) => {
        return st.name === this.state
      }).abbr

      const testing = await axios.get(
        'https://covidtracking.com/api/v1/states/daily.json'
      )

      const tData = []

      if (d && Array.isArray(d)) {
        d.forEach((element) => {
          const tests = testing.data.find((t) => {
            return (
              t.dateChecked.substr(0, 10) === element.Date.substr(0, 10) &&
              t.state === abbr
            )
          })
          const tot = tests ? tests.total : 0
          const normCases = (element.Cases / pop) * 100000
          const normTests = Math.round((tot / pop) * 100000 * 100) / 100
          const tt = Math.round((normTests / normCases) * 100) / 100
          // const norm = Math.round(normCases * 100) / 100
          tData.push(tt)
          // casesArr.push(norm)
          datesArr.push(element.Date.substr(5, 5))
        })
      }

      this.datacollection = {
        labels: datesArr,
        datasets: [
          // {
          //   backgroundColor:
          //     this.status === 'confirmed' ? '#add8e6' : '#ffcccb',
          //   label: `${this.state} / ${this.status} per 100,000 pop`,
          //   data: casesArr
          // },
          {
            backgroundColor: '#ffcaaa',
            label: `Tests / Cases ratio normalized 100,000 pop`,
            data: tData
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
