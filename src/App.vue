<template>

  <v-app id="inspire">
  <v-container fluid grid-list-md>

    <v-toolbar dense app>
      <img v-if="!dark" src="https://raw.githubusercontent.com/SIPVZ/timecop/master/static/static/img/logo.svg" height="70%" class="pa-1" alt="Time Cop">
      <img v-else src="https://raw.githubusercontent.com/SIPVZ/timecop/master/static/static/img/logo_dark.svg" height="70%" class="pa-1" alt="Time Cop">






      <v-flex>
      <v-dialog v-model="dialog" persistent max-width="600px">
        <template v-slot:activator="{ on }">
          <v-btn color="primary" dark v-on="on">Timecop server library</v-btn>
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
               <v-row>
                
                <v-col cols="12">
                  <v-text-field label="URL for mongodb backend " v-model="mongodb_url" required></v-text-field>
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

      <v-flex>
      <v-dialog v-model="dialog2" persistent max-width="600px">
        <template v-slot:activator="{ on }">
          <v-btn color="primary" dark v-on="on">Realtime training</v-btn>
        </template>
        <v-card>
          <v-card-title>
            <span class="headline">Timecop CSV Data</span>
          </v-card-title>
          <v-card-text>
            <v-container>
              <v-row>
                
                <v-col cols="12">
                  <v-text-field label="Server" v-model="timecop_url" required></v-text-field>
                </v-col>
                
              </v-row>
              <v-row>
                
                <v-col cols="12">
                  <v-text-field label="Timeseries Name" v-model="ts_data_name" required></v-text-field>
                </v-col>
                
              </v-row>
              <v-row>
                
                <v-col cols="12">
                  <v-text-field label="Data" v-model="ts_data_data" required></v-text-field>
                </v-col>
                
              </v-row>
              <v-row>
                
                <v-col cols="12">
                  <v-text-field label="Num Futures" v-model="ts_data_num_fut" required></v-text-field>
                </v-col>
                
              </v-row>
              <v-row>
                
                <v-col cols="12">
                  <v-text-field label="Desv mse" v-model="ts_data_desv_mse" required></v-text-field>
                </v-col>
                
              </v-row>
               <v-row>
                
                <v-col cols="12">
                  Train: 
                  <v-checkbox
                      v-model="ts_data_train"
                      :label="Train"
                      ></v-checkbox>
                </v-col>
                
              </v-row>
              
               <v-row>
                
                <v-col cols="12">
                  Restart: <v-checkbox
                      v-model="ts_data_restart"
                      :label="Restart"
                      ></v-checkbox>
                </v-col>

              </v-row>
            </v-container>
            <small>*indicates required field</small>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" text @click="dialog2 = false">Close</v-btn>
            <v-btn color="blue darken-1" text @click="timecop_realtime($event); dialog2 = false">Save</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
      </v-flex>


      <v-flex xs3  >

        <v-btn light target="new" >
          Timecop URL:<input v-model="timecop_url" placeholder="Timecop Server">
        </v-btn>
      </v-flex>

 


    </v-toolbar>  

    <v-layout v-bind="binding"  row wrap>

        <v-flex v-if="selected_ready" d-flex xs12 sm6 md3>

            <v-simple-table>
              <thead >
              <tr><th class="text-left">Algorithm</th><th class="text-left">mae</th><th class="text-left">mse</th><th class="text-left">rmse</th></tr>
              </thead>
              <tbody>
                  <tr v-for="item in metrics" :key="item.name">
                    <td>{{ item.name }}</td><td>    {{item.mae.toFixed(3)}}</td> <td>    {{item.mse.toFixed(3)}}</td> <td>    {{item.rmse.toFixed(3)}}</td>
                </tr>
              </tbody>
            </v-simple-table>     
        </v-flex>

        <v-flex  d-flex xs12 sm12 md9>
          <v-layout row wrap justify-center>
            <v-flex v-if="selected_ready"  xs6>
               <v-card dark color="secondary" class="justify-center">
                 <v-card-title primary-title class="justify-center">


                    <img v-if="selected_ready" src="https://truckersagainsttrafficking.org/wp-content/uploads/2018/10/if_advantage_quality_1034364-256x256.png" height="50"  alt="Time Cop">
                    And the winner is... <br>{{winner}}
                 </v-card-title>
                 <v-card-text class="justify-center">
                   
                </v-card-text>

               </v-card>
            </v-flex>

          <v-flex d-flex xs12>

          <v-card v-if="selected_ready" dark color="secondary">
            <v-card dark color="primary">
            <v-card-text v-if="selected_ready">Select Time series to visualize</v-card-text>
          </v-card>
            <select  v-if="selected_ready" v-model="ts_selected"  v-on:change="changeTS(rowId, $event)"  filled label="Filled style">
              <option v-for="user in info.data" :key="user.name"  v-bind:value="user.name">
                {{user.name}}
              </option>
            </select>
          </v-card>
            </v-flex>

          
          <v-flex d-flex xs12>

          <v-dialog v-if="selected_ready" v-model="dialog_json" persistent width="600px">
            <template v-slot:activator="{ on }">
              <div class="text-xs-center">

              <v-btn color="primary" dark v-on="on">More info</v-btn>
              </div>
            </template>
            <br>
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

             </v-flex>





            
            
            
          </v-layout>
        </v-flex>
 

        <v-flex d-flex  xs6 sm2 md11>

          <LineTest v-if="loaded" :chart-data="datacollection" :width="1800" :height="500"/>
        </v-flex>


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
    mongodb_url: '',
    pollInterval: 20000,
    pool_url: '',
    task_info_status: {},
    task_id: '',
    ts_data_sent: {},
    ts_data_name: '',
    ts_data_num_fut: 5,
    ts_data_desv_mse: 2,
    ts_data_restart: true,
    ts_data_train: true,
    ts_data_data: [27566, 27621, 25696, 21653, 21197, 21620, 25596, 28327, 29892, 28206, 28718, 44288, 29219,23345, 25696, 21653, 21197, 21620, 25596, 28327, 29892, 28206, 28718, 44288, 29219,23345, 25696, 21653, 21197, 21620, 25596, 28327, 29892, 28206, 28718, 44288, 29219,23345, 25696, 21653, 21197, 21620, 25596, 28327, 29892, 28206, 28718, 44288, 29219,23345],
    metrics: [],
    dialog_json: false,
    dialog: true,
    dialog2: false,
    response: {},
    selected_ready: false,
    toggleDataVisibility: true,
    dark: true,
    reset: false,
    datacollection: {
            labels: ['1','2','3','4','5','6','7','8'],
            datasets: []
            },
    loaded: true,
    ts_graph: {'data':{" ": " "}},

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
        draw_TS: function () {
        const main_ts = this.ts_graph.data
        console.log(main_ts)
        const series = {}
        series['ts'] = this.addTS(this.ts_graph.data['ts'], 'ts')
    
        const res = this.ts_graph.data.data.status
        this.winner = this.ts_graph.data.data.status.winner

        //this.mae['VAR'] = (this.ts.ts_graph.data.data.status.VAR.mae === undefined) ? 'NA' : this.ts.ts_graph.data.data.status.VAR.mae ;
        
        this.metrics=[]
        console.log('#####################Llega')
        
        if ('VAR' in this.ts_graph.data.data.status) {
          console.log('entra en VAR')
          var  dic_temp={}
          dic_temp['VAR'] = this.ts_graph.data.data.status.VAR.mae
          dic_temp['name']  = 'VAR'
          dic_temp['mae'] = this.ts_graph.data.data.status.VAR.mae
          dic_temp['mse'] = this.ts_graph.data.data.status.VAR.mse
          dic_temp['rmse'] = this.ts_graph.data.data.status.VAR.rmse

          this.metrics.push(dic_temp)
          } 
        if ('Holtwinters' in this.ts_graph.data.data.status) {
          dic_temp={}
          dic_temp['Holtwinters'] = this.ts_graph.data.data.status.Holtwinters.mae
          dic_temp['name']  = 'Holtwinters'
          dic_temp['mae'] = this.ts_graph.data.data.status.Holtwinters.mae
          dic_temp['mse'] = this.ts_graph.data.data.status.Holtwinters.mse
          dic_temp['rmse'] = this.ts_graph.data.data.status.Holtwinters.rmse

          this.metrics.push(dic_temp)

          } 
        if ('arima' in this.ts_graph.data.data.status) {
          dic_temp={}
          dic_temp['arima'] = this.ts_graph.data.data.status.arima.mae
          dic_temp['name']  = 'arima'
          dic_temp['mae'] = this.ts_graph.data.data.status.arima.mae
          dic_temp['mse'] = this.ts_graph.data.data.status.arima.mse
          dic_temp['rmse'] = this.ts_graph.data.data.status.arima.rmse

          this.metrics.push(dic_temp)

          } 
        if ('LSTM' in this.ts_graph.data.data.status) {
          dic_temp={}
          dic_temp['LSTM'] = this.ts_graph.data.data.status.arima.mae
          dic_temp['name']  = 'LSTM'
          dic_temp['mae'] = this.ts_graph.data.data.status.LSTM.mae
          dic_temp['mse'] = this.ts_graph.data.data.status.LSTM.mse
          dic_temp['rmse'] = this.ts_graph.data.data.status.LSTM.rmse

          this.metrics.push(dic_temp)

          } 
        if ('fbprophet' in this.ts_graph.data.data.status) {
          dic_temp={}
          dic_temp['fbprophet'] = this.ts_graph.data.data.status.arima.mae
          dic_temp['name']  = 'fbprophet'
          dic_temp['mae'] = this.ts_graph.data.data.status.fbprophet.mae
          dic_temp['mse'] = this.ts_graph.data.data.status.fbprophet.mse
          dic_temp['rmse'] = this.ts_graph.data.data.status.fbprophet.rmse

          this.metrics.push(dic_temp)

          } 
        if ('gluonts' in this.ts_graph.data.data.status) {
          dic_temp={}
          dic_temp['gluonts'] = this.ts_graph.data.data.status.arima.mae
          dic_temp['name']  = 'gluonts'
          dic_temp['mae'] = this.ts_graph.data.data.status.gluonts.mae
          dic_temp['mse'] = this.ts_graph.data.data.status.gluonts.mse
          dic_temp['rmse'] = this.ts_graph.data.data.status.gluonts.rmse
          
          this.metrics.push(dic_temp)

          } 
        
        
        //'Holtwinters' in this.ts_graph.data.data.status ? this.mae['Holtwinters'] = this.ts_graph.data.data.status.Holtwinters.mae : console.log('No Holtwinters')
        //'arima' in this.ts_graph.data.data.status ? this.mae['arima'] = this.ts_graph.data.data.status.arima.mae : console.log('No Autoarima')
        //'LSTM' in this.ts_graph.data.data.status ? this.mae['LSTM'] = this.ts_graph.data.data.status.LSTM.mae : console.log('No LSTM')
        //'fbprophet' in this.ts_graph.data.data.status ? this.mae['fbprophet'] = this.ts_graph.data.data.status.fbprophet.mae : console.log('No fbprophet')
        //'gluonts' in this.ts_graph.data.data.status ? this.mae['gluonts'] = this.ts_graph.data.data.status.gluonts.mae : console.log('No gluonts')


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
    },


    draw_realtime_TS: function () {
        const main_ts = this.task_info_status.data
        console.log(main_ts)

        this.labels=[]
        this.datasets=[]
        //alert('esta es la serie que dibujo ' + main_ts.name)
        const series = {}
 
        //series['ts'] = this.addTS(this.task_info_status.ts, 'ts')
        var serie_temp = {}
        var indice = 0
        var iterador = this.task_info_status.ts.values();

        for (let valor of iterador) {
            indice = indice + 1
            serie_temp[indice] = valor
          }
        series['ts']=serie_temp
        
        console.log("TSSSSSSSSSSSSSSSS")
        console.log(series['ts'])
        const res = this.task_info_status.status
        this.winner = this.task_info_status.status.winner

        //this.mae['VAR'] = (this.ts.ts_graph.data.data.status.VAR.mae === undefined) ? 'NA' : this.ts.ts_graph.data.data.status.VAR.mae ;
        
        this.metrics=[]
        console.log('#####################Llega')
        console.log(this.task_info_status.status)
        
        if ('VAR' in this.task_info_status.status) {
          console.log('entra en VAR')
          var  dic_temp={}
          dic_temp['VAR'] = res.VAR.mae
          dic_temp['name']  = 'VAR'
          dic_temp['mae'] = res.VAR.mae
          dic_temp['mse'] = res.VAR.mse
          dic_temp['rmse'] = res.VAR.rmse

          this.metrics.push(dic_temp)
          } 
        if ('Holtwinters' in res) {
          dic_temp={}
          dic_temp['Holtwinters'] = res.Holtwinters.mae
          dic_temp['name']  = 'Holtwinters'
          dic_temp['mae'] = res.Holtwinters.mae
          dic_temp['mse'] = res.Holtwinters.mse
          dic_temp['rmse'] = res.Holtwinters.rmse

          this.metrics.push(dic_temp)

          } 
        if ('arima' in res) {
          dic_temp={}
          dic_temp['arima'] = res.arima.mae
          dic_temp['name']  = 'arima'
          dic_temp['mae'] = res.arima.mae
          dic_temp['mse'] = res.arima.mse
          dic_temp['rmse'] = res.arima.rmse

          this.metrics.push(dic_temp)

          } 
        if ('LSTM' in res) {
          dic_temp={}
          dic_temp['LSTM'] = res.LSTM.mae
          dic_temp['name']  = 'LSTM'
          dic_temp['mae'] = res.LSTM.mae
          dic_temp['mse'] = res.LSTM.mse
          dic_temp['rmse'] = res.LSTM.rmse

          this.metrics.push(dic_temp)

          } 
        if ('fbprophet' in res) {
          dic_temp={}
          dic_temp['fbprophet'] = res.fbprophet.mae
          dic_temp['name']  = 'fbprophet'
          dic_temp['mae'] = res.fbprophet.mae
          dic_temp['mse'] = res.fbprophet.mse
          dic_temp['rmse'] = res.fbprophet.rmse

          this.metrics.push(dic_temp)

          } 
        if ('gluonts' in res) {
          dic_temp={}
          dic_temp['gluonts'] = res.gluonts.mae
          dic_temp['name']  = 'gluonts'
          dic_temp['mae'] = res.gluonts.mae
          dic_temp['mse'] = res.gluonts.mse
          dic_temp['rmse'] = res.gluonts.rmse
          
          this.metrics.push(dic_temp)

          }         
    
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
        console.log ('######################series a pintar')
        console.log(series)
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
    },

    timecop_realtime: function (event) {

      // Tenemos que enviar primero los datos para conseguir un ID y luego el pool para saber el estado de la ocnsulta
      this.datasets=[]
      //alert('Hello ' + event + '!')
      // `event` is the native DOM event
      //alert(event.target.tagName)
      console.log(event.target.tagName)
      
      this.ts_data_sent['data'] = [27566, 27621, 25696, 21653, 21197, 21620, 25596, 28327, 29892, 28206, 28718, 44288, 29219,23345, 25696, 21653, 21197, 21620, 25596, 28327, 29892, 28206, 28718, 44288, 29219,23345, 25696, 21653, 21197, 21620, 25596, 28327, 29892, 28206, 28718, 44288, 29219,23345, 25696, 21653, 21197, 21620, 25596, 28327, 29892, 28206, 28718, 44288, 29219,23345]
      this.ts_data_sent['name'] = this.ts_data_name
      this.ts_data_sent['num_future'] = this.ts_data_num_fut
      this.ts_data_sent['desv_metric'] = this.ts_data_desv_mse
      this.ts_data_sent['train'] = this.ts_data_train
      this.ts_data_sent['restart'] = this.ts_data_restart


      axios.post(this.timecop_url+'/back_univariate', this.ts_data_sent, {headers: {'content-type': 'application/json'}})
                .then((response) => {
                    //check if status is completed, if it is stop polling 
                    //if(response.data.chartName = 'completed') {
                    //     clearInterval(this.pollInterval); //won't be polled anymore 
                    //}
                    
                    console.log(response.data)
                    this.task_id = response.data.task_id; 
                    this.check_task_status()
                }).catch(e => {
            console.log(e);
            });

      //this.query_data()
      //this.formatData()
      this.selected_ready=true
      },
    
    check_task_status: function () {
      var v = this;
      var interval = setInterval(function () {
        v.pool_url = v.timecop_url + '/back_univariate_status/'+ v.task_id
        //axios.get(this.timecop_url+'/back_univariate_status/'+ this.task_id)
        axios.get(v.pool_url)
        .then((response) => {
            //check if status is completed, if it is stop polling
            if(response.data.state == 'SUCCESS') {
                 clearInterval(interval); //won't be polled anymore 
            }
            console.log('respuesta ok')
            console.log(response.data.status)
            v.task_info_status['status'] = response.data.status;
            v.task_info_status['ts'] = v.ts_data_data 
            //v.ts_graph = response;
                
                
                //ts_temp = response;
            v.draw_realtime_TS()
         })
      }, this.pollInterval);
    },
    formatData () {

        const d = this.parametersDialog.data
        let dToSent = {}
        if (d.length > 0) {
          if (d.length === 1) {
            dToSent.data = d[0].data
          } else {
            dToSent.main = d[0].data
            dToSent.timeseries = []
            d.map((v, i) => {
              if (i > 1) dToSent.timeseries.push(v)
            })
          }
          this.parametersList.map(v => {
            if (v.value !== '') {
              dToSent[v.key] = v.value
            }
          })
          this.dataToProcess = JSON.stringify(dToSent)
          this.resetParametersDialog()
          this.getUrlToken()
         }
    },
    getUrlToken () {
      this.$emit('reset')
      this.loading = true
      this.$http.post(this.url, this.dataSet).then(response => {
        const newUrl = `${this.url}_status/${response.body.task_id}`
        const interval = setInterval(() => {
          if (this.state === 'SUCCESS' || this.state === 'ERROR') {
            this.loading = false
            this.state = ''
            clearInterval(interval)
          } else {
            this.getUrl(newUrl)
          }
        }, 2000)
      })
    },
    getUrl (url) {
      this.$http.get(url).then(response => {
        console.log(response.body)
        this.state = response.body.state
        const r = response.body.response || response.body.status
        this.$emit('response', {dataToProcess: this.dataSet, result: r})
      }).catch(err => {
        this.loading = false
        this.errorDialog.value = true
        this.errorDialog.text = err
        console.log(err)
        this.state = 'ERROR'
      })
    },
      
    greet: function (event) {
      // `this` inside methods point to the Vue instance
      this.datasets=[]
      // `event` is the native DOM event
      console.log(event.target.tagName)
      this.ts_list()

      //this.query_data()
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


            this.datacollection= {
            labels: this.labels,
            datasets: this.datasets
            }

      },

      changeTS: function(rowId, event) {
        this.datasets=[]
        this.ts_grah_name= event.target.value
        var temp_ts_graph =  this.obtain_ts_by_name()
        console.log(temp_ts_graph)
        
    },


    ts_list: function () {
        var v = this;
        var datos_a_enviar = {}
        datos_a_enviar['collection'] = "timecop"
        datos_a_enviar['url'] = this.mongodb_url
        datos_a_enviar['database'] = 'ts'

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
    obtain_ts_by_name:  function () {
        //var v = this;

        var ts_temp = {}
        var datos_a_enviar = {}
        datos_a_enviar['collection_ts'] = "ts"
        datos_a_enviar['collection_timecop'] = "timecop"
        datos_a_enviar['url'] = this.mongodb_url
        datos_a_enviar['database'] = 'ts'
        datos_a_enviar['name'] = this.ts_grah_name
        console.log ('datos a enviar' + datos_a_enviar)
         axios.post(this.timecop_url+'/result_document' , datos_a_enviar, {headers: {'content-type': 'application/json'}})
            .then((response) => {
                //check if status is completed, if it is stop polling 
                //if(response.data.chartName = 'completed') {
                //     clearInterval(this.pollInterval); //won't be polled anymore 
                //}
                
                
                this.ts_graph = response;
                
                
                //ts_temp = response;
                this.draw_TS()
            }).catch(e => {
                console.log(e);
        });
        
        return ts_temp;
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
                ts[el['step']]=(el['expected value'] || el['value']);
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


    //this.ts_list()


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