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

// Búsqueda por nombre: se resuelve aparte del listado SSR, porque es una
// acción del usuario (no tiene sentido pedirla en el servidor de entrada).
// Primero confirmamos que el pokémon exista y recién ahí navegamos al
// detalle, así un nombre inválido nunca rompe la app.
const busqueda = ref('')
const buscando = ref(false)
const noEncontrado = ref(false)
const errorBusqueda = ref('')

function limpiarEstadoBusqueda() {
  noEncontrado.value = false
  errorBusqueda.value = ''
}

async function buscarPokemon() {
  const nombre = busqueda.value.trim().toLowerCase()
  limpiarEstadoBusqueda()
  if (!nombre) return

  buscando.value = true
  try {
    await $fetch(`https://pokeapi.co/api/v2/pokemon/${nombre}`)
    await navigateTo(`/pokemon/${nombre}`)
  } catch (err) {
    const status = err?.response?.status ?? err?.statusCode
    if (status === 404) {
      noEncontrado.value = true
    } else {
      errorBusqueda.value =
        'No se pudo conectar con la PokéAPI. Intentá de nuevo.'
    }
  } finally {
    buscando.value = false
  }
}
</script>

<template>
  <section class="page">
    <p class="badge">Modo SSR · useFetch</p>
    <h1>Pokédex</h1>
    <p>Los primeros 20 Pokémon. Tocá uno para ver su detalle.</p>

    <form class="buscador" @submit.prevent="buscarPokemon">
      <label for="busqueda" class="buscador__label">Buscar por nombre</label>
      <div class="buscador__controles">
        <input
          id="busqueda"
          v-model="busqueda"
          type="text"
          placeholder="Ej: pikachu, charizard..."
          @input="limpiarEstadoBusqueda"
        />
        <button type="submit" :disabled="buscando">
          {{ buscando ? 'Buscando...' : 'Buscar' }}
        </button>
      </div>
      <p v-if="noEncontrado" class="buscador__feedback buscador__feedback--warning" role="alert">
        No encontramos ningún pokémon llamado "{{ busqueda }}". Probá con otro
        nombre.
      </p>
      <p v-else-if="errorBusqueda" class="buscador__feedback buscador__feedback--error" role="alert">
        {{ errorBusqueda }}
      </p>
    </form>

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

.buscador {
  display: grid;
  gap: 0.5rem;
}

.buscador__label {
  font-weight: 600;
  font-size: 0.9rem;
}

.buscador__controles {
  display: flex;
  gap: 0.5rem;
  flex-wrap: wrap;
}

.buscador__controles input {
  flex: 1 1 220px;
  padding: 0.6rem 0.75rem;
  border: 1px solid #dbe4ef;
  border-radius: 4px;
  font-size: 1rem;
}

.buscador__controles button {
  background: #0c172a;
  color: #fff;
  border: none;
  padding: 0.6rem 1.25rem;
  border-radius: 4px;
  cursor: pointer;
}

.buscador__controles button:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.buscador__feedback {
  margin: 0;
}

.buscador__feedback--warning {
  color: #9a3412;
}

.buscador__feedback--error {
  color: #b91c1c;
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
