<template>
  <div>
    <p>Admin User</p>
    <h1>{{formState.state ? "Sharing Location ................." : "Stopped Location............"}}</h1>
    <GoogleMap
      v-if="formState.showMap"
      api-key="AIzaSyABPywZVGnAsgP8llgiBFnx8sAvUUiRyv4"
      style="width: 100%; height: 500px"
      :center="formState.center"
      :zoom="25"
    >
      <Polyline v-if="formState.loader" :options="line.flightPath" />
      <Marker v-if="formState.loader" :options="{ position: formState.center }" />
    </GoogleMap>

  </div>
</template>

<script>
import { defineComponent, reactive,onMounted } from "vue";
import { useRoute } from "vue-router";
// import AddUser from "../../pages/AddUser.vue";
import { GoogleMap, Marker, Polyline } from "vue3-google-map";
import User from "../../../server/user"
export default defineComponent({
  components: {
    // AddUser,
    GoogleMap, Marker, Polyline,
  },
  setup() {
    const router = useRoute();
    const formState = reactive({
      id: router.params.id,
      showMap:false,
      data:[],
      center: { lat: 40.689247, lng: -74.044502 },loader: false,
    });
    const line = reactive({
      flightPath: {
        path: [],
        geodesic: true,
        strokeColor: "#FF0000",
        strokeOpacity: 1.0,
        strokeWeight: 2,
        state:null,
      }
    });

    onMounted(async ()=>{
      console.log("onMOunted .........")
    //   await User.getPointer(router.params.id).then((obj)=>{
    //     console.log(obj)
    //     var getData = obj.get('userLocation')
    //     line.flightPath.path=getData
    //     console.log("getData",getData)
    //     var len = getData.length
    //     console.log("len",len)
    //     formState.center=getData[len-1]
    //     console.log(formState.center)
    //     formState.data=getData
    // })
    //   formState.loader=true
    // getLocation

      window.setInterval(() => {
        
        getLocation();
      }, 5000); //number of milliseconds
  })
  const getLocation =async ()=>{
    await User.getPointer(router.params.id).then((obj)=>{
    formState.loader=false

      formState.showMap=true
        var state = obj.get("state")
        formState.state = state
        console.log(obj)
        var getData = obj.get('userLocation')
        line.flightPath.path=getData
        console.log("getData",getData)
        var len = getData.length
        console.log("len",len)
        formState.center=getData[len-1]
      formState.loader=true

        console.log(formState.center)
        formState.data=getData
    })
  }

    return {
      formState,
      line,
      getLocation

    };
  },
});
</script>
<style scoped>
.slider.round {
  border-radius: 34px;
}

.slider.round:before {
  border-radius: 50%;
}
</style>
