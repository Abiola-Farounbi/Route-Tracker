<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar class="bg-deep-purple-8  text-white">
        <q-toolbar-title >
          Route Tracker
        </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-page-container>
     <!-- To handle the search for location -->
      <div class="q-pa-md" >
    <q-form @submit="SearchAndCalculateRoute()" class="q-gutter-md" >
      <q-input filled v-model="locationQuery" label="Search for an address/location"/>
      <div>
        <q-btn label="Search" type="submit" color="deep-purple-8"/>
      </div>
    </q-form>

    <!--  map display component -->
    <map-display v-if='routeLongitude && routeLatitude' :longitude="routeLongitude" :latitude="routeLatitude" ></map-display>

    <div>
      <!-- Display the result of the search -->
      <p>Destination -  {{routeAddress}} </p>
      <p> Estimated Route Distance(KM) - {{destinationDistance}}
        <br>
           Estimated Time(MINS) -  {{timeToDestination}}
      </p>
      <p class='text-red-10'>
        {{errorMessage}}
      </p>
    </div>
      </div>


    </q-page-container>
  </q-layout>
</template>

<script>
// import the map component
import mapDisplay from "components/mapDisplay.vue"

// Importing the tomtom service
import tt from '@tomtom-international/web-sdk-services'

export default {
  name: 'MainLayout',
  components:{
    mapDisplay
  },
  data () {
    return {
    userLat: 0,
    userLng: 0,
    locationQuery:"",
    routeAddress: "",
    routeLatitude:0,
    routeLongitude: 0,
    destinationDistance: "",
    timeToDestination:"",
    errorMessage:""
    }
  },
    // Getting the user's location
created(){
  navigator.geolocation.getCurrentPosition(
      position => {
        this.userLat = position.coords.latitude;
        this.userLng = position.coords.longitude;
        
      },
      error => {
        console.log(error.message);
      }
    );
},

  methods:{
    async SearchAndCalculateRoute(){
    const _this = this
      // Using the fuzzySearch Service
    await tt.services.fuzzySearch({
      key:"8h504Wc4AXL6OPndqhrtKf70AovVBL3V",
      query: this.locationQuery  //The input gotten from the search
    }).then(function (response) {
      _this.routeAddress = response.results[0].address.freeformAddress
      _this.routeLatitude =  response.results[0].position.lat
      _this.routeLongitude = response.results[0].position.lng
    })   


  //  Using the routing service
      const startLoc = this.userLat + ',' + this.userLng //The location of the user
      const stopLoc = this.routeLatitude + ',' +this.routeLongitude // The location of the destination
      await tt.services.calculateRoute({
        key: "8h504Wc4AXL6OPndqhrtKf70AovVBL3V",
        locations:  `${startLoc} : ${stopLoc} `,
        // locations:  '4.8,52.3:4.82,52.37',
        travelMode: 'car', //Specifying a routing parameter
        })
      .then(function(routeData) {
        const routeGeoJson = routeData.toGeoJson()
          _this.timeToDestination = (routeGeoJson.features[0].properties.summary.travelTimeInSeconds / 60).toFixed(2)
          _this.destinationDistance = (routeGeoJson.features[0].properties.summary.lengthInMeters / 1000).toFixed(2)
          console.log(routeGeoJson.features[0].properties.summary.lengthInMeters / 1000)
        })
   
      .catch((err) => {
        console.log(err)
        _this.errorMessage = 'Locations cannot be mapped. Try another location'
     });
  
      }

  },


 
}
</script>
