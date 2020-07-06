<template>
  <div>
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

          <v-select
            v-model="rollDays"
            outlined
            class="mx-2"
            :items="['1', '3', '7', '14']"
            label="Rolling days"
            @change="submit"
          ></v-select>
        </v-col>
      </v-row>
    </v-container>
    <line-chart :chart-data="datacollection" :options="options"></line-chart>
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
    const deaths = await axios.get(
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
      deaths
    }
  },
  data() {
    return {
      options: {
        maintainAspectRatio: false,
        legend: {
          labels: {
            boxWidth: 15,
            usePointStyle: true
          }
        },
        scales: {
          yAxes: [
            {
              id: 'y-axis-0',
              display: true,
              position: 'right',
              scaleLabel: {
                display: true,
                labelString: 'Deaths'
              }
            },
            {
              id: 'y-axis-1',
              display: true,
              position: 'right',
              scaleLabel: {
                display: true,
                labelString: 'Hospitalizations'
              }
            }
          ]
        }
      },
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
          abbr: 'FL',
          hosp: 'increase'
        },
        {
          name: 'Georgia',
          pop: 10617423,
          abbr: 'GA'
        },
        {
          name: 'Hawaii',
          pop: 1415872,
          abbr: 'HI',
          hosp: 'increase'
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
          name: 'Kansas',
          pop: 2913314,
          abbr: 'KS',
          hosp: 'increase'
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
      testing: [],
      rollDays: '7'
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
    getData(d, DAYS) {
      const casesArr = []
      let newCasesAve
      if (d && Array.isArray(d)) {
        d.forEach((element, i) => {
          if (i > DAYS) {
            let added = 0
            for (let j = 0; j < DAYS; j++) {
              added = added + d[i - j]
            }
            newCasesAve = added / DAYS
          } else {
            newCasesAve = 0
          }
          const norm = Math.round(newCasesAve * 100) / 100
          casesArr.push(norm)
        })
      }
      return {
        casesArr
      }
    },
    submit() {
      this.fillData()
    },
    fillData() {
      const DAYS = parseInt(this.rollDays)
      const abbr = this.states.find((st) => {
        return st.name === this.state
      }).abbr

      const hosp = this.states.find((st) => {
        return st.name === this.state
      }).hosp

      const one = this.deaths.data.filter((el) => {
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

      let prevDeathsIncrease
      const deaths = one
        .map((e, i) => {
          let deathIncrease
          if (
            e.deathIncrease === null ||
            e.deathIncrease === undefined ||
            e.deathIncrease < 0
          ) {
            deathIncrease = prevDeathsIncrease
          } else {
            deathIncrease = e.deathIncrease
            prevDeathsIncrease = e.deathIncrease
          }
          return deathIncrease
        })
        .reverse()
      const rollingDeaths = this.getData(deaths, DAYS)

      let rollingHosps
      if (hosp === 'increase') {
        let prevHospsIncrease
        const hosps = one
          .map((e, i) => {
            let hospitalizedIncrease
            if (
              e.hospitalizedIncrease === null ||
              e.hospitalizedIncrease === undefined ||
              e.hospitalizedIncrease < 0
            ) {
              hospitalizedIncrease = prevHospsIncrease
            } else {
              hospitalizedIncrease = e.hospitalizedIncrease
              prevHospsIncrease = e.hospitalizedIncrease
            }
            return hospitalizedIncrease
          })
          .reverse()
        rollingHosps = this.getData(hosps, DAYS).casesArr
      } else {
        rollingHosps = one
          .map((e, i) => {
            let hospsCurr
            if (e.hospitalizedCurrently === null) {
              hospsCurr = 0
            } else {
              hospsCurr = e.hospitalizedCurrently
            }
            return hospsCurr
          })
          .reverse()
      }

      this.datacollection = {
        labels: days,
        datasets: [
          {
            fillOpacity: 1,
            backgroundColor: `rgba(${this.colors[0][0]}, ${this.colors[0][1]}, ${this.colors[0][2]}, 1)`,
            label: `${this.state} - Deaths`,
            fill: false,
            data: rollingDeaths.casesArr
          },
          {
            fillOpacity: 1,
            yAxisID: 'y-axis-1',
            backgroundColor: `rgba(${this.colors[1][0]}, ${this.colors[1][1]}, ${this.colors[1][2]}, 1)`,
            label: `${this.state} - Hospitalizations - ${hosp || 'current'}`,
            fill: false,
            data: rollingHosps
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
