<script>
  import { Bar, mixins } from 'vue-chartjs'
  const { reactiveProp } = mixins

  export default {
    extends: Bar,
    mixins: [reactiveProp],
    props: ['datos_bar'],
    watch: { 
     title: function(newVal, oldVal) { // watch it
     console.log('Prop changed: ', newVal, ' | was: ', oldVal)
        }
      },
    data () {
      return {
        datacollection:  this.datos_bar
        ,
        options: {
          scales: {
            yAxes: [{
              ticks: {
                beginAtZero: true
              },
              gridLines: {
                display: true
              }
            }],
            xAxes: [ {
              gridLines: {
                display: false
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

        
      this.renderChart(this.chartData, this.options)
    }
  }
</script>s