<script setup lang="ts">
import L from "leaflet";
import { ref, onMounted, computed, watch } from "vue";

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
onMounted(() => {
  const map = L.map("map", {
    // center: [13.195882,52.517553],
    center: [52.517553, 13.195882],
    zoom: 15,
  });

  L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
    attribution:
      '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
  }).addTo(map);
});

const navigate = () => {
  fetch(
    `https://api.openrouteservice.org/v2/directions/foot-walking?api_key=5b3ce3597851110001cf6248ca98fc4abd7f4ba2a8e0810928ee1d68&start=${startPoint.value[0]},${startPoint.value[1]}&end=${endPoint.value[0]},${endPoint.value[1]}`
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
    })
    .catch((err) => console.error(err));
};
</script>
<template>
  <div>
    <div id="map"></div>
    <button type="button" @click="navigate">Navigation</button>
    {{ flippedDirections }}
  </div>
</template>
<style scoped>
#map {
  min-width: 300px;
  width: 100vw;
  height: 300px;
}
</style>
