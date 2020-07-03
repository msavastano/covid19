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
        scales: {
          yAxes: [
            {
              id: 'y-axis-0',
              type: 'linear',
              position: 'right',
              display: true
            }
          ]
        }
      }
    })
  }
}
</script>
