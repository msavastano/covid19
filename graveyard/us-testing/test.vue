<template>
  <div class="small">
    <v-container fluid>
      <v-row align="center">
        <v-col class="d-flex" cols="12">
          <v-select
            v-model="stateTwo"
            outlined
            class="mx-2"
            :items="StateNames"
            label="State"
            @change="submit"
          ></v-select>
          <v-select
            v-model="stateThree"
            outlined
            class="mx-2"
            :items="StateNames"
            label="State"
            @change="submit"
          ></v-select>
          <v-select
            v-model="stateFour"
            outlined
            class="mx-2"
            :items="StateNames"
            label="State"
            @change="submit"
          ></v-select>
        </v-col>
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
            v-model="stateFive"
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
import { groupBy, sumBy } from 'lodash'
import LineChart from '@/components/LineChart.vue'

export default {
  components: {
    LineChart
  },
  async asyncData(context) {
    const colors = []
    for (let i = 0; i < 10; i++) {
      const rbg = []
      for (let j = 0; j < 3; j++) {
        rbg.push(Math.floor(Math.random() * 254 + 1).toString())
      }
      colors.push(rbg)
    }
    const conNew = await axios.get(
      'https://api.covid19api.com/dayone/country/us/status/confirmed'
    )

    const dateCon = groupBy(conNew.data, 'Province')

    const stateCon = Object.keys(dateCon).map((key) => {
      return groupBy(dateCon[key], 'Date')
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

    const full = aggCon

    return {
      full,
      colors
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
      state: 'New York',
      stateTwo: 'New Jersey',
      stateThree: 'Rhode Island',
      stateFour: 'Massachusetts',
      stateFive: 'Connecticut',
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
      const casesArr = []
      const datesArr = []
      if (d && Array.isArray(d)) {
        d.forEach((element, i) => {
          const tests = this.testing.data.find((t) => {
            return (
              t.dateChecked > '2020-04-10-T00:00:00Z' &&
              t.dateChecked.substr(0, 10) === element.Date.substr(0, 10) &&
              t.state === abbr
            )
          })
          if (i !== 0) {
            element.newCases = element.Cases - d[i - 1].Cases
          } else {
            element.newCases = 0
          }
          let testsAddedAve = 0
          if (i >= 0) {
            let added = 0
            let testsAdded = 0
            for (let j = 0; j < DAYS && i - j >= 0; j++) {
              added = added + d[i - j].newCases
              testsAdded = tests
                ? testsAdded + this.testing.data[i - j].totalTestResultsIncrease
                : 0
            }
            const denom = i + 1 < DAYS ? i + 1 : DAYS
            element.newCasesAve = added / denom
            testsAddedAve = testsAdded / denom
          } else {
            element.newCasesAve = 0
          }
          console.log('test', testsAddedAve)
          console.log('case', element.newCasesAve)
          const norm =
            Math.round((testsAddedAve / element.newCasesAve) * 100) / 100
          casesArr.push(norm)
          datesArr.push(element.Date.substr(5, 5))
        })
      }
      return {
        casesArr,
        datesArr
      }
    },
    submit() {
      this.fillData()
    },
    async fillData() {
      this.testing = await axios.get(
        'https://covidtracking.com/api/v1/states/daily.json'
      )
      console.log(this.testing[300])
      const DAYS = parseInt(this.rollDays)
      const one = this.full.filter((el) => {
        return (
          el.Province === this.state &&
          el.Status === 'confirmed' &&
          el.Date > '2020-04-10-T00:00:00Z'
        )
      })

      const two = this.full.filter((el) => {
        return (
          el.Province === this.stateTwo &&
          el.Status === 'confirmed' &&
          el.Date > '2020-04-10-T00:00:00Z'
        )
      })

      const three = this.full.filter((el) => {
        return (
          el.Province === this.stateThree &&
          el.Status === 'confirmed' &&
          el.Date > '2020-04-10-T00:00:00Z'
        )
      })

      const four = this.full.filter((el) => {
        return (
          el.Province === this.stateFour &&
          el.Status === 'confirmed' &&
          el.Date > '2020-04-10-T00:00:00Z'
        )
      })

      const five = this.full.filter((el) => {
        return (
          el.Province === this.stateFive &&
          el.Status === 'confirmed' &&
          el.Date > '2020-04-10-T00:00:00Z'
        )
      })

      const abbr = this.states.find((st) => {
        return st.name === this.state
      }).abbr
      const abbrTwo = this.states.find((st) => {
        return st.name === this.stateTwo
      }).abbr
      const abbrThree = this.states.find((st) => {
        return st.name === this.stateThree
      }).abbr
      const abbrFour = this.states.find((st) => {
        return st.name === this.stateFour
      }).abbr
      const abbrFive = this.states.find((st) => {
        return st.name === this.stateFive
      }).abbr

      const casesArrOne = this.getData(one, abbr, DAYS)
      const casesArrTwo = this.getData(two, abbrTwo, DAYS)
      const casesArrThree = this.getData(three, abbrThree, DAYS)
      const casesArrFour = this.getData(four, abbrFour, DAYS)
      const casesArrFive = this.getData(five, abbrFive, DAYS)

      const container = []

      container.push(
        casesArrOne,
        casesArrTwo,
        casesArrThree,
        casesArrFour,
        casesArrFive
      )

      container.sort(function(a, b) {
        return b.casesArr.length - a.casesArr.length
      })

      const days = container[0].datesArr

      this.datacollection = {
        labels: days,
        datasets: [
          {
            backgroundColor: `rgba(${this.colors[1][0]}, ${this.colors[1][1]}, ${this.colors[1][2]}, 0.08)`,
            label: `${this.state} / tests : cases - ${DAYS} day average`,
            data: casesArrOne.casesArr
          },
          {
            fillOpacity: 0.3,
            backgroundColor: `rgba(${this.colors[3][0]}, ${this.colors[3][1]}, ${this.colors[3][2]}, 0.08)`,
            label: `${this.stateTwo} / tests : cases - ${DAYS} day average`,
            data: casesArrTwo.casesArr
          },
          {
            fillOpacity: 0.3,
            backgroundColor: `rgba(${this.colors[5][0]}, ${this.colors[5][1]}, ${this.colors[5][2]}, 0.08)`,
            label: `${this.stateThree} / tests : cases  - ${DAYS} day average`,
            data: casesArrThree.casesArr
          },
          {
            fillOpacity: 0.3,
            backgroundColor: `rgba(${this.colors[7][0]}, ${this.colors[7][1]}, ${this.colors[7][2]}, 0.08)`,
            label: `${this.stateFour} / tests : cases - ${DAYS} day average`,
            data: casesArrFour.casesArr
          },
          {
            fillOpacity: 0.3,
            backgroundColor: `rgba(${this.colors[9][0]}, ${this.colors[9][1]}, ${this.colors[9][2]}, 0.08)`,
            label: `${this.stateFive} / tests : cases - ${DAYS} day average`,
            data: casesArrFive.casesArr
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
