<template>
  <v-row justify="center">

    <v-dialog
      v-model="dialog"
      max-width="550"
      persistent
      
    >
      <v-card>
        <v-card-title style="font-size:15px">{{stationNm[itemIndex]}} 도착 예정 버스
            <v-spacer></v-spacer>
            <v-icon @click="close">mdi-close</v-icon>
        </v-card-title>
        <!-- <v-card-subtitle class="pb-0">{{routeNum[itemIndex]}}</v-card-subtitle> -->
        <v-divider></v-divider>
      
        <div class="modal" style="max-height:300px; overflow-y:auto">

          <div v-for="(item, index) in routeNum" :key="index">
          <router-link style="text-decoration:none; color:black" :to="{ name: 'BusDetail', params: {routeId:routeId[index], routeNm:routeNm[index]}}">
            <div class="left">
              <v-icon class="ml-6" color="#0475f4" size="18">mdi-bus-clock</v-icon>
                <span style="font-size:14px; font-weight:700;padding-left:5px">{{via[index]}}</span>
                <span style="font-size:14px; font-weight:500;padding-left:5px;color:grey">{{item}}번</span>
            </div>
            
            <div class="right" v-if="predictTm[index]!=null">
             <span style="padding-right:10px; color:#0475f4;font-size:14px" v-if="predictTm[index]!=0">{{predictTm[index]}}분</span>
             <span style="font-size:14px" v-if="predictTm[index]!=0">{{remainStation[index]}}정류장 전</span>
             <span style="font-size:14px;color:red" v-else>잠시후도착</span>
            </div>
            
            <div class="right" v-else-if="predictTm[index]==null">
             <span style="font-size:14px">운행정보없음</span>
            </div>
          </router-link>
          </div>
          
        </div>
      
      </v-card>
    </v-dialog>
  </v-row>
</template>
<script>
import {EventBus} from ".././eventbus.js"
import axios from 'axios'
import Vue from 'vue'

  export default {
      props:[
          'stationId',
          'itemIndex',
          'stationNm'
      ],
    data () {
      return {
          dialog:true,
          via:[],
          routeNum:[],
          postPlateNo:[],
          predictTm:[],
          remainStation:[],
          routeId:[],
          routeNm:[],

      }
    },
    
    mounted(){
        console.log('BusStopModal.vue mounted')
        axios
            .get('https://cors-anywhere.herokuapp.com/'
            +'http://bus.andong.go.kr:8080/api/facilities/station/getBusArriveData?stationId='+this.stationId,)
            .then(res => {
            console.log(res)
            console.log('해당 정류소의 도착정보 api를 가져온다.')
            this.via=[]
            this.routeNum=[]
            this.postPlateNo=[]
            this.predictTm=[]
            this.remainStation=[]
            for(let i = 0; i < res.data.results.length; i++)
            {
                Vue.set(this.via, i, res.data.results[i].via)
                Vue.set(this.routeNum, i, res.data.results[i].routeNum)
                Vue.set(this.postPlateNo, i, res.data.results[i].postPlateNo)
                Vue.set(this.predictTm, i, res.data.results[i].predictTm)
                Vue.set(this.remainStation, i, res.data.results[i].remainStation)
                Vue.set(this.routeId, i, res.data.results[i].routeId)
                Vue.set(this.routeNm, i, res.data.results[i].routeNm)
                
            }
            console.log('노선번호, 차량번호, 몇정류장전에 위치한지, 몇분후 도착인지 등',)
            console.log(this.via[0],this.routeNum[0],this.postPlateNo[0],this.predictTm[0],this.remainStation[0])

            })
            .catch(err =>{
            console.log(err)
            
            })//axios1
        EventBus.$on('open',() =>{
            console.log('리스트를 클릭하면 모달 컴포넌트의 상태가 true가 되면서 오픈된다.')
            this.dialog = true
       });
    },
    methods:{
        close(){
            EventBus.$emit('close')
        }
    }
    

  }
</script>
<style scoped>
.left{
width:60%;
float:left;
}
.right{
width:40%;
float:right;
text-align: right;
padding-right: 10px;
}
</style>