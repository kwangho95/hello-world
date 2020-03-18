<template>
       <div v-if="!state" style="text-align:center">
        
        <v-icon x-large
        >mdi-alert-circle-outline</v-icon>
        <v-card-text>
            정류소를 검색하시면 노선 정보 및 배차시간<br>
            첫차 및 마지막 운행시간, 운수사 정보 등을<br>
            확인하실 수 있습니다.
        </v-card-text>
    </div> 
</template>
<script>
export default {
    props:['state'],

    data(){
        return{

        }
    }
}
</script>