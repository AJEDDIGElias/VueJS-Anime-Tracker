<script setup>
import { ref, computed, onMounted } from 'vue'

//Variable qui serviront à stocker les données
const query = ref('')
const my_anime = ref([])
const search_result = ref([])

//Variable pour la liste croissante des animes
const my_anime_asc = computed(() => {
  return my_anime.value.sort((a, b) => {
    return a.title.localeCompare(b.title)
  })
})

//Fonction de recherche d'anime
const searchAnime = () => {
  const url = `https://api.jikan.moe/v4/anime?q=${query.value}`
  fetch(url)
  .then(res => res.json())
  .then(res => {
    search_result.value = res.data
  })
}

//Fonction qui va vérifier que si la recherche est vide, on vide le resultat 
const handleInput = e => {
  if(!e.target.value){
    search_result.value = []
  }
}

//Fonction qui ajoute des animes a la watchlist
const addAnime = anime => {
  search_result.value = []
  query.value = ''
  my_anime.value.push({
    id: anime.mal_id,
    image: anime.images.jpg.image_url,
    total_episodes: anime.episodes,
    watched_episodes: 0

  })

  localStorage.setItem('my-anime', JSON.stringify(my_anime.value))
}

const increaseWatch = anime => {
  anime.watched_episodes++
  localStorage.setItem('my-anime', JSON.stringify(my_anime.value))
}

const decreaseWatch = anime => {
  anime.watched_episodes--
  localStorage.setItem('my-anime', JSON.stringify(my_anime.value))
}


</script>

<template>
  <main>Hello World !</main>
</template>

<style>

</style>
