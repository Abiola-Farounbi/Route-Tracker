<template>
        <div id='map' class='window-height'></div>
</template>

<script>
export default {
  name: 'mapDisplay',
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
       displayMap(){
            const map = tt.map({  
            key: "8h504Wc4AXL6OPndqhrtKf70AovVBL3V",  
            container:'map', // Container ID 
            style: 'tomtom://vector/1/basic-main',  //the specified map style 
        });  
        map.addControl(new tt.FullscreenControl());  
        map.addControl(new tt.NavigationControl()); 
        const location = [this.longitude,this.latitude];
        const popupOffset = 25; 
        const marker = new tt.Marker().setLngLat(location).addTo(map); 
        const popup = new tt.Popup({ offset: popupOffset }).setHTML("Your Destination"); 
        marker.setPopup(popup).togglePopup();       
       }
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