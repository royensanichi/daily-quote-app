<template>
  <div :class="[darkMode ? 'dark' : 'light', 'container']">
    <button @click="toggleDarkMode" class="secondary" style="position: absolute; top: 1rem; right: 1rem;">
      {{ darkMode ? 'Light Mode' : 'Dark Mode' }}
    </button>

    <div class="quote-box">
      <p class="quote">"{{ quote.content }}"</p>
      <p class="author">— {{ quote.author }}</p>
      <div>
        <button @click="fetchQuote" class="primary">New Quote</button>
        <button @click="toggleFavorite" class="secondary">
          {{ isFavorite ? 'Unfavorite' : 'Favorite' }}
        </button>
      </div>
    </div>

    <div v-if="favorites.length" class="favorites">
      <h2>Favorites</h2>
      <ul>
        <li v-for="(fav, i) in favorites" :key="i">"{{ fav.content }}" — <strong>{{ fav.author }}</strong></li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'

const quote = ref({ content: '', author: '' })
const darkMode = ref(false)
const favorites = ref(JSON.parse(localStorage.getItem('favorites')) || [])

const fetchQuote = async () => {
  const res = await fetch('https://api.api-ninjas.com/v1/quotes',
  {
    headers: { 'X-Api-Key': 'OwTV0suhGN4ZyZ2o5LnmeA==ebZJyqE5UDOPiNI2'
 }
  })
  const data = await res.json()
  // const data = {
  //   content: "He that will not apply new remedies must expect new evils.",
  //   author: "Bacon, Francis"
  // }
  console.log(data)
  data.error ? 
    quote.value = { content: "INVALID API Key. Renew your API KEY by sign-up at api.api-ninjas.com .and failed at something doesn't mean you're a failure, there's Error, Keep trying to find another quote", author: "SanichiDev"}:
quote.value = { content: data[0].quote, author: data[0].author }
}

const toggleDarkMode = () => {
  darkMode.value = !darkMode.value
}

const isFavorite = computed(() => {
  return favorites.value.some(q => q.content === quote.value.content)
})

const toggleFavorite = () => {
  const index = favorites.value.findIndex(q => q.content === quote.value.content)
  if (index >= 0) {
    favorites.value.splice(index, 1)
  } else {
    favorites.value.push({ ...quote.value })
  }
  localStorage.setItem('favorites', JSON.stringify(favorites.value))
}

onMounted(fetchQuote)
</script>
