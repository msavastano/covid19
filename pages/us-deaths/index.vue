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

          <v-select
            v-model="hospDataType"
            outlined
            class="mx-2"
            :items="['Daily Increase', 'Currently Hospitalized']"
            label="Hospitalization Data Type"
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
import states from '../../utils/states'
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
              gridLines: {
                drawOnChartArea: false
              },
              scaleLabel: {
                display: true,
                labelString: 'Hospitalizations'
              }
            }
          ]
        }
      },
      datacollection: { labels: [], datasets: [] },
      states,
      state: 'Arizona',
      testing: [],
      rollDays: '7',
      hospDataType: 'Currently Hospitalized'
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
      if (this.hospDataType === 'Daily Increase') {
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
            label: `${this.state} - Deaths - new`,
            fill: false,
            data: rollingDeaths.casesArr
          },
          {
            fillOpacity: 1,
            yAxisID: 'y-axis-1',
            backgroundColor: `rgba(${this.colors[1][0]}, ${this.colors[1][1]}, ${this.colors[1][2]}, 1)`,
            label: `${this.state} - Hospitalizations - ${this.hospDataType}`,
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
