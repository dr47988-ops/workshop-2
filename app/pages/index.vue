<script setup>
// El listado de la API solo trae nombre y url, pero la url termina en el id
// del pokémon, así que de ahí sacamos el id para armar el sprite.
const SPRITES_URL =
  'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon'

useHead({ title: 'Pokédex' })

const {
  data: pokemones,
  pending,
  error,
  refresh,
} = useFetch('https://pokeapi.co/api/v2/pokemon?limit=20', {
  key: 'lista-pokemon',
  // lazy: en el cliente no bloquea la navegación, así el "Cargando" se alcanza
  // a ver. En un refresh el servidor igual espera la respuesta y el HTML ya
  // llega con los pokémon adentro (SSR).
  lazy: true,
  transform: (respuesta) => {
    const lista = respuesta.results.map((pokemon) => {
      const id = pokemon.url.split('/').filter(Boolean).pop()
      return {
        id,
        nombre: pokemon.name,
        sprite: `${SPRITES_URL}/${id}.png`,
      }
    })

    // Este log solo aparece en la terminal de Nuxt, sirve para comprobar el SSR
    if (import.meta.server) {
      console.log('SSR: pokémon cargados en el servidor', lista.length)
    }

    return lista
  },
})
</script>

<template>
  <section class="page">
    <p class="badge">Modo SSR · useFetch</p>
    <h1>Pokédex</h1>
    <p>Los primeros 20 Pokémon. Tocá uno para ver su detalle.</p>

    <p v-if="pending">Cargando pokémon...</p>

    <div v-else-if="error" class="error" role="alert">
      <p>
        No se pudo cargar la lista de pokémon. Revisá tu conexión e intentá de
        nuevo.
      </p>
      <button type="button" class="retry" @click="refresh()">Reintentar</button>
    </div>

    <ul v-else class="lista">
      <li v-for="pokemon in pokemones" :key="pokemon.id" class="lista__item">
        <NuxtLink :to="`/pokemon/${pokemon.nombre}`" class="lista__link">
          <img
            :src="pokemon.sprite"
            :alt="`Sprite de ${pokemon.nombre}`"
            width="96"
            height="96"
            loading="lazy"
          />
          <span class="lista__id">#{{ pokemon.id }}</span>
          <span class="lista__nombre">{{ pokemon.nombre }}</span>
        </NuxtLink>
      </li>
    </ul>
  </section>
</template>

<style scoped>
.page {
  display: grid;
  gap: 1rem;
}

.badge {
  margin: 0;
  width: fit-content;
  background: #dcfce7;
  color: #166534;
  padding: 0.35rem 0.7rem;
  border-radius: 999px;
  font-size: 0.85rem;
}

.error {
  color: #b91c1c;
  display: grid;
  gap: 0.5rem;
  justify-items: start;
}

.error p {
  margin: 0;
}

.retry {
  background: #0c172a;
  color: #fff;
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 4px;
  cursor: pointer;
}

.lista {
  list-style: none;
  margin: 0;
  padding: 0;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
  gap: 1rem;
}

.lista__item {
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.08);
}

.lista__link {
  display: grid;
  justify-items: center;
  gap: 0.25rem;
  padding: 1rem;
  color: inherit;
  text-decoration: none;
}

.lista__link:hover {
  color: #38c1d9;
}

.lista__id {
  font-size: 0.8rem;
  color: #52607a;
}

.lista__nombre {
  text-transform: capitalize;
  font-weight: 600;
}
</style>
