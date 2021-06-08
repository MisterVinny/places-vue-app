<template>
  <div class="home">
    <h1>{{ message }}</h1>

    <div>
      <h2>Create a new place:</h2>
      <input v-model="newPlaceParams.name" type="text" />
      <input v-model="newPlaceParams.address" type="text" />
      <button v-on:click="createPlace()">Submit Place</button>
      <hr />
    </div>

    <div v-for="place in places" v-bind:key="place.id">
      <p><b>Name:</b> {{ place.name }}</p>
      <p><b>Address:</b> {{ place.address }}</p>
      <button v-on:click="showPlace(place)">More Info</button>
      <hr />
    </div>

    <dialog id="place-details">
      <form method="dialog">
        <div>
          <h1>Place Info</h1>
          <p>
            Name:
            <input type="text" v-model="activePlace.name" />
          </p>
          <p>
            Address:
            <input type="text" v-model="activePlace.address" />
          </p>

          <div>
            <button v-on:click="updatePlace()">Update</button>
            <button>Close</button>
          </div>

          <br />
        </div>
        <div>
          <button v-on:click="destroyPlace()">Destroy</button>
        </div>
      </form>
    </dialog>
  </div>
</template>

<style>
h1 {
  font-size: 3em;
}
p b {
  font-style: bold;
}
</style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      message: "The good stuff in Chicago!",
      places: [],
      newPlaceParams: {},
      activePlace: {},
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("http://localhost:3000/places/").then((response) => {
        console.log(response.data);
        this.places = response.data;
        this.places.sort((a, b) => a.id - b.id).reverse();
      });
    },

    createPlace: function () {
      axios
        .post("http://localhost:3000/places/", this.newPlaceParams)
        .then((response) => {
          console.log("Place added.", response.data);
          this.places.push(response.data);
          this.places.sort((a, b) => a.id - b.id).reverse();
        })
        .catch((error) => {
          console.log(error.response.data.errors);
        });
    },

    showPlace: function (place) {
      console.log(place);
      this.activePlace = place;
      document.querySelector("#place-details").showModal();
    },

    updatePlace: function () {
      var updatePlaceParams = this.activePlace;
      axios
        .patch(
          `http://localhost:3000/places/${this.activePlace.id}`,
          updatePlaceParams
        )
        .then((response) => {
          console.log("Place updated", response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
        });
    },

    destroyPlace: function () {
      axios
        .delete(`http://localhost:3000/places/${this.activePlace.id}`)
        .then((response) => {
          console.log("Place deleted", response.data);
          var index = this.places.indexOf(this.activePlace);
          this.places.splice(index, 1);
        });
    },
  },
};
</script>
