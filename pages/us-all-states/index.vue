<template>
  <div>
    <v-container fluid>
      <v-row align="center">
        <v-col class="d-flex" cols="12">
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

    const dateGr = groupBy(conNew.data, 'date')

    let aggDea = Object.keys(dateGr).map((gr) => {
      const cs = sumBy(dateGr[gr], (o) => {
        return o.deathIncrease
      })
      const dateString = String(gr).slice(4)
      let Date = dateString.split('')
      Date.splice(2, 0, '-')
      Date = Date.join('')
      return {
        Status: 'confirmed',
        Cases: cs,
        Date
      }
    })

    aggDea = [].concat(...aggDea)

    let aggCon = Object.keys(dateGr).map((gr) => {
      const cs = sumBy(dateGr[gr], (o) => {
        return o.positiveIncrease
      })
      const dateString = String(gr).slice(4)
      let Date = dateString.split('')
      Date.splice(2, 0, '-')
      Date = Date.join('')
      return {
        Status: 'confirmed',
        Cases: cs,
        Date
      }
    })
    aggCon = [].concat(...aggCon)

    return {
      aggCon,
      aggDea,
      colors
    }
  },
  data() {
    return {
      rollDays: '7',
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
              position: 'left',
              scaleLabel: {
                display: true,
                labelString: 'New Confirmed'
              }
            },
            {
              id: 'y-axis-1',
              display: true,
              gridLines: {
                drawOnChartArea: false
              },
              position: 'right',
              scaleLabel: {
                display: true,
                labelString: 'New Deaths'
              }
            }
          ]
        }
      },
      datacollection: { labels: [], datasets: [] }
    }
  },
  mounted() {
    this.fillData()
  },
  methods: {
    getData(d, DAYS) {
      const casesArr = []
      const datesArr = []
      let newCasesAve
      if (d && Array.isArray(d)) {
        d.forEach((element, i) => {
          if (element.Date === '06-25' && element.Cases === 2501) {
            element.Cases = 724
          }
          if (i > DAYS) {
            let added = 0
            for (let j = 0; j < DAYS; j++) {
              if (d[i - j].Date === '06-25' && d[i - j].Cases === 2501) {
                d[i - j].Cases = 724
              }
              added = added + d[i - j].Cases
            }
            newCasesAve = added / DAYS
          } else {
            newCasesAve = 0
          }
          const norm = Math.round(newCasesAve * 100) / 100
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
      const confirmed = this.getData(this.aggCon, DAYS)
      const deaths = this.getData(this.aggDea, DAYS)

      this.datacollection = {
        labels: confirmed.datesArr,
        datasets: [
          {
            fillOpacity: 1,
            backgroundColor: `rgba(${this.colors[0][0]}, ${this.colors[0][1]}, ${this.colors[0][2]}, 1)`,
            label: `US / confirmed - new`,
            fill: false,
            data: confirmed.casesArr,
            yAxisID: 'y-axis-0'
          },
          {
            fillOpacity: 1,
            fill: false,
            backgroundColor: `rgba(${this.colors[2][0]}, ${this.colors[2][1]}, ${this.colors[2][2]}, 1)`,
            label: `US / deaths - new`,
            yAxisID: 'y-axis-1',
            data: deaths.casesArr
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
