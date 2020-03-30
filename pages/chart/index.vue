<template>
  <div class="small">
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
  data() {
    return {
      datacollection: null
    }
  },
  mounted() {
    this.fillData()
  },
  methods: {
    async fillData() {
      const { data } = await axios.get(
        `https://api.covid19api.com/total/dayone/country/italy/status/confirmed`
      )
      const casesArr = []
      const datesArr = []
      let ctry
      if (data && Array.isArray(data)) {
        ctry = data[0].Country
        data.forEach((element) => {
          casesArr.push(element.Cases)
          datesArr.push(element.Date.substr(2, 10))
        })
      }

      this.datacollection = {
        labels: datesArr,
        datasets: [
          {
            label: ctry,
            data: casesArr
          }
        ]
      }
    }
  }
}
</script>

<style>
.small {
  max-width: 600px;
  margin: 150px auto;
}
</style>
