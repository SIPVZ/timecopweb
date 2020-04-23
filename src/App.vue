<template>

  <v-app id="inspire">
  <v-container fluid grid-list-lg>

    <v-toolbar dense app>
      <img v-if="!dark" src="https://raw.githubusercontent.com/SIPVZ/timecop/master/static/static/img/logo.svg" height="70%" class="pa-1" alt="Time Cop">
      <img v-else src="https://raw.githubusercontent.com/SIPVZ/timecop/master/static/static/img/logo_dark.svg" height="70%" class="pa-1" alt="Time Cop">


<v-flex>
              <v-dialog v-model="dialog" persistent max-width="600px">
        <template v-slot:activator="{ on }">
          <v-btn color="primary" dark v-on="on">Set Timecop server</v-btn>
        </template>
        <v-card>
          <v-card-title>
            <span class="headline">Timecop Server Profile</span>
          </v-card-title>
          <v-card-text>
            <v-container>
              <v-row>
                
                <v-col cols="12">
                  <v-text-field label="URL for timecop Server" v-model="timecop_url" required></v-text-field>
                </v-col>
                
              </v-row>
            </v-container>
            <small>*indicates required field</small>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" text @click="dialog = false">Close</v-btn>
            <v-btn color="blue darken-1" text @click="greet($event); dialog = false">Save</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
      </v-flex>

      <v-flex xs3 offset-xs9 >

        <v-btn light target="new" >
          Timecop URL:<input v-model="timecop_url" placeholder="Timecop Server">
        </v-btn>
      </v-flex>

      <v-flex xs3 offset-xs9 >

        <v-btn light target="new" href="https://github.com/BBVA/timecop">
          <img class="mr-2" src="https://raw.githubusercontent.com/SIPVZ/timecop/master/static/static/github.svg" height="26px" alt="github">
          <span>github</span>
        </v-btn>
      </v-flex>
    </v-toolbar>  

    <v-layout v-bind="binding"  row wrap>
      <v-row>
        <v-col>
        <v-flex v-if="selected_ready">

            <v-simple-table>
              <thead >
              <tr><th class="text-left">Algorithm</th><th class="text-left">metric</th></tr>
              </thead>
              <tbody>
                  <tr v-for="(item,index) in mae">
                    <td>{{ index }}</td><td>    {{item}}</td>
                </tr>
              </tbody>
            </v-simple-table>     
        </v-flex>
        </v-col>
      

      
      </v-row>
      
      <v-row>
        <v-flex>

        <img v-if="selected_ready" src="https://truckersagainsttrafficking.org/wp-content/uploads/2018/10/if_advantage_quality_1034364-256x256.png" height="50" class="pa-1" alt="Time Cop">
           
           {{winner}}
        </v-flex>

        <v-flex>
          <v-card dark color="primary">
            <v-card-text v-if="selected_ready">Select Time series to visualize</v-card-text>
          </v-card>
        </v-flex>


        <v-flex>
          <v-card v-if="selected_ready" dark color="secondary">
            <select  v-if="selected_ready" v-model="ts_selected"  v-on:change="changeTS(rowId, $event)"  filled label="Filled style">
              <option v-for="user in info.data" :key="user.name"  v-bind:value="user.name">
                {{user.name}}
              </option>
            </select>
          </v-card>
        </v-flex>
      
     </v-row>
      <v-row>


        <v-col v-if="selected_ready">
      <v-dialog v-model="dialog_json" persistent width="600px">
        <template v-slot:activator="{ on }">
          <v-btn color="primary" dark v-on="on">More info</v-btn>
        </template>
        <v-card>
        <vue-json-pretty
            :path="'res'"
            :data="ts_graph.data"
            :collapsedOnClickBrackets = true
            :deep=2
            :showLength = true 


          >
          </vue-json-pretty>            <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="green darken-1" text @click="dialog_json = false">Close</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
      </v-col>
        
      </v-row>

      <v-divider></v-divider>
      <v-row>
        <v-col >
          <LineTest v-if="loaded" :chart-data="datacollection" :width="2100" :height="800"/>
        </v-col>
        
      </v-row>
    </v-layout>



  </v-container>
  </v-app>

   
</template>

<script>
//import HelloWorld from './components/HelloWorld';
import LineTest from './components/LineTest.vue'
import VueJsonPretty from 'vue-json-pretty'
import axios from 'axios'
import Forecast from './assets/TS.json'



