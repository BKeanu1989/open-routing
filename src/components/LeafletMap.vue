<script setup lang="ts">
import L from "leaflet";
import { ref, onMounted, computed, watch } from "vue";

let mapInstance = null;
const startPoint = ref<number[]>([52.517553, 13.195882]);
const endPoint = ref([52.527322, 13.195898]);
const directions = ref<any>(null);
const flippedDirections = computed(() => {
  return (
    directions?.value?.map((singleDirection) => {
      return [singleDirection[1], singleDirection[0]];
    }) || null
  );
});

watch(flippedDirections, (newVal, oldVal) => {
  console.log(newVal);
  const polyLine = L.polyline(newVal, { color: "red" }).addTo(mapInstance);
});

onMounted(() => {
  mapInstance = L.map("map", {
    // center: [13.195882,52.517553],
    center: [52.517553, 13.195882],
    zoom: 15,
  });

  L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
    attribution:
      '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
  }).addTo(mapInstance);
});

const navigate = () => {
  fetch(
    // `https://api.openrouteservice.org/v2/directions/foot-walking?api_key=5b3ce3597851110001cf6248ca98fc4abd7f4ba2a8e0810928ee1d68&start=8.681495,49.41461&end=8.687872,49.420318`
    `https://api.openrouteservice.org/v2/directions/foot-walking?api_key=5b3ce3597851110001cf6248ca98fc4abd7f4ba2a8e0810928ee1d68&start=${startPoint.value[1]},${startPoint.value[0]}&end=${endPoint.value[1]},${endPoint.value[0]}`
  )
    .then((res) => {
      return res.json();
    })
    .then((data) => {
      if (data?.features[0]?.geometry) {
        directions.value = data.features[0].geometry.coordinates;
      }
      //   console.log(data);
      //   directions.value = data;
    });
};
</script>
<template>
  <div>
    <div id="map"></div>
    <button type="button" @click="navigate">Navigation</button>
  </div>
</template>
<style scoped>
#map {
  min-width: 300px;
  width: 100vw;
  height: 300px;
}
</style>
