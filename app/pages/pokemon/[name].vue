<script setup>
const route = useRoute()

const pokemonName = computed(() => String(route.params.name).toLowerCase())

useHead(() => ({
  title: pokemonName.value
    ? `${pokemonName.value} | Pokédex`
    : 'Detalle del Pokémon',
}))

const {
  data: pokemon,
  pending,
  error,
  refresh,
} = await useFetch(
  () => `https://pokeapi.co/api/v2/pokemon/${pokemonName.value}`,
  {
    key: () => `pokemon-${pokemonName.value}`,
    lazy: true,
    transform: (respuesta) => ({
      id: respuesta.id,
      nombre: respuesta.name,
      imagen:
        respuesta.sprites.other?.['official-artwork']?.front_default ??
        respuesta.sprites.front_default,
      tipos: respuesta.types.map((item) => item.type.name),
      altura: respuesta.height / 10,
      peso: respuesta.weight / 10,
      habilidades: respuesta.abilities.map((item) => item.ability.name),
    }),
  },
)
</script>

<template>
  <section class="page">
    <NuxtLink to="/" class="back">Volver</NuxtLink>

    <p v-if="pending" class="loading">Cargando detalle del pokémon...</p>

    <div v-else-if="error" class="error" role="alert">
      <p>No se pudo cargar la información del pokémon.</p>
      <button type="button" class="button" @click="refresh()">Reintentar</button>
    </div>

    <article v-else-if="pokemon" class="pokemon">
      <div class="pokemon__media">
        <img
          v-if="pokemon.imagen"
          :src="pokemon.imagen"
          :alt="`Imagen de ${pokemon.nombre}`"
          width="280"
          height="280"
        />
      </div>

      <div class="pokemon__content">
        <p class="pokemon__number">#{{ pokemon.id }}</p>
        <h1>{{ pokemon.nombre }}</h1>

        <div class="types" aria-label="Tipos">
          <span v-for="tipo in pokemon.tipos" :key="tipo" class="type">
            {{ tipo }}
          </span>
        </div>

        <dl class="stats">
          <div>
            <dt>Altura</dt>
            <dd>{{ pokemon.altura }} m</dd>
          </div>
          <div>
            <dt>Peso</dt>
            <dd>{{ pokemon.peso }} kg</dd>
          </div>
        </dl>

        <section class="abilities" aria-labelledby="abilities-title">
          <h2 id="abilities-title">Habilidades</h2>
          <ul>
            <li v-for="habilidad in pokemon.habilidades" :key="habilidad">
              {{ habilidad }}
            </li>
          </ul>
        </section>
      </div>
    </article>
  </section>
</template>

<style scoped>
.page {
  display: grid;
  gap: 1rem;
}

.back {
  width: fit-content;
  color: #38c1d9;
  font-weight: 700;
  text-decoration: none;
}

.back:hover {
  text-decoration: underline;
}

.loading {
  margin: 0;
}

.error {
  color: #b91c1c;
  display: grid;
  gap: 0.75rem;
  justify-items: start;
}

.error p {
  margin: 0;
}

.button {
  background: #0c172a;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  padding: 0.6rem 1rem;
}

.pokemon {
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.08);
  display: grid;
  gap: 1.5rem;
  grid-template-columns: minmax(220px, 0.8fr) 1fr;
  padding: 1.5rem;
}

.pokemon__media {
  align-items: center;
  background: #eef9fb;
  border-radius: 8px;
  display: flex;
  justify-content: center;
  min-height: 280px;
}

.pokemon__media img {
  height: auto;
  max-width: 100%;
}

.pokemon__content {
  align-content: start;
  display: grid;
  gap: 1rem;
}

.pokemon__number {
  color: #52607a;
  font-weight: 700;
  margin: 0;
}

.pokemon h1 {
  margin: 0;
  text-transform: capitalize;
}

.types {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
}

.type {
  background: #dcfce7;
  border-radius: 999px;
  color: #166534;
  font-size: 0.9rem;
  font-weight: 700;
  padding: 0.35rem 0.75rem;
  text-transform: capitalize;
}

.stats {
  display: grid;
  gap: 0.75rem;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  margin: 0;
}

.stats div {
  border: 1px solid #dbe4ef;
  border-radius: 8px;
  padding: 0.85rem;
}

.stats dt {
  color: #52607a;
  font-size: 0.85rem;
  margin-bottom: 0.25rem;
}

.stats dd {
  font-size: 1.2rem;
  font-weight: 700;
  margin: 0;
}

.abilities {
  display: grid;
  gap: 0.5rem;
}

.abilities h2 {
  font-size: 1.1rem;
  margin: 0;
}

.abilities ul {
  margin: 0;
  padding-left: 1.25rem;
}

.abilities li {
  line-height: 1.6;
  text-transform: capitalize;
}

@media (max-width: 720px) {
  .pokemon {
    grid-template-columns: 1fr;
  }
}
</style>
