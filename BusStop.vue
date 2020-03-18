<template>
    <div class="box">
        <v-text-field 
            class="ma-4"
            label="정류소 검색"
            dense
            prepend-inner-icon="mdi-magnify"
            outlined
            color="blue"
            v-model="searchItem"
            v-on:keyup.enter="search"
            clearable
     ></v-text-field>
     <ResultTitle v-if="state" :busCount = busCount :busName="searchItem" ></ResultTitle>
     <Guide :state = state></Guide>
     <ResultList :stationNm = stationNm :mobiNum = mobiNum :stationEngNm = stationEngNm :stationId = stationId></ResultList>
     
     <!-- <BusStopModal v-show="show"></BusStopModal> -->
    
    </div>
</template>
<script>
import axios from 'axios'
import Vue from 'vue'
import ResultTitle from './BusStopComponent/ResultTitle'
import Guide from './BusStopComponent/Guid'
import ResultList from './BusStopComponent/ResultList'
import BusStopModal from './BusStopComponent/BusStopModal'

import {EventBus} from "./eventbus.js"




export default {
    components:{
        ResultTitle,
        Guide,
        ResultList,
    },
    
    data(){
        return{
            searchItem:'',
            stationNm:[],
            busName:'',
            busCount:0,
            state:false,
            stationEngNm:[],
            mobiNum:[],
            gpsX:[],
            gpsY:[],
            show:false,
            stationId:[],
            snackbar:false,
            multiLine: true,

        }
    },
    
    beforeDestroy(){
        // EventBus.$off('gps')
        // console.log('off완료')
    },
    mounted(){
        console.log('BusStop.vue mounted')
        if(this.$route.params.stationList != null){
        console.log('노선검색에서 받아온 정류소 이름: ',this.$route.params.stationList)
        console.log('노선검색에서 받아온 stationId: ',this.$route.params.stationId)
        this.searchItem = this.$route.params.stationId
        this.search()
        }
        
    },
    methods:{
        search(){
            console.log('정류소를 검색함')
            //입력한 텍스트 this.searchItem에 해당하는 정류소를 가져온다.
             axios
            .get('https://cors-anywhere.herokuapp.com/'
            +'http://bus.andong.go.kr:8080/api/facilities/station/getDataList?type=Paging&pageSize=10&pageUnit=2000&pageIndex=1&searchKeyword='+this.searchItem,)
            .then(res => {
            console.log('정류소 keyword에 맞은 api 호출')
            if(this.$route.params.stationId != null)
            {
            this.searchItem = this.$route.params.stationList 
            }
            console.log(res)
            //가져온 정류소를 this.stationNm 배열에 전달
            this.stationNm=[]
            this.stationEngNm=[]
            this.mobiNum=[]
            this.gpsX=[]
            this.gpsY=[]
            this.stationId=[]
            console.log('리스트- > 정류소, 영어이름, 모바일번호, id, gpsX, gpsY')
            for(let i = 0; i < res.data.results.length; i++)
            {
                Vue.set(this.stationNm, i, res.data.results[i].stationNm)
                Vue.set(this.stationEngNm, i, res.data.results[i].stationEngNm)
                Vue.set(this.mobiNum, i, res.data.results[i].mobiNum)
                Vue.set(this.stationId, i, res.data.results[i].stationId)
                Vue.set(this.gpsX, i, res.data.results[i].gpsX)
                Vue.set(this.gpsY, i, res.data.results[i].gpsY)
            }
            console.log('데이터 입력 확인, ex) 정류소리스트 목록')
            console.log(this.stationNm)
            
            //가져온 정류소의 length 만큼 this.busCount에 저장
            this.busCount = res.data.results.length
            //검색 하면 state 상태 true, state 상태가 true면 ResultTitle.vue component를 불러온다
            console.log('검색성공-> 리스트 컴포넌트를 불러옴, 검색실패-> 현재 컴포넌트')
            console.log('문제점-> Vue.set으로 배열에 데이터를 넣을때 이전 배열의 데이터가 남아있다\n해결-> 배열리셋 or 배열의길이 splice')
            console.log('gps x,y 좌표를 eventbus를 통해 map.vue 컴포넌트에 전달')
            EventBus.$emit('gps', this.gpsX, this.gpsY)
            this.state=true
            })
            .catch(err =>{
            console.log(err)
            EventBus.$emit('alert')
            this.busCount = 0
            this.stationNm=[]
            })//axios1
                
            },
            
           
    }
}
</script>
<style scoped>

   
</style>