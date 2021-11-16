<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <p>Place Name:</p>
    <input type="string" v-model="newPlaceParams.name" />
    <p>Adress:</p>
    <input type="text" v-model="newPlaceParams.adress" />
    <p></p>
    <ul>
      <li v-for="error in errors" v-bind:key="error">{{ error }}</li>
    </ul>
    <p></p>
    <button v-on:click="createPlace()">Create Place!</button>
    <div v-for="place in places" v-bind:key="place.id">
      <h2>{{ place.name }}</h2>
      <p>{{ place.adress }}</p>
      <button v-on:click="showPlace(place)">Modify</button>
      <dialog id="place-details">
        <form method="dialog">
          <h2>Place Modification:</h2>
          <p>
            Name:
            <input type="text" v-model="currentPlace.name" />
          </p>
          <p>
            Adress:
            <input type="text" v-model="currentPlace.adress" />
          </p>

          <button v-on:click="updatePlace(currentPlace)">Update Place</button>
          <button v-on:click="destroyPlace(currentPlace)">Delete Place</button>
          <button>Close</button>
        </form>
      </dialog>
      <p>------------------------------------</p>
    </div>
  </div>
</template>

<style scoped>
h1 {
  text-align: center;
  border: 12px solid;
  border-image: linear-gradient(135deg, #ff0000 0%, #49ff33 25%, #3364ff 50%, #f6ff33 75%, #ff00a8 100%) 1;
}

img {
  width: 350px;
}

.home {
  margin: 0 auto;
  padding: 10px 25px;
  border: 3px solid;
  border-image: linear-gradient(135deg, #ff0000 0%, #49ff33 25%, #3364ff 50%, #f6ff33 75%, #ff00a8 100%) 1;

  background: rgb(228, 226, 226);
}
</style>

<script>
const axios = require("axios");

export default {
  data: function () {
    return {
      message: "The Places You'll Go",
      places: [],
      newPlaceParams: {},
      currentPlace: {},
      errors: [],
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios
        .get("http://localhost:3000/places")
        .then((response) => {
          this.places = response.data;
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    createPlace: function () {
      axios
        .post("http://localhost:3000/places", this.newPlaceParams)
        .then((response) => {
          console.log(response.data);
          this.places.push(response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
      this.newPlaceParams.title = "";
      this.newPlaceParams.plot = "";
      this.newPlaceParams.year = "";
    },
    showPlace: function (place) {
      this.currentPlace = place;
      console.log(place);
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function (place) {
      axios
        .patch("http://localhost:3000/places/" + place.id, place)
        .then((response) => {
          console.log("Place Created", response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    destroyPlace: function (place) {
      axios
        .delete("http://localhost:3000/places/" + place.id)
        .then((response) => {
          console.log("Place Destroyed", response.data);
          var index = this.places.indexOf(place);
          this.places.splice(index, 1);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
  },
};
</script>
