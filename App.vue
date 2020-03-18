<template>
  <v-app id="app">
    <v-navigation-drawer
      v-model="drawer"
      width="400px"
      app
    >
      <div style="text-align:center">
        <router-link to="/BusList"><v-btn class="ma-2" rounded  color="success">노선 검색</v-btn></router-link>
        <router-link to="/BusStop"><v-btn class="ma-2" rounded  color="success">정류소 검색</v-btn></router-link>
    </div>
      <!-- <bus-list></bus-list> -->
      <router-view></router-view>
    </v-navigation-drawer>
    <v-content>
      
      <v-container
        class="fill-height"
        fluid
        style="padding:0px"
      >
           <v-btn tile color="white" class="drawer" @click.stop="drawer = !drawer">
             <v-icon color="indigo">mdi-hand-pointing-right</v-icon>
           </v-btn>
           <Map></Map>
           <v-snackbar
            v-model="alert"
            top
          >
            찾을 수 없습니다.
            <v-btn
              color="red"
              text
              @click="alert = false"
            >
              Close
            </v-btn>
          </v-snackbar>
             

      </v-container>
    </v-content>
    <v-footer
      color="indigo"
      app
    >
      <span class="white--text">&copy; 2019</span>
    </v-footer>
  </v-app>
</template>

<script>

import BusList from './components/BusList.vue'
import Map from './components/map.vue'
import {EventBus} from "./components/eventbus.js"


  export default {
    components:{
      BusList,
      Map
    },
    props: {
      source: String,
    },
    data: () => ({
      drawer: null,
      alert: false,
    }),
    methods:{
      snackbar(){
      this.alert=false

      }
    },
     mounted(){
       EventBus.$on('alert',() =>{
            this.alert=true
       });
    }
  }
  
</script>
<style scoped>
h1{
  font-size:20px;
}
#map{
  width:100%;
  height:100%;
}
.drawer{
  position: absolute;
  z-index: 10000;
  left:0;
  top:0;
}
#app{
  font-family: 'Noto Serif KR', serif;
}
a{
  text-decoration: none;
}



</style>