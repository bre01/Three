<template>
   <div id="map"></div>
   <div style="width: 300px;height:400px">{{ props.position }}</div>
</template>
<script lang="ts" setup>
import * as L from "leaflet";
import "leaflet/dist/leaflet.css";
import throttle from "../functions/throttle.js";

const props = defineProps(['position']);


let mak:L.Marker;

onMounted(() => {
   const map = L.map('map', {
      crs: L.CRS.Simple
   });
   const bounds = [[0, 0], [300, 300]] as L.LatLngBoundsLiteral;
   const image = L.imageOverlay("models/download.jpeg", bounds).addTo(map);
   map.fitBounds(bounds);
   const sol = L.latLng([150, 150]);

   mak = L.marker(sol)

   mak.addTo(map);
   
})

watch(() => props.position, (newPosition) => {
   console.log(newPosition!.x, newPosition!.y)
   if (mak) {
      mak.setLatLng([newPosition!.x, newPosition!.y]);
   }
})
let thPrint=throttle(()=>{console.log(props.position)},1000);
thPrint();

</script>

<style>
#map {
   width: 300px;
   height: 300px;
   position: absolute;
   top: 20px;
   right: 20px;
   z-index: 100;
}
</style>