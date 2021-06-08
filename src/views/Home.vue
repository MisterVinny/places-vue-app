<template>
  <div class="home">
    <h1>{{ message }}</h1>

    <div>
      <input v-model="placesParams.name" type="text" />
      <input v-model="placesParams.address" type="text" />
      <button v-on:click="createPlace()">Submit Place</button>
      <p>{{ placesParams }}</p>
    </div>

    <div v-for="place in places" v-bind:key="place.id">
      <p><b>Name:</b> {{ place.name }}</p>
      <p><b>Address:</b> {{ place.address }}</p>
      <hr />
    </div>
  </div>
</template>

<style>
p b {
  font-style: bold;
}
</style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      message: "Good stuff in Chicago!",
      places: [],
      placesParams: {},
    };
  },
  created: function () {
    this.placesIndex();
  },
  methods: {
    placesIndex: function () {
      axios.get("http://localhost:3000/places/").then((response) => {
        console.log(response.data);
        this.places = response.data;
      });
    },

    placesCreate: function (placesParams) {
      axios
        .post("http://localhost:3000/places/")
        .then((response) => {
          console.log(response.data);
          this.places.push(response.data);
        })
        .catch();
    },
  },
};
</script>
