<script setup lang="ts">
import { computed } from 'vue'
import { translateAbility } from '../utils/translations'

export interface Pokemon {
  id: number
  name: string
  image: string
  types: string[]
  hp: number
  height: number
  weight: number
  baseExperience: number
  abilities: string[]
}

const props = defineProps<{
  pokemon: Pokemon
}>()

const typeColors: Record<string, string> = {
  normal: '#a8a878',
  fire: '#f08030',
  water: '#6890f0',
  electric: '#f8d030',
  grass: '#78c850',
  ice: '#98d8d8',
  fighting: '#c03028',
  poison: '#a040a0',
  ground: '#e0c068',
  flying: '#a890f0',
  psychic: '#f85888',
  bug: '#a8b820',
  rock: '#b8a038',
  ghost: '#705898',
  dragon: '#7038f8',
  dark: '#705848',
  steel: '#b8b8d0',
  fairy: '#ee99ac',
}

const typesEs: Record<string, string> = {
  normal: 'Normal',
  fire: 'Fuego',
  water: 'Agua',
  electric: 'Eléctrico',
  grass: 'Planta',
  ice: 'Hielo',
  fighting: 'Lucha',
  poison: 'Veneno',
  ground: 'Tierra',
  flying: 'Volador',
  psychic: 'Psíquico',
  bug: 'Bicho',
  rock: 'Roca',
  ghost: 'Fantasma',
  dragon: 'Dragón',
  dark: 'Siniestro',
  steel: 'Acero',
  fairy: 'Hada',
}

const mainColor = computed(() => typeColors[props.pokemon.types[0] ?? 'normal'] ?? '#a8a878')
const secondColor = computed(() => typeColors[props.pokemon.types[1] ?? props.pokemon.types[0] ?? 'normal'] ?? '#a8a878')

const cardStyle = computed(() => ({
  '--c1': mainColor.value,
  '--c2': secondColor.value,
}))

const pad = (n: number) => String(n).padStart(3, '0')
const formatName = (s: string) => s.replace(/-/g, ' ').toUpperCase()
</script>

<template>
  <article class="retro-card" :style="cardStyle">
    <div class="card-inner">
      <header class="card-header">
        <h2 class="pokemon-name">{{ formatName(pokemon.name) }}</h2>
        <div class="hp-box">
          <span class="hp-label">PS</span>
          <span class="hp-value">{{ pokemon.hp }}</span>
        </div>
      </header>

      <div class="art-frame">
        <div class="art-bg">
          <img :src="pokemon.image" :alt="pokemon.name" loading="lazy" />
        </div>
      </div>

      <div class="info-strip">
        <span class="pokemon-id">N.º {{ pad(pokemon.id) }}</span>
        <div class="types-container">
          <span
            v-for="t in pokemon.types"
            :key="t"
            class="type-pill"
            :style="{ backgroundColor: typeColors[t] ?? '#7f8c8d' }"
          >
            {{ typesEs[t] ?? t }}
          </span>
        </div>
      </div>

      <ul class="moves-list">
        <li v-for="(ab, index) in pokemon.abilities" :key="ab" class="move-item">
          <span class="move-energy">{{ index + 1 }}</span>
          <span class="move-title">{{ translateAbility(ab) }}</span>
        </li>
      </ul>

      <footer class="specs-bar">
        <div class="spec-col">
          <span class="spec-label">ALTURA</span>
          <span class="spec-data">{{ (pokemon.height / 10).toFixed(1) }} m</span>
        </div>
        <div class="spec-divider"></div>
        <div class="spec-col">
          <span class="spec-label">PESO</span>
          <span class="spec-data">{{ (pokemon.weight / 10).toFixed(1) }} kg</span>
        </div>
        <div class="spec-divider"></div>
        <div class="spec-col">
          <span class="spec-label">EXP</span>
          <span class="spec-data">{{ pokemon.baseExperience }}</span>
        </div>
      </footer>
    </div>
  </article>
</template>

