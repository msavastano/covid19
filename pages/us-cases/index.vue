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
import states from '../../utils/states'
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
      'https://api.covidtracking.com/v1/states/daily.json'
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
        maintainAspectRatio: false,
        legend: {
          labels: {
            boxWidth: 15,
            usePointStyle: true
          }
        }
      },
      datacollection: { labels: [], datasets: [] },
      states,
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
            fillOpacity: 1,
            backgroundColor: `rgba(${this.colors[0][0]}, ${this.colors[0][1]}, ${this.colors[0][2]}, 1)`,
            label: `${this.state} / confirmed - new`,
            fill: false,
            data: casesArrOne.casesArr
          },
          {
            fillOpacity: 1,
            fill: false,
            backgroundColor: `rgba(${this.colors[2][0]}, ${this.colors[2][1]}, ${this.colors[2][2]}, 1)`,
            label: `${this.stateTwo} / confirmed - new`,
            data: casesArrTwo.casesArr
          },
          {
            fillOpacity: 1,
            fill: false,
            backgroundColor: `rgba(${this.colors[4][0]}, ${this.colors[4][1]}, ${this.colors[4][2]}, 1)`,
            label: `${this.stateThree} / confirmed - new`,
            data: casesArrThree.casesArr
          },
          {
            fillOpacity: 1,
            fill: false,
            backgroundColor: `rgba(${this.colors[6][0]}, ${this.colors[6][1]}, ${this.colors[6][2]}, 1)`,
            label: `${this.stateFour} / confirmed - new`,
            data: casesArrFour.casesArr
          },
          {
            fillOpacity: 1,
            fill: false,
            backgroundColor: `rgba(${this.colors[8][0]}, ${this.colors[8][1]}, ${this.colors[8][2]}, 1)`,
            label: `${this.stateFive} / confirmed - new`,
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
