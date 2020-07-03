<script>
import { Line, mixins } from 'vue-chartjs'
const { reactiveProp } = mixins
export default {
  extends: Line,
  mixins: [reactiveProp],
  props: {
    options: {
      type: Object,
      default() {
        return {}
      }
    },
    chartData: {
      type: Object,
      default() {
        return {}
      }
    }
  },
  mounted() {
    this.renderChart({
      datasets: this.chartData
    })
  }
}
</script>
