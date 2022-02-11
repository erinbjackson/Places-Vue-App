<script>import axios from "axios";

  export default {
    data: function () {
      return {
        message: "Restaurant Recomendations",
        places: [],
        newParams: {},
        currentPlace: {},
        editPlaceParams: {},
        errors: []
      };
    },
    created: function () {
      this.indexPlaces();
    },
    methods: {
      submit: function() {
    axios.post("/places", this.newParams)
      .then(response => {
        console.log("Success", response.data);
      })
      .catch(error => {
        this.errors = error.response.data.errors;
      });
  },
      indexPlaces: function () {
        axios.get("/places").then((response) => {
          console.log("success", response.data);
          this.places = response.data;
        })
      },
      createPlace: function () {
        axios.post("/places", this.newParams).then((response) => {
          console.log("success", response.data);
          this.places.push(response.data);
          this.newParams = {};
        })
        .catch(error => {
        this.errors = error.response.data.errors;
      });
      },
      showPlace: function(place) {
        console.log(place);
        this.currentPlace = place;
        this.editPlaceParams = place;
        document.querySelector("#placeDetails").showModal();
      },
      updatePlace: function(place) {
        axios.patch(`/places/${place.id}`, place).then ((response) => {
          console.log(response.data);
        })
        .catch(error => {
        this.errors = error.response.data.errors;
      });
      },
      destroyPlace: function(place) {
        axios.delete(`/places/${place.id}`).then((response) => {
          console.log("success", response.data);
          var index = this.places.indexOf(place);
          this.places.splice(index, 1);
        });
      }

    }
  };
</script>

<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <ul>
	<li v-for="error in errors" v-bind:key="error.id">{{ error }}</li>
</ul>
    <div v-for=" place in places " v-bind:key="place.id">
    <h3>{{place.name}}</h3>
    <button v-on:click="showPlace(place)">More Info</button>
    <dialog id="placeDetails">
      <form method="dialog">
        <h2>{{currentPlace.name}}</h2>
      <p>{{currentPlace.address}}</p>
      <input type="text" placeholder="name" v-model="editPlaceParams.name"><br>
      <input type="text" placeholder="address" v-model="editPlaceParams.address"><br>
      <br>
      
      <button v-on:click="updatePlace(currentPlace)">Update</button><button v-on:click="destroyPlace(currentPlace)">Delete</button>
      <button>Close </button>
      </form>
    </dialog>
</div>
    <h2>Add a new place</h2>
    <input type="text" v-model="newParams.name" placeholder="Name"><br>
    <input type="text" v-model="newParams.address" placeholder="Address"><br>
    <button v-on:click="createPlace()">Create</button>
  
    
  </div>
</template>

<style></style>
