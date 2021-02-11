<template>
  <div class="home">
    <h1>New Place</h1>
    <div>
      Name: <input type="text" v-model="newName"/> <br/>
      Address: <input type="text" v-model="newAddress"/> <br/>
      <button v-on:click="createPlace()">Create</button>
    </div>
    <h1>All Places</h1>
    <div v-for="place in places" v-bind:key="place.id">
    <h2> {{ place.name }} </h2>
    <p> Address: {{ place.address }} </p>
    <button v-on:click="showPlace(place)">Location Info</button>
  </div>
  <dialog>
    <form method="dialog">
      <h2>Location Info</h2>
      <p> Name: <input type="text" v-model="currentPlace.name"/></p>
      <p> Address: <input type="text" v-model="currentPlace.address"/></p>
      <button v-on:click="updatePlace(currentPlace)">Update</button>
      <button v-on:click="destroyPlace(currentPlace)">Delete</button>
      <button>Close</button>
    </form>
  </dialog>
  </div>
</template>

<style>
</style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      places: [],
      currentPlace: {},
      errors: [],
      newName: "",
      newAddress: "",
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("/api/places").then((response) => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    createPlace: function () {
      var params = {
        name: this.newName,
        address: this.newAddress,
      };
      axios
        .post("/api/places", params)
        .then((response) => {
          console.log(response.data);
          this.places.push(response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function (place) {
      console.log(place);
      this.currentPlace = place;
      document.querySelector("dialog").showModal();
    },
    updatePlace: function (place) {
      var params = {
        name: place.name,
        address: place.address,
      };
      axios
        .patch(`/api/places/${place.id}`, params)
        .then((response) => {
          console.log("Updated!", response.data);
        })
        .catch((error) => {
          console.log(error.response.data);
        });
    },
    destroyPlace: function (place) {
      axios.delete(`/api/places/${place.id}`).then((response) => {
        console.log("Place deleted!", response.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>