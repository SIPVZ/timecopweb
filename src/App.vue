<template>

  <div id="app">
      Timecop URL:<input v-model="timecop_url" placeholder="http://ip172-18-0-8-bpsji37nctv000dlivr0-3000.direct.labs.play-with-docker.com/back_univariate">

        <br> {{winner}}<br>{{mae}}<br>
      <select  v-if="loaded" v-model="ts_selected"  v-on:change="changeTS(rowId, $event)">
        <option v-for="user in info.data" :key="user.name"  v-bind:value="user.name">
                        {{user.name}}
                    </option>
    </select>
    <br>

    <span  v-if="loaded">Selected: {{ ts_selected }}</span>
        <br>

    <p v-if="loaded">El mensaje es: {{ message }}</p>
    <LineTest v-if="loaded" :chart-data="datacollection" :width="1500" :height="600"/>

    
    
  </div>
</template>

<script>
import LineTest from './components/LineTest.vue'
//import tForm from './components/Form.vue'
import axios from 'axios'
import Forecast from './assets/TS.json'
export default {
  name: 'App',
  components: {
    LineTest
  },
  data (){
      
    return {
        mae: {},
        winner: null,
        ts_selected: null,
        response: {},
        ts_graph : null,
        ts_grah_name : "Australia monthly production of cars and station wagons  Sep 1962   Sep 1994",

        timecop_url: "https://ip172-18-0-39-bq6g8oqosm4g00ensul0-443.direct.labs.play-with-docker.comddd/", 
        loaded: false,
        test: null,
        Forecast,
        labels: null,
        datasets: [],
        values: null,
        title: "hola",
        datos_enviados: '{"data":[27566,27621,25696,21653,21197,21620,25596,28327,29892,28206,28718,44288,29219,29644,32218,29586,22089,28209,33675,25075,32186,27235,29864,49103,29164,29603,32186,24415,19784,24414,27565,27195,34042,37434,35694,48706,31852,30595,34674,27604,22198,26123,28663,27622,40449,29890,28611,52268,33107,31596,38201,36443,23401,25007,30838,28133,40717,30156,31128,53754,35064,35736,39570,38185,24885,31983,31330,31692,41228,35142,36248,58774,36981,36434,41582,33090,27913,30197,32034,33434,41170,34119,35007,54617,39892,41970,41204,46232,31122,31839,40017,37335,46586,40656,43900,61656,41678,41267,46116,44875,32437,32732,41276,40579,49177,42140,44589,69672,46057,49286,51877,41966,33160,34671,44117,45356,51756,46904,48200,78352,53264,51909,58021,56304,39324,43422,49671],"name":"fsdfsdfsd","num_future":5,"desv_metric":2,"train":true,"restart":true}',

        message:"",
        info: null,
        datacollection: {
            labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'],
            datasets: []
            }
      
        }
    },
  methods:{

    changeTS: function(rowId, event) {
        this.datasets=[]
        this.ts_grah_name= event.target.value
        //alert(event.target.value)
        var temp_ts_graph =  this.obtain_ts_by_name()
        console.log(temp_ts_graph)


    
        const main_ts = this.ts_graph.data
        console.log(main_ts)
        const series = {}
        series['ts'] = this.addTS(this.ts_graph.data['ts'], 'ts')
    
        const res = this.ts_graph.data.data.status
        this.winner = this.ts_graph.data.data.status.winner

        //this.mae['VAR'] = (this.ts.ts_graph.data.data.status.VAR.mae === undefined) ? 'NA' : this.ts.ts_graph.data.data.status.VAR.mae ;

        //this.mae['VAR'] = 
        //this.mae['Holtwinters'] = this.ts.ts_graph.data.data.status.Holtwinters.mae
        //this.mae['Autoarima'] = this.ts.ts_graph.data.data.status.Autoarima.mae
        //this.mae['LSTM'] = this.ts_graph.data.data.status.LSTM.mae
        
    
        for (const key in res) {
            // no deberia hacer esto :/
            if (key === 'Holtwinters' || key === 'LSTM' || key === 'VAR' || key === 'Autoarima') {
                //tengo que añadir el debug y el futuro

                var nombre_serie = key + '-debug'
                series[nombre_serie] = this.addDebugEngine(res[key], key)
                nombre_serie = key + '-future'
                series[nombre_serie] = this.addFutureEngine(res[key], key)
 
                }
            }
        var max_length = 0;
        for (const ts in series){
            var miserie=[]
            var temp_serie=series[ts]
            var keys = [];

            for (var k in temp_serie) keys.push(k);
        
            var N = Math.max.apply(null, keys);
            if (N> max_length){
                max_length = N;
                } 
            //var valorx = Array.apply(null, {length: N}).map(Number.call, Number)
            for (var i=0; i < N ; i++){
                if (temp_serie[i] !== undefined ) {
                    miserie.push(temp_serie[i]);
                } else {
                    miserie.push(null);
                }
            
            }
    

            var test = {
                label: ts,
                fill: false,
                //backgroundColor: this.getRandomColor(),
                //pointBackgroundColor: 'blue',
                borderWidth: 1,
                //pointBorderColor: this.getRandomColor(),
                borderColor: this.getRandomColor(),
                
                data: miserie
                };
            if (ts != 'ts') {
                test['borderDash'] = [10,5]
                test['borderWidth'] = 3
            }
            console.log(test)
            
            this.datasets.push(test)
            this.labels = Array.apply(null, {length:max_length}).map(Number.call, Number)
            }
        // overwrite
        
        console.log('new labels & datasets')
        console.log(this.labels)
        console.log(this.datasets)
        this.datacollection= {
            labels: this.labels,
            datasets: this.datasets
            }


        console.log('new datacollection')
        console.log(this.datacollection.labels)
        console.log(this.datacollection.datasets)
    },


    ts_list: function () {
        var v = this;
        var datos_a_enviar = '{"collection": "timecop",  "url": "mongodb://webserver:webserver1@ds261570.mlab.com:61570/ts?retryWrites=false", "database": "ts" }'

            axios.post(this.timecop_url+'/result_list', datos_a_enviar, {headers: {'content-type': 'application/json'}})
                .then((response) => {
                    //check if status is completed, if it is stop polling 
                    //if(response.data.chartName = 'completed') {
                    //     clearInterval(this.pollInterval); //won't be polled anymore 
                    //}
                    
                    console.log(response.data)
                    v.info = response; 
                }).catch(e => {
            console.log(e);
            });
        },
    obtain_ts_by_name: async function () {
        var v = this;

        var ts_temp = {}
        var datos_a_enviar = '{"collection_ts": "ts", "collection_timecop": "timecop", "url": "mongodb://webserver:webserver1@ds261570.mlab.com:61570/ts?retryWrites=false", "database": "ts", "name": "'+this.ts_grah_name +'" }'
        console.log ('datos a enviar' + datos_a_enviar)
        await axios.post(this.timecop_url+'/result_document' , datos_a_enviar, {headers: {'content-type': 'application/json'}})
            .then((response) => {
                //check if status is completed, if it is stop polling 
                //if(response.data.chartName = 'completed') {
                //     clearInterval(this.pollInterval); //won't be polled anymore 
                //}
                
                
                v.ts_graph = response; 
                
                
                ts_temp = response;
            }).catch(e => {
                console.log(e);
        });
        
        return ts_temp;
    },
    

    pool_exec: function () {
      var v = this;
      setInterval(function () {
        axios.get('https://api.coindesk.com/v1/bpi/currentprice.json')
        .then((response) => {
            //check if status is completed, if it is stop polling 
            //if(response.data.chartName = 'completed') {
            //     clearInterval(this.pollInterval); //won't be polled anymore 
            //}
            v.info = response; 
         })
    }, 2000);
   },

    testFunction: function () {
      var v = this;
      setInterval(function () {
        
            axios.post(this.timecop_url, this.datos_enviados)
                .then((response) => {
                //check if status is completed, if it is stop polling 
                //if(response.data.chartName = 'completed') {
                //     clearInterval(this.pollInterval); //won't be polled anymore 
                //}
                
                v.info = response; 
            }).catch(e => {
            console.log(e);
        })
    }, 2000);
   },
    addDebugEngine: function (engine, name) {
        var ts = {}
        console.log(name)
        Array.from(engine.debug).forEach(el => {
                ts[el['step']]=(el['expected value']);
                });
        return ts;
    },

    addFutureEngine: function (engine, name) {
        var ts = {}
                console.log(name)

        Array.from(engine.future).forEach(el => {
                ts[el['step']]=(el['value'] || el['valores']);
                });
        return ts;
    },
    addTS: function (engine, name) {
        var ts = {}

        console.log(name)
        
        var indice =0
        Array.from(engine).forEach(el => {
                indice = indice +1 
                ts[indice]=el['value'] 
                });
        console.log(ts)
        return ts;
    },
    getRandomColor: function () {
        var letters = '0123456789ABCDEF';
        var color = '#';
        for (var i = 0; i < 6; i++) {
            color += letters[Math.floor(Math.random() * 16)];
        }
    return color;
    },
   fillData: function  () {
        this.datacollection= {
          labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'],
          datasets: [
            {
              label: this.title,
              //backgroundColor: '#f87979',
              pointBackgroundColor: 'white',
              borderWidth: 1,
              pointBorderColor: '#249EBF',
              data: [40, 40, 20, 50, 90, 10, 20, 40, 50, 70, 90, 100]
            },{
              label: this.title,
              //backgroundColor: '#f87979',
              pointBackgroundColor: 'black',
              borderWidth: 1,
              pointBorderColor: '#249EBF',
              data: [60, 60, 60, 90, 80, 20, 40, 50 ]
            }
          ]
        }

      },
	},
   async mounted () {
    
    this.loaded = false    
    let uri = window.location.search.substring(1); 
    let params = new URLSearchParams(uri);

    //alert(this.$route.query.url) // outputs 'yay'
    this.timecop_url=params.get("url")



    var temp_ts_graph =  await this.obtain_ts_by_name()
    console.log(temp_ts_graph)


    
    const main_ts = this.ts_graph.data
    console.log(main_ts)
    const series = {}
    series['ts'] = this.addTS(this.ts_graph.data['ts'], 'ts')
    
    const res = this.ts_graph.data.data.status
    
    for (const key in res) {
        // no deberia hacer esto :/
        if (key === 'Holtwinters' || key === 'LSTM' || key === 'VAR' || key === 'Autoarima') {
            //tengo que añadir el debug y el futuro

            var nombre_serie = key + '-debug'
            series[nombre_serie] = this.addDebugEngine(res[key], key)
            nombre_serie = key + '-future'
            series[nombre_serie] = this.addFutureEngine(res[key], key)
 
            }
        }
    var max_length = 0;
    for (const ts in series){
        var miserie=[]
        var temp_serie=series[ts]
        var keys = [];

        for (var k in temp_serie) keys.push(k);
        
        var N = Math.max.apply(null, keys);
        if (N> max_length){
            max_length = N;
        } 
        //var valorx = Array.apply(null, {length: N}).map(Number.call, Number)
        for (var i=1; i < N ; i++){
            if (temp_serie[i] !== undefined ) {
                miserie.push(temp_serie[i]);
            } else {
                miserie.push(null);
            }
            
        }
    
        var test = {
            label: ts,
            fill: false,
            backgroundColor: this.getRandomColor(),
            pointBackgroundColor: 'blue',
            borderWidth: 3,
            pointBorderColor: this.getRandomColor(),
            borderColor: this.getRandomColor(),
            data: miserie
            };
        console.log(test)
        this.datacollection.datasets.push(test)
        this.datacollection.labels = Array.apply(null, {length:max_length}).map(Number.call, Number)
        }


    this.ts_list()

    //this.testFunction()


    this.loaded = true
    }
    
}

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
