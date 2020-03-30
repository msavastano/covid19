<script>
import { Line, mixins } from 'vue-chartjs'
const { reactiveProp } = mixins
export default {
  extends: Line,
  mixins: [reactiveProp],
  mounted() {
    this.renderChart({
      datasets: this.chartData,
      options: {
        responsive: true,
        maintainAspectRatio: false
      }
    })
  }
}
</script>
