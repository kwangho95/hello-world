<template>
<div>
    
     <v-text-field 
            v-if="state"
            class="ma-4"
            label="노선 번호 검색"
            dense
            prepend-inner-icon="mdi-magnify"
            outlined
            color="blue"
            v-model="searchItem"
            v-on:keyup.enter="search"
            clearable
     ></v-text-field>
    <div v-if="!routeNm.length" style="text-align:center">
        <v-icon x-large
        >mdi-alert-circle-outline</v-icon>
        <v-card-text>
            노선을 검색하시면 노선 정보 및 배차시간<br>
            첫차 및 마지막 운행시간, 운수사 정보 등을<br>
            확인하실 수 있습니다.
        </v-card-text>
        
    </div>
    
     <v-list-item class="routeList" v-for="(item, index) in routeNm" :key="index" @click="state = !state,shootId(index)" v-else-if="routeNm.length && state"
     style="border-bottom:1px solid #e5e5e5;width:90%; margin:0 auto; padding:0px;">
        <v-icon
         class="mb-8"
         color="#83c100"
         left > mdi-bus
        </v-icon>
        <v-list-item-content style="font-family: 'Noto Serif KR', serif;"> 
        <v-list-item-title style="font-weight:700; font-size:16px; " v-text="item"></v-list-item-title>
        <v-list-item-subtitle class="text--primary">{{runTotCnt[index]}}대 운행중 · 일 {{runCnt[index]}}회 운행</v-list-item-subtitle>
        <v-list-item-subtitle >운행정보 {{StTm[index]}}~{{EdTm[index]}}</v-list-item-subtitle>
         </v-list-item-content >
    </v-list-item>
    <BusDetail  v-else-if="!state" :routeId="prop" :busNm = busNm ></BusDetail>
     
</div>
</template>
<script>


import axios from 'axios'
import BusDetail from './DetailComponent/BusDetail'
import {EventBus} from "./eventbus.js"

export default {
    components:{
        BusDetail
    },
    data(){
        return{
        searchItem:'',
        routeNum:[],
        routeInfo:[],
        routeNm:[],
        runTotCnt:[],
        StTm:[],
        EdTm:[],
        runCnt:[],
        routeId:[],
        prop:'',
        state:true,
        busNm:''
        
        }
    },
    mounted(){
        console.log('BusList.vue mounted')
        console.log('api의 노선 리스트들을 모두 불러온다.')
       axios
      .get('https://cors-anywhere.herokuapp.com/'
      +'http://bus.andong.go.kr:8080/api/route/getDataList?type=All',)
      .then(res => {
        console.log('불러온 데이터....')
        console.log(res)
         this.routeInfo = res.data.results
        
         for(let i=0; i<res.data.results.length; i++)
         {
         this.routeNum[i] = res.data.results[i].routeNum
        }
      })
      .catch(err =>{
        console.log(err)
      })
      EventBus.$on('back', item =>{
          
          this.state = !this.state
          console.log('뒤로가기 버튼 click 이벤트 확인 ui변경완료')
      });

    
    },
    methods:{
        search(){
            console.log('노선번호 검색')
            this.routeNm=[]
            this.runTotCnt=[]
            this.StTm=[]
            this.EdTm=[]
            this.minInterval=[]
            this.runCnt=[]
            this.routeId=[]
         if(this.routeNum.indexOf(this.searchItem) != -1){
             console.log('가져온 api 데이터중에 검색한 노선 번호가 있는것을 확인')
             for(let i = 0; i < this.routeInfo.length; i++)
             {
                 if(this.searchItem == this.routeInfo[i].routeNum)
                 {
                     this.routeNm.push(this.routeInfo[i].routeNm)//노선이름
                     this.runTotCnt.push(this.routeInfo[i].runTotCnt)//현재 몇대 운행중인지
                     this.StTm.push(this.routeInfo[i].stTm)//운행시작시간
                     this.EdTm.push(this.routeInfo[i].edTm)//운행종료시간
                     //this.minInterval.push(this.routeInfo[i].minInterval+"~"+this.routeInfo[i].maxInterval)//배차간격
                     this.runCnt.push(this.routeInfo[i].runCnt)//운행횟수
                     this.routeId.push(this.routeInfo[i].routeId)
                 }     
             }
            console.log('노선이름, 현재 운행수, 운행시작 및 종료시간, 총 운행횟수, routeId 등을 사용하기 위해 가져온다')
            console.log(this.routeNm[0],this.runTotCnt[0],this.StTm[0],this.EdTm[0])
         }else{
             console.log('api에 존재하지않은 노선번호일경우 alert')
             alert('존재하지않는 번호 입니다.')
         }
        },
        shootId(index){
            console.log('list click -> 해당 노선의 routeId(api 파라미터로 사용) 와 노선이름(title)을 props으로 BusDetail.vue에 전달')
            this.prop = this.routeId[index]
            console.log('선택한 routeId는',this.prop)
            
            this.busNm = this.routeNm[index]
            console.log('선택한 노선이름은',this.busNm)
        },
    }
    
}
</script>
<style scoped>
.routeList:hover{background:#F6F6F6; opacity:0.9;cursor: pointer;}

a{text-decoration: none;}

</style>