<template>
    <div class="modal-overlay modal-dialog modal-dialog-centered modal-dialog-scrollable" @click="closeModal">
      <div class="modal-content ">
        <div class="modal-header">
          <h1>{{ movieDetails.title }}</h1>
          <button type="button" class="btn btn-primary" @click="closeModal">X</button>
        </div>
        <div class="modal-body ">
          <div class="movie-details">
            <img :src="getImageUrl(movieDetails.poster_path)" alt="Movie Poster" />
            <div class="movie-info">
              <h4>Popularity: {{ movieDetails.popularity }}</h4>
              <h4>Overview:</h4>
              <p>{{ movieDetails.overview }}</p>
              <h4>Genres:</h4>
              <p>{{ getGenres }}</p>
              <h4>Starring:</h4>
              <p>{{ getStarring }}</p>
              <h4>Languages:</h4>
              <p>{{ getLanguages }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  export default {
    props:{
        movieDetails: {
    type: Object,
    required: true
  }
    },
    data() {
        return {
            movieDetailsData: null
        };
    },
    computed: {
        getGenres() {
            if(this.movieDetails && this.movieDetails.genres && this.movieDetails.genres.length > 0) {
                return this.movieDetails.genres.map((genre) => genre.name).join(', ');
            }
            return 'N/A';
        },
        getStarring() {
            if(this.movieDetails && this.movieDetails.credits && this.movieDetails.credits.cast && this.movieDetails.credits.cast.length > 0){
                return this.movieDetails.credits.cast.map((actor) => actor.name).join(', ');
            }
            return 'N/A';
        },
        getLanguages() {
            if(this.movieDetails && this.movieDetails.spoken_languages){
                return this.movieDetails.spoken_languages.map((language) => language.name).join(', ');
            }
            return 'N/A';
        },
    },
    methods: {
      closeModal() {
        this.$emit('close');
      },
      getImageUrl(posterPath){
        if(posterPath){
            return `https://image.tmdb.org/t/p/w200${posterPath}`;
        }
        return 'https://via.placeholder.com/500x750';
      },
     getMovieDetails() {
        axios
        .get(`https://api.themoviedb.org/3/movie/${this.movieDetails.id}`,{
            params: {
            api_key: '04c1c54369f819831f199fc72863d3af',
            append_to_response: 'credits',
          },
        })
        .then((response) => {
            this.movieDetailsData = response.data;
        })
        .catch((error) => {
            console.error(error);
        })
     }
    },
    created(){
        this.getMovieDetails();
    },
  };
  </script>
  

  <style scoped>
  *{
    background-color: #717171;
    color: rgb(255, 255, 255);
  }
  .modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    align-items: center;
    justify-content: center;
  }
  
  .modal-content {
    background-color: #fff;
    border-radius: 5px;
    padding: 0.5rem;
    width: 80%;
  }
  
  .modal-header {
    display: flex;
    align-items: center;
    padding-left: 1rem;
    /* justify-content: space-between; */
  }
  
  .modal-body {
    display: flex;
    gap: 20px;
  }
  .movie-details{
    display: flex;
    justify-content: space-between;
  } 
  .movie-details img {
   padding: 1rem;
    width: 342px;
    height: 513px;
  }
  
  .close-button {
    border: none;
    background-color: #ccc;
    padding: 5px 10px;
    border-radius: 5px;
    cursor: pointer;
  }
  </style>
  