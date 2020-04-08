<script>
  import { Line, mixins } from 'vue-chartjs'
  const { reactiveProp } = mixins


  export default {
    extends: Line,
    mixins: [reactiveProp],
    //props: ['options'],
    
    data () {
      return {
          options: {
          scales: {
            yAxes: [{
              ticks: {
                beginAtZero: false
              },
              gridLines: {
                display: true
              }
            }],
            xAxes: [ {
              gridLines: {
                display: true
              }
            }]
          },
          legend: {
            display: true
          },
          responsive: true,
          maintainAspectRatio: false
        }
        
        
      }
    },
    mounted () {
      console.log (this.chartData)
      this.renderChart(this.chartData, this.options)
    }
  }
</script>s