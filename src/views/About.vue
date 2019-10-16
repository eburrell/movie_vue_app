<template>
  <div class="Movies">
    <h1>New Movie</h1>
    <div>
      Title:
      <input type="text" v-model="newMovieTitle" />
      Year:
      <input type="text" v-model="newMovieYear" />
      Plot:
      <input type="text" v-model="newMoviePlot" />
      <button v-on:click="createMovie()">Create Movie</button>
    </div>
    <h1>All Movies</h1>
    <div v-for="movie in movies">
      <h2>{{ movie.title }}</h2>
      <button v-on:click="showMovie(movie)">Show more</button>
      <div v-if="currentMovie === movie">
        <p>Year: {{ movie.year }}</p>
        <p>Plot {{ movie.plot }}</p>
        <div>
          Title:
          <input type="text" v-model="movie.title" />
          Year:
          <input type="text" v-model="movie.year" />
          Plot:
          <input type="text" v-model="movie.plot" />
          <button v-on:click="updateMovie(movie)">Update Movie</button>
          <button v-on:click="destroyMovie(movie)">Delete Movie</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      movies: [],
      currentMovie: {},
      newMovieTitle: "",
      newMovieYear: "",
      newMoviePlot: ""
    };
  },
  created: function() {
    axios.get("/api/movies").then(response => {
      this.movies = response.data;
    });
  },
  methods: {
    createMovie: function() {
      var params = {
        title: this.newMovieTitle,
        year: this.newMovieYear,
        plot: this.newMoviePlot
      };
      axios.post("/api/movies", params).then(response => {
        this.movies.push(response.data);
        this.newMovieTitle = "";
        this.newMovieYear = "";
        this.newMoviePlot = "";
      });
    },
    showMovie: function(movie) {
      if (this.currentMovie === movie) {
        this.currentMovie = {};
      } else {
        this.currentMovie = movie;
      }
    },
    updateMovie: function(movie) {
      var params = {
        title: movie.title,
        year: movie.year,
        plot: movie.plot
      };
      axios.patch("/api/movies/" + movie.id, params).then(response => {
        this.currentMovie = {};
      });
    },
    destroyMovie: function(movie) {
      axios.delete("/api/movies/" + movie.id).then(response => {
        var index = this.movies.indexOf(movie);
        this.movies.splice(index, 1);
      });
    }
  }
};
</script>
