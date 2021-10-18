<template>
  <div id="map">
  </div>
</template>

<script>
import { Loader } from "@googlemaps/js-api-loader"
const loader = new Loader({
  apiKey: "",
  version: "weekly",
  // ...additionalOptions,
});

loader.load().then(() => {
  console.log(!!window.google.maps)
  // eslint-disable-next-line no-undef
  // eslint-disable-next-line no-unused-vars
  const map = new window.google.maps.Map(document.getElementById("map"), {
    center: { lat: -34.397, lng: 150.644 },
    zoom: 8,
  });
});

export default {

}
</script>

<style>
#map {
  height: 100vh;
}
</style>
