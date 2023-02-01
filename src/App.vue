<script setup>
import { ref, computed, onMounted } from 'vue'

//Variable qui serviront à stocker les données
const query = ref('')
const my_anime = ref([])
const search_result = ref([])

//Variable pour la liste croissante des animes
const my_anime_asc = computed(() => {
	return my_anime.value.sort((a, b) => {
    console.log(a)
    console.log(b)
		return a.title.localeCompare(b.title)
	})
})

//Fonction de recherche d'anime
const searchAnime = () => {
  const url = `https://api.jikan.moe/v4/anime?q=${query.value}`
  fetch(url)
  .then(res => res.json())
  .then(data => {
    search_result.value = data.data
  })
}

//Fonction qui va vérifier que si la recherche est vide, on vide le resultat 
const handleInput = (e) => {
  if(!e.target.value){
    search_result.value = []
  }
}

//Fonction qui ajoute des animes a la watchlist
const addAnime = (anime) => {
  search_result.value = []
  query.value = ''
  my_anime.value.push({
    id: anime.mal_id,
    title: anime.title,
    image: anime.images.jpg.image_url,
    total_episodes: anime.episodes,
    watched_episodes: 0

  })

  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

//Fonction pour incrementer le nombre d'épisode vu
const increaseWatch = (anime) => {
  anime.watched_episodes++
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

//Fonction pour décrémenter le nombre d'épisode vu
const decreaseWatch = (anime) => {
  anime.watched_episodes--
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

//Fonction qui au chargement vérifie si le localStorage a des données sinon initialise
onMounted(() => {
  my_anime.value = JSON.parse(localStorage.getItem('my_anime')) || []
})


</script>

<template>
  <main>
  <h1>My Anime Tracker</h1>
  <form @submit.prevent="searchAnime">
    <input type="text" placeholder="Search an anime..." v-model="query" @input="handleInput" />
    <button type="submit" class="button">Search</button>
  </form>

  <div class="results" v-if="search_result.length > 0">
    <div class="result" v-for="anime in search_result">
      <img :src="anime.images.jpg.image_url" />
      <div class="details">
        <h3>{{ anime.title }}</h3>
        <p :title="anime.synopsis" v-if="anime.synopsis">{{anime.synopsis.slice(0,120)}}</p>
        <span class="flex-1"></span>
        <button class="button" @click="addAnime(anime)">Add to my list</button>
      </div>  
    </div>
  </div>

  <div class="myanime" v-if="my_anime.length > 0">
    <h2>My WatchList</h2>

    <div v-for="anime in my_anime_asc" class="anime">
      <img :src="anime.image" />
      <h3>{{ anime.title }}</h3>
      <div class="flex-1"></div>
      <span class="episodes">
        {{ anime.watched_episodes }} / {{ anime.total_episodes }}
      </span>
      <button class="button" v-if="anime.total_episodes !== anime.watched_episodes" @click="increaseWatch(anime)">+</button>
      <button class="button" v-if="anime.watched_episodes > 0" @click="decreaseWatch(anime)">-</button>

    </div>
  </div>

  </main>
</template>

<style>

* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
	font-family: 'Montserrat', sans-serif;
}

body {
	background-color: #EEE;
}

main {
	margin: 0 auto;
	max-width: 768px;
	padding: 1.5rem;
}

h1 {
	text-align: center;
	margin-bottom: 1.5rem;
}

form {
	display: flex;
	max-width: 480px;
	margin: 0 auto 1.5rem;
}

form input {
	appearance: none;
	outline: none;
	border: none;
	background: white;

	display: block;
	color: #888;
	font-size: 1.125rem;
	padding: 0.5rem 1rem;
	width: 100%;
}

.button {
	appearance: none;
	outline: none;
	border: none;
	background: none;

	cursor: pointer;
	display: block;
	padding: 0.5rem 1rem;
	background-image: linear-gradient(to right, deeppink 50%, darkviolet 50%);
	background-size: 200%;
	color: white;
	font-size: 1.125rem;
	font-weight: bold;
	text-transform: uppercase;
	transition: 0.4s;
}

.button:hover {
	background-position: right;
}

.results {
	background-color: #fff;
	border-radius: 0.5rem;
	box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
	max-height: 480px;
	overflow-y: scroll;
	margin-bottom: 1.5rem;
}

.result {
	display: flex;
	margin: 1rem;
	padding: 1rem;
	border: 1px solid #ccc;
	border-radius: 5px;
	transition: 0.4s;
}

.result img {
	width: 100px;
	border-radius: 1rem;
	margin-right: 1rem;
}

.details {
	flex: 1 1 0%;
	display: flex;
	align-items: flex-start;
	flex-direction: column;
}

.details h3 {
	font-size: 1.25rem;
	margin-bottom: 0.5rem;
}

.details p {
	font-size: 0.875rem;
	margin-bottom: 1rem;
  text-align: left;
  line-height: 1.4;
}
.details .button {
	margin-left: auto;
}

.flex-1 {
	display: block;
	flex: 1 1 0%;
}

.myanime h2 {
	color: #888;
	font-weight: 400;
	margin-bottom: 1.5rem;
}

.myanime .anime {
	display: flex;
	align-items: center;
	margin-bottom: 1.5rem;
	background-color: #FFF;
	padding: 1rem;
	border-radius: 0.5rem;
	box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
}

.anime img {
	width: 72px;
	height: 72px;
	object-fit: cover;
	border-radius: 1rem;
	margin-right: 1rem;
}

.anime h3 {
	color: #888;
	font-size: 1.125rem;
}

.anime .episodes {
	margin-right: 1rem;
	color: #888;
}

.anime .button:first-of-type {
	margin-right: 0.5rem;
}

.anime .button:last-of-type {
	margin-right: 0;
}

</style>
