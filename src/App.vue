<script setup lang="ts">
import { ref, onMounted } from 'vue'
import axios from 'axios'
import PokemonCard, { type Pokemon } from './components/PokemonCard.vue'

const POKEMON_IDS = [
  1, 4, 7, 25, 39, 52, 54, 58, 59, 63,
  65, 68, 92, 94, 95, 104, 123, 129, 130, 131,
  132, 133, 134, 135, 136, 143, 144, 145, 146, 149,
  150, 151, 152, 155, 158, 175, 196, 197, 248, 249
]

const pokemons = ref<Pokemon[]>([])
const loading = ref(false)
const error = ref<string | null>(null)

const toCard = (data: any): Pokemon => {
  const abilities: string[] = data.abilities.map((a: any) => a.ability.name)
  const moves: string[] = data.moves.map((m: any) => m.move.name)
  while (abilities.length < 2 && moves.length > 0) {
    const move = moves.splice(Math.floor(Math.random() * moves.length), 1)[0]
    if (move && !abilities.includes(move)) {
      abilities.push(move)
    }
  }

  return {
    id: data.id,
    name: data.name,
    image:
      data.sprites.other?.['official-artwork']?.front_default ??
      data.sprites.other?.home?.front_default ??
      data.sprites.front_default,
    types: data.types.map((t: any) => t.type.name),
    hp: data.stats.find((s: any) => s.stat.name === 'hp')?.base_stat ?? 50,
    height: data.height,
    weight: data.weight,
    baseExperience: data.base_experience ?? 100,
    abilities: abilities.slice(0, 2),
  }
}

const fetchDeck = async () => {
  loading.value = true
  error.value = null
  try {
    const responses = await Promise.all(
      POKEMON_IDS.map((id) => axios.get(`https://pokeapi.co/api/v2/pokemon/${id}`)),
    )
    pokemons.value = responses.map((r) => toCard(r.data))
  } catch (err) {
    error.value = 'Error al cargar las cartas de la PokéAPI.'
    console.error(err)
  } finally {
    loading.value = false
  }
}

onMounted(() => {
  fetchDeck()
})
</script>

<template>
  <div class="page">
    <header class="header">
      <h1>Pokédex 40 cartas</h1>
    </header>

    <main class="content-area">
      <div v-if="loading" class="status">
        <p>Cargando 40 cartas...</p>
      </div>

      <div v-else-if="error" class="status error">
        <p>{{ error }}</p>
        <button class="retry-btn" @click="fetchDeck">Reintentar</button>
      </div>

      <section v-else class="cards-grid">
        <PokemonCard
          v-for="p in pokemons"
          :key="p.id"
          :pokemon="p"
        />
      </section>
    </main>
  </div>
</template>

<style scoped>
.page {
  min-height: 100vh;
  background-color: #ffffff;
  color: #111111;
  font-family: Arial, sans-serif;
  padding: 20px;
}

.header {
  max-width: 1400px;
  margin: 0 auto 20px;
}

h1 {
  margin: 0;
  font-size: 1.8rem;
  font-weight: bold;
  color: #111111;
}

.content-area {
  max-width: 1400px;
  margin: 0 auto;
}

.status {
  text-align: center;
  padding: 40px 20px;
  font-size: 1rem;
}

.status.error {
  color: #c00000;
}

.retry-btn {
  margin-top: 10px;
  padding: 6px 14px;
  cursor: pointer;
}

.cards-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
  gap: 16px;
}

@media (max-width: 600px) {
  h1 {
    font-size: 1.4rem;
  }
  .cards-grid {
    grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
    gap: 10px;
  }
}
</style>
