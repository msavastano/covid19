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
          pop: 4903185
        },
        {
          name: 'Alaska',
          pop: 731545
        },
        {
          name: 'Arizona',
          pop: 7278717
        },
        {
          name: 'Arkansas',
          pop: 3017825
        },
        {
          name: 'California',
          pop: 39512223
        },
        {
          name: 'Colorado',
          pop: 5758736
        },
        {
          name: 'Connecticut',
          pop: 3565287
        },
        {
          name: 'Delaware',
          pop: 973764
        },
        {
          name: 'District of Columbia',
          pop: 705749
        },
        {
          name: 'Florida',
          pop: 21477737
        },
        {
          name: 'Georgia',
          pop: 10617423
        },
        {
          name: 'Hawaii',
          pop: 1415872
        },
        {
          name: 'Idaho',
          pop: 1787147
        },
        {
          name: 'Illinois',
          pop: 12671821
        },
        {
          name: 'Indiana',
          pop: 6732219
        },
        {
          name: 'Iowa',
          pop: 3155070
        },
        {
          name: 'Kansas',
          pop: 2913314
        },
        {
          name: 'Kentucky',
          pop: 4467673
        },
        {
          name: 'Louisiana',
          pop: 4648794
        },
        {
          name: 'Maine',
          pop: 1344212
        },
        {
          name: 'Maryland',
          pop: 6045680
        },
        {
          name: 'Massachusetts',
          pop: 6949503
        },
        {
          name: 'Michigan',
          pop: 9986857
        },
        {
          name: 'Minnesota',
          pop: 5639632
        },
        {
          name: 'Mississippi',
          pop: 2976149
        },
        {
          name: 'Missouri',
          pop: 6137428
        },
        {
          name: 'Montana',
          pop: 1068778
        },
        {
          name: 'Nebraska',
          pop: 1934408
        },
        {
          name: 'Nevada',
          pop: 3080156
        },
        {
          name: 'New Hampshire',
          pop: 1359711
        },
        {
          name: 'New Jersey',
          pop: 8882190
        },
        {
          name: 'New Mexico',
          pop: 2096829
        },
        {
          name: 'New York',
          pop: 19453561
        },
        {
          name: 'North Carolina',
          pop: 10488084
        },
        {
          name: 'North Dakota',
          pop: 762062
        },
        {
          name: 'Ohio',
          pop: 11689100
        },
        {
          name: 'Oklahoma',
          pop: 3956971
        },
        {
          name: 'Oregon',
          pop: 4217737
        },
        {
          name: 'Pennsylvania',
          pop: 12801989
        },
        {
          name: 'Rhode Island',
          pop: 1059361
        },
        {
          name: 'South Carolina',
          pop: 5148714
        },
        {
          name: 'South Dakota',
          pop: 884659
        },
        {
          name: 'Tennessee',
          pop: 6833174
        },
        {
          name: 'Texas',
          pop: 28995881
        },
        {
          name: 'Utah',
          pop: 3205958
        },
        {
          name: 'Vermont',
          pop: 623989
        },
        {
          name: 'Virginia',
          pop: 8535519
        },
        {
          name: 'Washington',
          pop: 7614893
        },
        {
          name: 'West Virginia',
          pop: 1792065
        },
        {
          name: 'Wisconsin',
          pop: 5822434
        },
        {
          name: 'Wyoming',
          pop: 578759
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
          const prev = i - 1
          if (i !== 0) {
            element.newCases = element.Cases - d[prev].Cases
          } else {
            element.newCases = 0
          }

          if (i > DAYS) {
            let added = 0
            for (let j = 1; j < DAYS + 1; j++) {
              added = added + d[i - j].newCases
            }
            element.newCasesAve = added / DAYS
          } else {
            element.newCasesAve = 0
          }
          const norm = isNaN(perPop)
            ? Math.round(element.newCasesAve)
            : Math.round((element.newCasesAve / pop) * perPop * 100) / 100
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
    fillData() {
      const DAYS = parseInt(this.rollDays)
      const one = this.full.filter((el) => {
        return el.Province === this.state && el.Date > '2020-02-15-T00:00:00Z'
      })

      const two = this.full.filter((el) => {
        return (
          el.Province === this.stateTwo && el.Date > '2020-02-15-T00:00:00Z'
        )
      })

      const three = this.full.filter((el) => {
        return (
          el.Province === this.stateThree && el.Date > '2020-02-15-T00:00:00Z'
        )
      })

      const four = this.full.filter((el) => {
        return (
          el.Province === this.stateFour && el.Date > '2020-02-15-T00:00:00Z'
        )
      })

      const five = this.full.filter((el) => {
        return (
          el.Province === this.stateFive && el.Date > '2020-02-15-T00:00:00Z'
        )
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
