<template>
<div class="box">
    <v-list class="overflow-y-auto" style="max-height:730px">
    <v-list-item class="overflow-y=auto"  v-for="(item, index) in stationNm" :key="index"  @click="open(index)"
     style="border-bottom:1px solid #e5e5e5;width:90%; margin:0 auto; padding:0px;">
        <v-icon
         class="mb-8"
         color="#83c100"
         left > mdi-bus
        </v-icon>
        <v-list-item-content style="font-family: 'Noto Serif KR', serif;"> 
        <v-list-item-title style="font-weight:700; font-size:16px; " v-text="item"></v-list-item-title>
        <v-list-item-subtitle class="text--primary">{{stationEngNm[index]}}</v-list-item-subtitle>
        <v-list-item-subtitle >{{mobiNum[index]}}</v-list-item-subtitle>
         </v-list-item-content >
    </v-list-item>
    </v-list>
    <BusStopModal v-if = "state"  :stationId = stationId[itemIndex] :itemIndex = itemIndex :stationNm = stationNm></BusStopModal>
     
</div>
</template>
<script>
import {EventBus} from ".././eventbus.js"
import BusStopModal from './BusStopModal'
import Vue from 'vue'
import axios from 'axios'
export default {
    props:[
        'busName',
        'busCount',
        'stationNm',
        'mobiNum',
        'stationEngNm',
        'stationId',
        'stationList'
    ],
    components:{
        BusStopModal
    },

    data(){
        return{
        state:false,
        dialog:true,
        via:[],
        routeNm:[],
        postPlateNo:[],//차량번호
        predictTm:[],//몇분뒤 도착
        remainStation:[],//몇정류전
        itemIndex:null,
        }
    },
    mounted(){
        
        EventBus.$on('close',() =>{
            this.state=false
       });
       console.log('ResultList.vue mounted')
            
       
    },
    methods:{
        open(index){
            this.itemIndex = index
            this.state = true
            EventBus.$emit('open')
            console.log
        }
    }
}
</script>
<style scoped>
/* .box{
        height:75vh;
        overflow-y:auto;
        overflow-x:hidden;
} */
</style>