<template>
    <div class="container w-50 p-3">
        <div class="row">
            <div class="col">
                <h1 class="h1">TMDb UI – Home</h1>
            </div>
      <div class="mb-3 row">
        <label for="title" class="form-label">Search for a Title:</label>
        <input type="text"  class="form-control col-sm-10" v-model="searchQuery" @input="searchMovies"/>
      </div>
      <Modal v-if="movieDetails" :movie-details="movieDetails" @close="movieDetails = null" />
      <div class="recent-movies" v-if="showPopularMovies">
        <h3>Popular Now</h3>
        <ul>
          <li v-for="movie in popularMovies" :key="movie.id">
            <div class="movie-item">
              <img :src="getImageUrl(movie.poster_path)" alt="Movie Poster"  @click="showMovieDetails(movie)"/>
              <div class="movie-details">
                <h4  @click="showMovieDetails(movie)">{{ movie.title }}</h4>
              </div>
            </div>
          </li>
        </ul>
      </div>
  
      <div class="search-results" v-if="!showPopularMovies">
        <ul>
          <li v-for="movie in filteredMovies" :key="movie.id">
            <div class="movie-item">
              <img :src="getImageUrl(movie.poster_path)" alt="Movie Poster" @click="showMovieDetails(movie)"/>
              <div class="movie-details">
                <h4 @click="showMovieDetails(movie)">{{ movie.title }}</h4>
                <p>Release Date: {{ movie.release_date }}</p>
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
    </div>
  
  </template>
  
  
  
  <script>
  import axios from 'axios';
  import Modal from './Modal.vue';
  
  export default {
    components: {
    Modal,
  },
  data() {
    return {
      movies: [],
      searchQuery: '',
      popularMovies: [],
      showPopularMovies: true,
      movieDetails: null,
    };
  },
  
  computed:{
    filteredMovies(){
        if(this.searchQuery.length >= 1) {
            const keyword = this.searchQuery.toLowerCase().trim();
            return this.movies.filter(movie => movie.title.toLowerCase().includes(keyword));
        }else{
            return [];
        }
    }
  },

  methods: {
    searchMovies() {
       if(this.searchQuery.length >= 1) {
        axios
       .get('https://api.themoviedb.org/3/search/movie', {
        params: {
            api_key:'04c1c54369f819831f199fc72863d3af',
            query: encodeURIComponent(this.searchQuery.trim()),
        },
       })
       .then((response) => {
        this.movies = response.data.results;
          this.showPopularMovies = false;
       })
       .catch((error) => {
        console.error(error);
       })
       }else{
        this.showPopularMovies = true;
        this.movies = [];
       }
    },
    getImageUrl(posterPath){
        if(posterPath){
            return `https://image.tmdb.org/t/p/w300${posterPath}`;
        }
        return 'https://via.placeholder.com/300x250';
    },
    showMovieDetails(movie) {
       axios
       .get(`https://api.themoviedb.org/3/movie/${movie.id}`,{
        params: {
          api_key: '04c1c54369f819831f199fc72863d3af',
          append_to_response: 'credits', // 根据需求决定是否需要额外的响应数据
        },
       })
       .then((response) => {
        this.movieDetails = response.data;
       })
       .catch((error) => {
        console.log(error);
       });
    },
   
  },
  created(){
    axios
      .get('https://api.themoviedb.org/3/movie/popular', {
        params: {
          api_key: '04c1c54369f819831f199fc72863d3af',
        },
      })
      .then((response) => {
        this.popularMovies = response.data.results;
      })
      .catch((error) => {
        console.error(error);
      });
  }
};
  </script>

<style>
*{
    background-color: black;
    color:#fff
}
.recent-movies {
  margin-bottom: 20px;
}

.recent-movies h3 {
  font-size: 18px;
  margin-bottom: 10px;
}

.search-results h3 {
  font-size: 18px;
  margin-bottom: 10px;
}

.search-results ul {
  margin-top: 20px;
}


</style>