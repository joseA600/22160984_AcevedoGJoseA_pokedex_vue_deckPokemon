<script setup lang="ts">
import { ref, onMounted } from 'vue'
import axios from 'axios'
import PokemonCard, { type Pokemon } from './components/PokemonCard.vue'

const TOTAL_CARTAS = 40
const MAX_ID = 1025

const pokemons = ref<Pokemon[]>([])
const loading = ref(false)
const error = ref<string | null>(null)

const generateRandomIds = (count: number): number[] => {
  const ids = new Set<number>()
  while (ids.size < count) {
    ids.add(Math.floor(Math.random() * MAX_ID) + 1)
  }
  return [...ids]
}

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
      data.sprites.front_default ??
      `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${data.id}.png`,
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
    const ids = generateRandomIds(TOTAL_CARTAS)
    const responses = await Promise.all(
      ids.map((id) => axios.get(`https://pokeapi.co/api/v2/pokemon/${id}`)),
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
      <button class="new-deck-btn" :disabled="loading" @click="fetchDeck">
        {{ loading ? 'Generando...' : 'Nuevo mazo' }}
      </button>
    </header>

    <main class="content-area">
      <div v-if="loading" class="status">
        <p>Cargando 40 cartas aleatorias...</p>
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
  display: flex;
  justify-content: space-between;
  align-items: center;
}

h1 {
  margin: 0;
  font-size: 1.8rem;
  font-weight: bold;
  color: #111111;
}

.new-deck-btn {
  padding: 8px 16px;
  font-size: 0.95rem;
  font-weight: bold;
  background-color: #ffffff;
  color: #111111;
  border: 2px solid #111111;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.15s ease, color 0.15s ease;
}

.new-deck-btn:hover:not(:disabled) {
  background-color: #111111;
  color: #ffffff;
}

.new-deck-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
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
  .header {
    flex-direction: column;
    gap: 10px;
    align-items: flex-start;
  }
  h1 {
    font-size: 1.4rem;
  }
  .cards-grid {
    grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
    gap: 10px;
  }
}
</style>
