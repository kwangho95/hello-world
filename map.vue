<template>
     <div id="map">
         <div>adll</div>
     </div>
</template>
<script>
import {EventBus} from "./eventbus.js"
import Vue from 'vue'

export default {
    data(){
        return{
        gpsX:[],
        gpsY:[],
        remove:new kakao.maps.Marker,
        removeItem:0
        }
    },
    created(){
         this.CreateMapMaker()
    },
    methods:{
        CreateMapMaker(){
            
        }
    },

    mounted(){
        console.log('map.vue mounted')
        var container = document.getElementById('map'); //지도를 담을 영역의 DOM 레퍼런스
        var options = { //지도를 생성할 때 필요한 기본 옵션
	        center: new daum.maps.LatLng(36.562597, 128.726515), //지도의 중심좌표.
	        level: 6 //지도의 레벨(확대, 축소 정도)
          };
        
        var map = new daum.maps.Map(container, options); //지도 생성 및 객체 리턴
        EventBus.$on('gps', (x, y) =>{
           
            console.log('map.vue eventbus On')
            
            
            // this.$nextTick(function() { 
            // });
            console.log('받은 gps 좌표',x,y)
            this.gpsX=[]
            this.gpsY=[]
            for(let i = 0; i < x.length; i++)
             {
    
                Vue.set(this.gpsX, i, x[i])
                Vue.set(this.gpsY, i, y[i])
             }
            //  console.log('반응형으로 바꾼 gps 좌표')
            //  console.log(this.gpsX , this.gpsY)
        
        

        // 마커 이미지의 이미지 주소입니다
        var imageSrc = "http://t1.daumcdn.net/localimg/localimages/07/mapapidoc/markerStar.png"; 
            
        for (var i = 0; i < this.gpsX.length; i ++) {
            
            // 마커 이미지의 이미지 크기 입니다
            var imageSize = new kakao.maps.Size(24, 35); 
            
            // 마커 이미지를 생성합니다    
            var markerImage = new kakao.maps.MarkerImage(imageSrc, imageSize); 
            
            // 마커를 생성합니다
           var marker = new kakao.maps.Marker({
                map: map, // 마커를 표시할 지도
                position: new kakao.maps.LatLng(this.gpsY[i],this.gpsX[i]), // 마커를 표시할 위치
                title : '', // 마커의 타이틀, 마커에 마우스를 올리면 타이틀이 표시됩니다
                image : markerImage // 마커 이미지 
            });
        }
       });
       

            },
            
        }
</script>