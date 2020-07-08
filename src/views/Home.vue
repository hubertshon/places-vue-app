<template>
  <div class="home">

    <form>
      Name<input type="text" v-model="newPlaceName"></input><br>
      Address<input type="text" v-model="newPlaceAddress"></input><br>
    <button v-on:click="createPlace()">Create Place</button>
    </form>

    <ul>
      <li v-for="error in newErrors">{{ error }}</li>
    </ul>

    <div v-for="place in places">
      <h3>{{ place.name }}</h3>
      <p>id: {{ place.id }}</p>
      <p>{{ place.address }}</p>
      <button v-on:click="showPlace(place)">More Info</button>
    </div>

    <dialog id="place-details">
      <p>Name: <input type="text" v-model="currentPlace.name"></p>
      <p>Address: <input type="text" v-model="currentPlace.address"></p>
      <button v-on:click="updatePlace(currentPlace)">Update</button>
      <button v-on:click="deletePlace(currentPlace)">Delete Place</button>
    </dialog>

  </div>
</template>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      places: [],
      newErrors: [],
      newPlaceName: "",
      newPlaceAddress: "",
      currentPlace: ""
    };
  },

  created: function() {
    this.indexPlaces();
  },

  methods: {
    indexPlaces: function() {
      axios.get("http://localhost:3000/api/places").then(response => {
        console.log("All Places", response.data);
        this.places = response.data;
      });
    },

    showPlace: function(place) {
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },

    createPlace: function() {
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress
      };

      axios
        .post("/api/places", params)
        .then(response => {
          console.log("Created", response.data);
          this.places.push(response.data);
        })
        .catch(error => {
          console.log(error.response.data.errors);
          this.newErrors = error.response.data.errors;
        });
    },
    updatePlace: function(place) {
      var params = {
        name: place.name,
        address: place.address
      };

      axios
        .patch(`/api/places/${place.id}`, params)
        .then(response => {
          console.log("Place Updated");
        })
        .catch(error => {
          console.log(error.response.data.errors);
          this.newErrors = error.response.data.errors;
        });
    },
    deletePlace: function(place) {
      axios.delete(`/api/places/${place.id}`).then(response => {
        console.log("Place Deleted");
      });
      var index = this.places.indexOf(place);
      this.places.splice(index, 1);
    }
  }
};
</script>
