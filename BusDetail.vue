<template>
<div>
       
     <router-view></router-view>
    <v-row justify="center">
    <v-btn icon @click="back">
        <v-icon color="black">mdi-arrow-left</v-icon>
        <v-icon color="#83c100">mdi-bus</v-icon>
        <h2>{{linkNm}}</h2>
    </v-btn>
    
    </v-row>
    <v-timeline v-for="(item, index) in stationList" :key="index"
    dense
    :align-top="alignTop">
    
       <router-link style="text-decoration:none; color:black" :to="{ name: 'BusStop', params: {stationList:stationList[index],stationId:stationId[index]}}">
     <v-timeline-item id="app"
        v-if="icon[index]=='mdi-bus'"
        class="pb-0"
        color="white"
        small
        left
        :icon="icon[index]"
        icon-color="light-blue"
        >
        {{stationList[index]}}
         <v-icon class="mb-1"  color="indigo" dark>{{'mdi-bus'}}</v-icon>
        <v-divider></v-divider>
     </v-timeline-item>
     <v-timeline-item id="app"
        v-else
        class="pb-0"
        color="white"
        small
        left
        icon="mdi-arrow-down-drop-circle-outline"
        icon-color="light-blue"
        >
        {{stationList[index]}}
        <v-divider></v-divider>
     </v-timeline-item>
     </router-link>
    
     
         
    </v-timeline>
    </div>
</template>
<script>
import axios from 'axios'
import {EventBus} from "../eventbus.js"
import Vue from 'vue'
import VueRouter from 'vue-router'
import router from '../../router'
export default {
   
    props:['routeId','busNm'],
    data(){
        return{
            alignTop:false,
            stationList:[],
            stationId:[],
            routeInfo:[],
            stationOrd:[],
            postPlateNo:[],
            icon:[],
            linkId:'',
            linkNm:'',
          
        }
    },
    updated() {
    this.$nextTick(function () {
      // 모든 화면이 렌더링된 후 실행합니다.
    })
  },
  mounted(){
      console.log('BusDeatil.vue mounted')
      
  },
      created(){
        this.linkId = this.routeId
        this.linkNm = this.busNm
        if(this.$route.params.routeNm != null){
        
        console.log('노선검색에서 받아온 노선 이름: ',this.$route.params.routeNm)
        console.log('노선검색에서 받아온 routeId: ',this.$route.params.routeId)
        
        this.linkId = this.$route.params.routeId
        this.linkNm = this.$route.params.routeNm
        }
        axios
       .get('https://cors-anywhere.herokuapp.com/'
      +'http://bus.andong.go.kr:8080/api/route/station/getDataList?routeId='+this.linkId)//해당 노선번호의 정류소 목록을 가져오는 api
      .then(res => {
          axios
          .get('https://cors-anywhere.herokuapp.com/'
          +'http://bus.andong.go.kr:8080/api/route/bus/getDataList?routeId='+this.linkId)//정류소 도착정보 api
            .then(res => {
              
              console.log('정류소리스트 배열에 현재 운행중인 버스가 몇번째에 위치했는지 알수있는 api호출 stationOrd를 파라미터로 ...')
              //icon 배열안에 리스트의 몇번째에 버스가 위치한지 입력 
              for(let i = 0; i < res.data.results.length; i++)
              {
                  Vue.set(this.stationOrd, i, res.data.results[i].stationOrd)//stationOrd[null,...,3,...37,...null,...83,...null]
                  this.icon[res.data.results[i].stationOrd-1] = res.data.results[i].stationOrd//icon[n,n,3,n,n,n...,37]
                  Vue.set(this.icon, this.stationOrd - 1, res.data.results[i].stationOrd)
                  Vue.set(this.postPlateNo, i, res.data.results[i].postPlateNo)    
              }
              console.log('정류소 리스트 배열에',this.stationOrd,'번째에 위치해 있음을 알 수있음')
              //icon 배열안에 버스가 몇번째에 위치한 정보가 있으면 버스 아이콘, 정보가 없으면 화살표 아이콘을 넣는다.
              //postPlateNo 버스가 몇번째 위치한 정보가 있으면 버스번호에 대한 정보를 넣는다.
              for(let i = 0; i < this.icon.length; i++){
                      if(this.icon[i] != null)//
                      { 
                          
                          Vue.set(this.icon, i, "mdi-bus")
                        
                      }else{
                          Vue.set(this.icon, i, "mdi-arrow-down-drop-circle-outline")
                      }    
                  }
                  console.log('리스트중 버스가 위치한 지점에 icon을 넣기위해 icon배열에 stationOrd를 index설정한 위치에 bus icon설정')      
        })
            .catch(err =>{
              console.log(err,'운행중인 버스가 없을 경우 모두 화살표 아이콘')
        })    
              console.log('해당 노선의 정류소리스트 호출....')
              
              this.routeInfo = res.data.results
        for(let i=0; i<res.data.results.length; i++)
        {
            Vue.set(this.stationList,i,res.data.results[i].stationNm)
            Vue.set(this.stationId, i, res.data.results[i].stationId)
           //this.stationList[i]=res.data.results[i].stationNm
        }
            console.log(this.stationList)
      })
      .catch(err =>{
        console.log(err)
      })
    
    },
    methods:{
        back(){
            console.log('뒤로가기 eventbus emit')
            EventBus.$emit("back", true)
        },
        test(){
        

    //     axios
    //    .get('https://cors-anywhere.herokuapp.com/'
    //   +'http://bus.andong.go.kr:8080/api/facilities/station/getBusArriveData?stationId='+this.routeInfo[0].stationId)//정류소 도착정보 api
    //   .then(res => {
    //      console.log('stationId로 api 호출',this.routeInfo[0].stationId)
    //      console.log(res)

    //   })
    //   .catch(err =>{
    //     console.log(err)
    //   })


        }
    }

}
</script>
<style scoped>
#app > hr{
    background: red;
}
</style>