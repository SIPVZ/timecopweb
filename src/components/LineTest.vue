<script>
  import { Line, mixins } from 'vue-chartjs'
  import zoom from 'chartjs-plugin-zoom';

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
            display: true,
            position: "bottom",
            labels: {
              fontSize: 30,
              fontFamily: "sans-serif"
            }
          },
          responsive: true,
          maintainAspectRatio: false,
          pan: {
              // Boolean to enable panning
              enabled: true,

              // Panning directions. Remove the appropriate direction to disable 
              // Eg. 'y' would only allow panning in the y direction
              mode: 'xy'
          },
          // Container for zoom options
          zoom: {
              // Boolean to enable zooming
              enabled: true,

              // Zooming directions. Remove the appropriate direction to disable 
              // Eg. 'y' would only allow zooming in the y direction
              mode: 'xy',
          },
          tooltips: {
					  mode: 'index',
            intersect: false,
            titleFontSize: 25,
            bodyFontSize: 20
				  }

        }
        
        
      }
    },
    mounted () {
      console.log (this.chartData)
      this.addPlugin(zoom);
      this.renderChart(this.chartData, this.options)
    }
  }
</script>s