<style scoped>
.retro-card {
  container-type: inline-size;
  aspect-ratio: 63 / 88;
  padding: 5px;
  border-radius: 8px;
  background: #e6c86e;
  border: 2px solid #b8932f;
  box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.2);
  transition: transform 0.2s ease;
  user-select: none;
}

.retro-card:hover {
  transform: scale(1.05);
}

.card-inner {
  height: 100%;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  gap: 2.8cqw;
  padding: 3.5cqw;
  border-radius: 4px;
  border: 1px solid #73591c;
  background: linear-gradient(
    165deg,
    color-mix(in srgb, var(--c1) 40%, #ffffff) 0%,
    color-mix(in srgb, var(--c2) 25%, #f4f0e6) 100%
  );
  color: #111111;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 2px solid rgba(0, 0, 0, 0.2);
  padding-bottom: 1.5cqw;
}

.pokemon-name {
  margin: 0;
  font-family: 'Rajdhani', sans-serif;
  font-size: 7.5cqw;
  font-weight: 700;
  color: #000;
}

.hp-box {
  display: flex;
  align-items: baseline;
  gap: 1cqw;
  font-family: 'Press Start 2P', monospace;
}

.hp-label {
  font-size: 4cqw;
  color: #c00000;
  font-weight: bold;
}

.hp-value {
  font-size: 6cqw;
  color: #c00000;
  font-weight: bold;
}

.art-frame {
  flex: 1;
  min-height: 0;
  padding: 2cqw;
  background: #d8ba5b;
  border-radius: 4px;
  border: 1px solid #826620;
  display: flex;
}

.art-bg {
  flex: 1;
  border-radius: 3px;
  background: radial-gradient(
    circle at 50% 50%,
    #ffffff 25%,
    color-mix(in srgb, var(--c1) 30%, #fff) 100%
  );
  display: grid;
  place-items: center;
  border: 1px solid rgba(0, 0, 0, 0.25);
  overflow: hidden;
}

.art-bg img {
  max-width: 86%;
  max-height: 92%;
  object-fit: contain;
}

.info-strip {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1cqw 2cqw;
  background: rgba(255, 255, 255, 0.85);
  border: 1px solid rgba(0, 0, 0, 0.25);
  border-radius: 3px;
}

.pokemon-id {
  font-family: 'Press Start 2P', monospace;
  font-size: 3.6cqw;
  color: #222;
}

.types-container {
  display: flex;
  gap: 1.5cqw;
}

.type-pill {
  color: #ffffff;
  font-size: 4.2cqw;
  font-weight: 700;
  padding: 0.6cqw 2.5cqw;
  border-radius: 3px;
  text-transform: uppercase;
  border: 1px solid rgba(0, 0, 0, 0.3);
}

.moves-list {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
  flex-direction: column;
  gap: 1.8cqw;
}

.move-item {
  display: flex;
  align-items: center;
  gap: 2.5cqw;
  padding: 1.6cqw 2cqw;
  background: rgba(255, 255, 255, 0.85);
  border-radius: 3px;
  border: 1px solid rgba(0, 0, 0, 0.2);
}

.move-energy {
  width: 6.5cqw;
  height: 6.5cqw;
  border-radius: 50%;
  background: var(--c1);
  color: #ffffff;
  display: grid;
  place-items: center;
  font-size: 4cqw;
  font-weight: bold;
  border: 1px solid rgba(0, 0, 0, 0.3);
  flex-shrink: 0;
}

.move-title {
  font-size: 5.5cqw;
  font-weight: 700;
  color: #111;
}

.specs-bar {
  display: flex;
  justify-content: space-around;
  align-items: center;
  background: rgba(255, 255, 255, 0.75);
  border: 1px solid rgba(0, 0, 0, 0.2);
  border-radius: 3px;
  padding: 1.2cqw 1cqw;
}

.spec-col {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.3cqw;
}

.spec-label {
  font-family: 'Press Start 2P', monospace;
  font-size: 3cqw;
  color: #444;
}

.spec-data {
  font-size: 4.8cqw;
  font-weight: 700;
  color: #111;
}

.spec-divider {
  width: 1px;
  height: 4.5cqw;
  background: rgba(0, 0, 0, 0.25);
}
</style>