export default {
  name: 'App',

  components: {
      LineTest,
      VueJsonPretty
      },

  data: () => ({
    dialog_json: false,
    dialog: true,
    response: {},
    selected_ready: false,
    toggleDataVisibility: true,
    dark: true,
    reset: false,
    datacollection: {
            labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'],
            datasets: []
            },
    loaded: true,
    ts_graph: {'data':{"hola": "funciona"}},

        mae: {},
        winner: null,
        ts_selected: null,
        
        ts_grah_name : "Real estate loans  billions   monthly  Jan 1973 Oct 1978",

        timecop_url: "Timecop Web server", 
        test: null,
        Forecast,
        labels: null,
        datasets: [],
        values: null,
        title: "hola",
        datos_enviados: '{"data":[27566,27621,25696,21653,21197,21620,25596,28327,29892,28206,28718,44288,29219,29644,32218,29586,22089,28209,33675,25075,32186,27235,29864,49103,29164,29603,32186,24415,19784,24414,27565,27195,34042,37434,35694,48706,31852,30595,34674,27604,22198,26123,28663,27622,40449,29890,28611,52268,33107,31596,38201,36443,23401,25007,30838,28133,40717,30156,31128,53754,35064,35736,39570,38185,24885,31983,31330,31692,41228,35142,36248,58774,36981,36434,41582,33090,27913,30197,32034,33434,41170,34119,35007,54617,39892,41970,41204,46232,31122,31839,40017,37335,46586,40656,43900,61656,41678,41267,46116,44875,32437,32732,41276,40579,49177,42140,44589,69672,46057,49286,51877,41966,33160,34671,44117,45356,51756,46904,48200,78352,53264,51909,58021,56304,39324,43422,49671],"name":"fsdfsdfsd","num_future":5,"desv_metric":2,"train":true,"restart":true}',

        message:"",
        info: null




  }),
    
  methods: {  
      
    greet: function (event) {
      // `this` inside methods point to the Vue instance
      this.datasets=[]
      //alert('Hello ' + event + '!')
      // `event` is the native DOM event
      //alert(event.target.tagName)
      console.log(event.target.tagName)
      this.query_data()
      this.selected_ready=true
      },


    query_data: function() {

        var temp_ts_graph = this.obtain_ts_by_name()
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
            data: miserie,
            pointRadius: 0
            };
        console.log(test)
        this.datasets.push(test)
        this.labels = Array.apply(null, {length:max_length}).map(Number.call, Number)
        }


    this.ts_list()
            this.datacollection= {
            labels: this.labels,
            datasets: this.datasets
            }

      },

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

        'VAR' in this.ts_graph.data.data.status ? this.mae['VAR'] = this.ts_graph.data.data.status.VAR.mae : console.log('No VAR')
        'Holtwinters' in this.ts_graph.data.data.status ? this.mae['Holtwinters'] = this.ts_graph.data.data.status.Holtwinters.mae : console.log('No Holtwinters')
        'arima' in this.ts_graph.data.data.status ? this.mae['arima'] = this.ts_graph.data.data.status.arima.mae : console.log('No Autoarima')
        'LSTM' in this.ts_graph.data.data.status ? this.mae['LSTM'] = this.ts_graph.data.data.status.LSTM.mae : console.log('No LSTM')
        'fbprophet' in this.ts_graph.data.data.status ? this.mae['fbprophet'] = this.ts_graph.data.data.status.fbprophet.mae : console.log('No fbprophet')
        'gluonts' in this.ts_graph.data.data.status ? this.mae['gluonts'] = this.ts_graph.data.data.status.gluonts.mae : console.log('No gluonts')


        //this.mae['VAR'] = 
        //this.mae['Holtwinters'] = this.ts.ts_graph.data.data.status.Holtwinters.mae
        //this.mae['Autoarima'] = this.ts.ts_graph.data.data.status.Autoarima.mae
        //this.mae['LSTM'] = this.ts_graph.data.data.status.LSTM.mae
        //this.mae['VAR'] = this.ts_graph.data.data.status.VAR.mae
        
    
        for (const key in res) {
            // no deberia hacer esto :/
            if (key === 'Holtwinters' || key === 'LSTM' || key === 'VAR' || key === 'arima' || key == 'fbprophet' || key == 'gluonts') {
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
                borderWidth: 6,
                //pointBorderColor: this.getRandomColor(),
                borderColor: this.getRandomColor(),
                pointRadius: 1,
                data: miserie
                };
            if (ts != 'ts') {
                test['borderDash'] = [15,5]
                test['borderWidth'] = 2
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
    toggleData () {
      this.toggleDataVisibility = true
    },
    showResponse (e) {
      this.response = {
        toPredict: e.dataToProcess,
        prediction: e.result
      }
    }
	},

    obtain_data: function () {
        alert(event.target.tagName)
        var temp_ts_graph = this.obtain_ts_by_name()
        console.log(temp_ts_graph)


    
    const main_ts = this.ts_graph.data
    console.log(main_ts)
    const series = {}
    series['ts'] = this.addTS(this.ts_graph.data['ts'], 'ts')
    
    const res = this.ts_graph.data.data.status
    
    for (const key in res) {
        // no deberia hacer esto :/
        if (key === 'Holtwinters' || key === 'LSTM' || key === 'VAR' || key === 'arima' || key == 'fbprophet' || key == 'gluonts') {
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
    
        /*var test = {
            label: ts,
            fill: false,
            backgroundColor: this.getRandomColor(),
            pointBackgroundColor: 'blue',
            borderWidth: 3,
            pointBorderColor: this.getRandomColor(),
            borderColor: this.getRandomColor(),
            data: miserie
            };*/

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
        this.datacollection.datasets.push(test)
        this.datacollection.labels = Array.apply(null, {length:max_length}).map(Number.call, Number)
        }


    this.ts_list()


    },


  mounted () {
    this.$vuetify.theme.dark = true

  },
  
};
</script>
<style>
.column-code {
  height: 85vh;
  overflow-y: scroll;
}
</style>