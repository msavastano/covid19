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
import states from '../../utils/states'
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
      tests: tests.data
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
                labelString: 'Positive Test %'
              }
            },
            {
              id: 'y-axis-2',
              display: true,
              gridLines: {
                drawOnChartArea: false
              },
              position: 'left',
              scaleLabel: {
                display: true,
                labelString: 'Confirmed Cases'
              }
            },
            {
              id: 'y-axis-3',
              display: true,
              gridLines: {
                drawOnChartArea: false
              },
              position: 'right',
              scaleLabel: {
                display: true,
                labelString: 'Total Tests'
              }
            }
          ]
        }
      },
      datacollection: { labels: [], datasets: [] },
      states,
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

      const one = this.tests.filter((el) => {
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
      const posTestRate = one
        .map((e, i) => {
          let totalTestResultsIncrease
          if (
            e.totalTestResultsIncrease === null ||
            e.totalTestResultsIncrease === undefined ||
            e.totalTestResultsIncrease <= 0
          ) {
            totalTestResultsIncrease = prevTotalTestResultsIncrease
          } else {
            totalTestResultsIncrease = e.totalTestResultsIncrease
            prevTotalTestResultsIncrease = e.totalTestResultsIncrease
          }

          let positiveIncrease
          if (
            e.positiveIncrease === null ||
            e.positiveIncrease === undefined ||
            e.positiveIncrease < 0
          ) {
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
      const rollingPosTestRate = this.getData(posTestRate, DAYS)

      let prevPositiveIncrease2
      const posTest = one
        .map((e, i) => {
          let positiveIncrease
          if (
            e.positiveIncrease === null ||
            e.positiveIncrease === undefined ||
            e.positiveIncrease < 0
          ) {
            positiveIncrease = prevPositiveIncrease2
          } else {
            positiveIncrease = e.positiveIncrease
            prevPositiveIncrease2 = e.positiveIncrease
          }
          return positiveIncrease
        })
        .reverse()
      const rollingPosTest = this.getData(posTest, DAYS)

      let prevTotalTestResultsIncrease2
      const totalTest = one
        .map((e, i) => {
          let totalTestResultsIncrease
          if (
            e.totalTestResultsIncrease === null ||
            e.totalTestResultsIncrease === undefined ||
            e.totalTestResultsIncrease < 0
          ) {
            totalTestResultsIncrease = prevTotalTestResultsIncrease2
          } else {
            totalTestResultsIncrease = e.totalTestResultsIncrease
            prevTotalTestResultsIncrease2 = e.totalTestResultsIncrease
          }
          return totalTestResultsIncrease
        })
        .reverse()
      const rollingTotalTest = this.getData(totalTest, DAYS)

      this.datacollection = {
        labels: days,
        datasets: [
          {
            fillOpacity: 1,
            fill: false,
            backgroundColor: `rgba(${this.colors[1][0]}, ${this.colors[1][1]}, ${this.colors[1][2]}, 1)`,
            label: `${this.state} - Positive test %`,
            data: rollingPosTestRate.casesArr
          },
          {
            fillOpacity: 1,
            fill: false,
            backgroundColor: `rgba(${this.colors[2][0]}, ${this.colors[2][1]}, ${this.colors[2][2]}, 1)`,
            label: `${this.state} - Confirmed Cases - new`,
            yAxisID: 'y-axis-2',
            data: rollingPosTest.casesArr
          },
          {
            fillOpacity: 1,
            fill: false,
            backgroundColor: `rgba(${this.colors[3][0]}, ${this.colors[3][1]}, ${this.colors[3][2]}, 1)`,
            label: `${this.state} - Total Tests - new`,
            yAxisID: 'y-axis-3',
            data: rollingTotalTest.casesArr
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
