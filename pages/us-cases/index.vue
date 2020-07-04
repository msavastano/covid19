<template>
  <div>
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
        <v-col class="d-flex" cols="12">
          <v-select
            v-model="perPop"
            outlined
            class="mx-2"
            :items="['1,000,000', 'Full']"
            label="Per Population"
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
      'https://covidtracking.com/api/v1/states/daily.json'
    )

    const dateCon = groupBy(conNew.data, 'state')

    const stateCon = Object.keys(dateCon).map((key) => {
      return groupBy(dateCon[key], 'date')
    })

    let aggCon = stateCon.map((state) => {
      let cs
      return Object.keys(state).map((date, i) => {
        cs = sumBy(state[date], (o) => {
          return o.positiveIncrease
        })
        const dateString = String(state[date][0].date).slice(4)
        let Date = dateString.split('')
        Date.splice(2, 0, '-')
        Date = Date.join('')
        return {
          state: state[date][0].state,
          Status: 'confirmed',
          Date,
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
      options: {
        maintainAspectRatio: false
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
      state: 'California',
      stateTwo: 'Texas',
      stateThree: 'South Carolina',
      stateFour: 'Florida',
      stateFive: 'Arizona',
      perPop: '1,000,000',
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
    getData(d, pop, DAYS) {
      const perPop = parseFloat(this.perPop.replace(/,/g, ''))
      const casesArr = []
      const datesArr = []
      if (d && Array.isArray(d)) {
        d.forEach((element, i) => {
          if (i > DAYS) {
            let added = 0
            for (let j = 0; j < DAYS; j++) {
              added = added + d[i - j].Cases
            }
            element.newCasesAve = added / DAYS
          } else {
            element.newCasesAve = 0
          }
          const norm = isNaN(perPop)
            ? Math.round(element.newCasesAve)
            : Math.round((element.newCasesAve / pop) * perPop * 100) / 100
          casesArr.push(norm)
          datesArr.push(element.Date)
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
    fillData() {
      const DAYS = parseInt(this.rollDays)

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

      const one = this.full.filter((el) => {
        return el.state === abbr
      })

      const two = this.full.filter((el) => {
        return el.state === abbrTwo
      })

      const three = this.full.filter((el) => {
        return el.state === abbrThree
      })

      const four = this.full.filter((el) => {
        return el.state === abbrFour
      })

      const five = this.full.filter((el) => {
        return el.state === abbrFive
      })

      const pop = this.states.find((st) => {
        return st.name === this.state
      }).pop
      const popTwo = this.states.find((st) => {
        return st.name === this.stateTwo
      }).pop
      const popThree = this.states.find((st) => {
        return st.name === this.stateThree
      }).pop
      const popFour = this.states.find((st) => {
        return st.name === this.stateFour
      }).pop
      const popFive = this.states.find((st) => {
        return st.name === this.stateFive
      }).pop

      const casesArrOne = this.getData(one, pop, DAYS)
      const casesArrTwo = this.getData(two, popTwo, DAYS)
      const casesArrThree = this.getData(three, popThree, DAYS)
      const casesArrFour = this.getData(four, popFour, DAYS)
      const casesArrFive = this.getData(five, popFive, DAYS)

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
      for (let k = 1; k < 5; k++) {
        const diff = container[0].casesArr.length - container[k].casesArr.length
        for (let l = 0; l < diff; l++) {
          container[k].casesArr.unshift(0)
        }
      }

      container.forEach((el) => {
        for (let g = 0; g < DAYS * 1.5; g++) {
          el.datesArr.shift()
        }
        for (let g = 0; g < DAYS * 1.5; g++) {
          el.casesArr.shift()
        }
      })

      this.datacollection = {
        labels: days,
        datasets: [
          {
            backgroundColor: `rgba(${this.colors[0][0]}, ${this.colors[0][1]}, ${this.colors[0][2]}, 0.08)`,
            label: `${this.state} / confirmed`,
            data: casesArrOne.casesArr
          },
          {
            fillOpacity: 0.3,
            backgroundColor: `rgba(${this.colors[2][0]}, ${this.colors[2][1]}, ${this.colors[2][2]}, 0.08)`,
            label: `${this.stateTwo} / confirmed`,
            data: casesArrTwo.casesArr
          },
          {
            fillOpacity: 0.3,
            backgroundColor: `rgba(${this.colors[4][0]}, ${this.colors[4][1]}, ${this.colors[4][2]}, 0.08)`,
            label: `${this.stateThree} / confirmed`,
            data: casesArrThree.casesArr
          },
          {
            fillOpacity: 0.3,
            backgroundColor: `rgba(${this.colors[6][0]}, ${this.colors[6][1]}, ${this.colors[6][2]}, 0.08)`,
            label: `${this.stateFour} / confirmed`,
            data: casesArrFour.casesArr
          },
          {
            fillOpacity: 0.3,
            backgroundColor: `rgba(${this.colors[8][0]}, ${this.colors[8][1]}, ${this.colors[8][2]}, 0.08)`,
            label: `${this.stateFive} / confirmed`,
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
