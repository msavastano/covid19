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
import { groupBy, remove, sumBy } from 'lodash'
import LineChart from '@/components/LineChart.vue'

export default {
  components: {
    LineChart
  },
  async asyncData(context) {
    const conOld = await axios.get(
      'https://api.covid19api.com/dayone/country/us/status/confirmed'
    )

    const deaOld = await axios.get(
      'https://api.covid19api.com/dayone/country/us/status/deaths'
    )

    const deaNew = remove(deaOld.data, (el) => {
      return el.Date > '2020-03-22T00:00:00Z'
    })

    const conNew = remove(conOld.data, (el) => {
      return el.Date > '2020-03-22T00:00:00Z'
    })

    const gCon = conNew.map((el) => {
      const regex = /^([^,])+/g
      el.Province = el.Province.match(regex)[0]
      return el
    })

    const dCon = deaNew.map((el) => {
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

    const full = conOld.data
      .concat(deaOld.data)
      .concat(aggCon)
      .concat(aggDea)

    return {
      full
    }
  },
  data() {
    return {
      statuses: ['deaths', 'confirmed'],
      status: 'confirmed',
      datacollection: null,
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
    fillData() {
      const d = this.full.filter((el) => {
        return el.Province === this.state && this.status === el.Status
      })
      const casesArr = []
      const datesArr = []
      const pop = this.states.find((st) => {
        return st.name === this.state
      }).pop
      if (d && Array.isArray(d)) {
        d.forEach((element) => {
          const norm = (element.Cases / pop) * 100000
          casesArr.push(norm)
          datesArr.push(element.Date.substr(5, 5))
        })
      }

      this.datacollection = {
        labels: datesArr,
        datasets: [
          {
            backgroundColor: '#add8e6',
            label: `${this.state} / ${this.status} per 100,000 pop`,
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
  max-width: 500px;
  margin: 50px auto;
}
</style>
