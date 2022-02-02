<template>
        <div id='map' class='window-height'></div>
</template>

<script>

import tts from '@tomtom-international/web-sdk-services'

export default {
  name: 'mapDisplay',
  data(){
      return{
        stations: []
      }
  },
   props:{
    longitude:{
      type: Number,
      required: true,
      default:0
    },
    latitude:{
      type: Number,
      required: true,
      default:0
    },
   },
       mounted() {  
        this.displayMap()
     },
    //  watch props for any change in value and the calls map function
  watch: {
    longitude() {
      this.displayMap();
    },
  
    latitude() {
      this.displayMap();
    },
  },
     methods:{
      async  displayMap(){      
            const _this = this  
            var HQ = { lat: _this.latitude, lng: _this.longitude }  
            const map = await tt.map({  
            key: "8h504Wc4AXL6OPndqhrtKf70AovVBL3V",  
            container:'map', // Container ID 
            style: 'tomtom://vector/1/basic-main',  //the specified map style 
            center: HQ,
            zoom:5
        });  
        map.addControl(new tt.FullscreenControl());  
        map.addControl(new tt.NavigationControl()); 

         await tts.services.poiSearch({
            key: "8h504Wc4AXL6OPndqhrtKf70AovVBL3V",
            query: 'Gas Station',
            center: [ _this.latitude, _this.longitude ]
            })
            .then(function(data) {
              _this.stations = data.results
                })
          
              .catch((err) => {
                console.log(err)
            });   
        // looping through the result to get the value of each location
       this.stations.forEach(function (e) {
                const popupOffset = 25; 
                const marker = new tt.Marker().setLngLat(e.position).addTo(map);
                const popup = new tt.Popup({ offset: popupOffset }).setHTML(
                  "<p>" + e.poi.name + "<br>" + e.address.freeformAddress +  "</p>" );
                marker.togglePopup(popup).setPopup(popup);    
            });
       },
     
     }
}

</script>
<style scoped>
#map { 
    height: 50vh; 
    width: 50vw; 
    margin:20px 0px;
} 
</style>


