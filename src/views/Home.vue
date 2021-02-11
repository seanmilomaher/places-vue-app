<template>
  <div class="home">

    <h1>Add Place</h1>
    <div>
      <p v-for="error in createErrors" :key="error">{{ error }}</p>
      <p>Name: <input type="text" v-model="newPlaceName"></p>
      <p>Address: <input type="text" v-model="newPlaceAddress"></p>
      <button v-on:click="placesCreate">Create Place</button>
    </div>

    <h1>All Places</h1>
    <div v-for="place in places" :key="place.id">
      <p>Name: {{place.name}}</p>
      <p>Address: {{place.address}}</p>
      <button v-on:click="placesShow(place)">More Info</button><br>
    </div>

    <dialog>
      <form method="dialog">
        <h2>Place Info</h2>
        <p v-for="error in updateErrors" :key="error">{{ error }}</p>
        <p>Name: <input type="text" v-model="currentPlace.name"></p>
        <p>Address: <input type="text" v-model="currentPlace.address"></p>
        <button>Close</button>
        <button v-on:click="placesUpdate(currentPlace)">Update</button>
        <button v-on:click="placesDestroy(currentPlace)">Delete</button>
      </form>
    </dialog>

  </div>
</template>

<style></style>

<script>
import axios from "axios";

export default {
  data: function () {
    return {
      places: [],
      newPlaceName: "",
      newPlaceAddress: "",
      currentPlace: {},
      createErrors: [],
      updateErrors: []
    };
  },
  created: function () {
    this.placesIndex();
  },
  methods: {
    placesIndex: function() {
      axios
      .get("/api/places")
      .then(response => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    placesCreate: function () {
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress
      };
      axios
      .post("/api/places", params)
      .then(response => {
        console.log(response.data);
        this.places.push(response.data);
      })
      .catch(error => {
        console.log(error.response.data.errors);
        this.createErrors = error.response.data.errors;
      })
    },
    placesShow: function (inputPlace) {
      console.log(inputPlace);
      this.currentPlace = inputPlace;
      document.querySelector("dialog").showModal(); 
    },
    placesUpdate: function (inputPlace) {
      var params = {
        name: inputPlace.name,
        address: inputPlace.address
      };
      axios
      .patch(`/api/places/${inputPlace.id}`, params)
      .then(response => {
        console.log("You did it!", response.data);
      })
      .catch(error => {
        console.log(error.response.data.errors);
        this.updateErrors = error.response.data.errors;
      })
    },
    placesDestroy: function(inputPlace) {
      axios
      .delete(`api/places/${inputPlace.id}`)
      .then(response => {
        console.log("Wooooooo", response.data);
        var index = this.places.indexOf(inputPlace);
        this.places.splice(index, 1);
      })
    }
  }
};
</script>