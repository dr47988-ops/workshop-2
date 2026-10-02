<script setup>
// URL base de los sprites oficiales utilizados por PokéAPI
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

    if (import.meta.server) {
      console.log('SSR: pokémon cargados en el servidor', lista.length)
    }

    return lista
  },
})

/* --------------------------------------------------
   BÚSQUEDA
-------------------------------------------------- */

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

/* --------------------------------------------------
   COLORES DE LAS TARJETAS
-------------------------------------------------- */

function clasePokemon(id) {
  const numero = Number(id)

  // Bulbasaur, Ivysaur, Venusaur
  if (numero >= 1 && numero <= 3) {
    return 'pokemon--grass'
  }

  // Charmander, Charmeleon, Charizard
  if (numero >= 4 && numero <= 6) {
    return 'pokemon--fire'
  }

  // Squirtle, Wartortle, Blastoise
  if (numero >= 7 && numero <= 9) {
    return 'pokemon--water'
  }

  // Caterpie, Metapod, Butterfree
  if (numero >= 10 && numero <= 12) {
    return 'pokemon--bug'
  }

  // Weedle, Kakuna, Beedrill
  if (numero >= 13 && numero <= 15) {
    return 'pokemon--yellow'
  }

  // Pidgey, Pidgeotto, Pidgeot
  if (numero >= 16 && numero <= 18) {
    return 'pokemon--normal'
  }

  return 'pokemon--purple'
}

function numeroPokemon(id) {
  return `#${String(id).padStart(4, '0')}`
}
</script>

<template>
  <section class="page">

    <!-- Encabezado -->
    <div class="hero">
      <p class="badge">
        SSR · useFetch
      </p>

      <h1>Pokédex</h1>

      <p class="descripcion">
        Descubrí los primeros 20 Pokémon y seleccioná uno para conocer
        todos sus detalles.
      </p>
    </div>

    <!-- Buscador -->
    <form class="buscador" @submit.prevent="buscarPokemon">

      <label for="busqueda" class="buscador__label">
        Buscar Pokémon
      </label>

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

      <p
        v-if="noEncontrado"
        class="buscador__feedback buscador__feedback--warning"
        role="alert"
      >
        No encontramos ningún Pokémon llamado "{{ busqueda }}".
        Probá con otro nombre.
      </p>

      <p
        v-else-if="errorBusqueda"
        class="buscador__feedback buscador__feedback--error"
        role="alert"
      >
        {{ errorBusqueda }}
      </p>

    </form>

    <!-- Loading -->
    <div v-if="pending" class="estado">
      <div class="loader"></div>
      <p>Cargando Pokémon...</p>
    </div>

    <!-- Error API -->
    <div v-else-if="error" class="error" role="alert">

      <p>
        No se pudo cargar la lista de Pokémon.
        Revisá tu conexión e intentá de nuevo.
      </p>

      <button
        type="button"
        class="retry"
        @click="refresh()"
      >
        Reintentar
      </button>

    </div>

    <!-- Lista Pokémon -->
    <ul v-else class="lista">

      <li
        v-for="pokemon in pokemones"
        :key="pokemon.id"
        class="lista__item"
        :class="clasePokemon(pokemon.id)"
      >

        <NuxtLink
          :to="`/pokemon/${pokemon.nombre}`"
          class="lista__link"
        >

          <!-- Parte superior -->
          <div class="pokemon__header">

            <span class="lista__nombre">
              {{ pokemon.nombre }}
            </span>

            <span class="lista__id">
              {{ numeroPokemon(pokemon.id) }}
            </span>

          </div>

          <!-- Imagen -->
          <div class="pokemon__imagen">

            <div class="pokemon__circulo"></div>

            <img
              :src="pokemon.sprite"
              :alt="`Sprite de ${pokemon.nombre}`"
              width="130"
              height="130"
              loading="lazy"
            />

          </div>

          <!-- Texto inferior -->
          <div class="pokemon__footer">
            <span>Ver detalles</span>
            <span class="flecha">→</span>
          </div>

        </NuxtLink>

      </li>

    </ul>

  </section>
</template>

<style scoped>

/* --------------------------------------------------
   PÁGINA
-------------------------------------------------- */

.page {
  display: grid;
  gap: 1.5rem;
}

.hero {
  display: grid;
  gap: 0.5rem;
}

.hero h1 {
  margin: 0;
  font-size: 2.3rem;
  color: #0c172a;
}

.descripcion {
  margin: 0;
  color: #52607a;
  line-height: 1.5;
}


/* --------------------------------------------------
   BADGE SSR
-------------------------------------------------- */

.badge {
  margin: 0;
  width: fit-content;

  background: #dcfce7;
  color: #166534;

  padding: 0.4rem 0.8rem;

  border-radius: 999px;

  font-size: 0.8rem;
  font-weight: 600;
}


/* --------------------------------------------------
   BUSCADOR
-------------------------------------------------- */

.buscador {
  display: grid;
  gap: 0.6rem;

  background: #ffffff;

  padding: 1rem;

  border-radius: 14px;

  box-shadow:
    0 4px 15px rgba(15, 23, 42, 0.07);
}

.buscador__label {
  font-weight: 700;
  font-size: 0.9rem;

  color: #0c172a;
}

.buscador__controles {
  display: flex;
  gap: 0.6rem;
}

.buscador__controles input {
  flex: 1;

  padding: 0.8rem 1rem;

  border: 1px solid #dbe4ef;
  border-radius: 9px;

  font-size: 0.95rem;

  outline: none;

  transition: 0.2s ease;
}

.buscador__controles input:focus {
  border-color: #38c1d9;

  box-shadow:
    0 0 0 3px rgba(56, 193, 217, 0.15);
}

.buscador__controles button {
  background: #0c172a;
  color: #ffffff;

  border: none;

  padding: 0.75rem 1.5rem;

  border-radius: 9px;

  cursor: pointer;

  font-weight: 600;

  transition: 0.2s ease;
}

.buscador__controles button:hover {
  background: #1e293b;
  transform: translateY(-1px);
}

.buscador__controles button:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}


/* --------------------------------------------------
   MENSAJES
-------------------------------------------------- */

.buscador__feedback {
  margin: 0;
  font-size: 0.9rem;
}

.buscador__feedback--warning {
  color: #9a3412;
}

.buscador__feedback--error {
  color: #b91c1c;
}


/* --------------------------------------------------
   LISTA
-------------------------------------------------- */

.lista {
  list-style: none;

  margin: 0;
  padding: 0;

  display: grid;

  grid-template-columns:
    repeat(auto-fill, minmax(190px, 1fr));

  gap: 1rem;
}


/* --------------------------------------------------
   TARJETAS
-------------------------------------------------- */

.lista__item {
  border-radius: 18px;

  overflow: hidden;

  min-height: 230px;

  box-shadow:
    0 6px 16px rgba(15, 23, 42, 0.12);

  transition:
    transform 0.25s ease,
    box-shadow 0.25s ease;
}

.lista__item:hover {
  transform: translateY(-6px);

  box-shadow:
    0 12px 25px rgba(15, 23, 42, 0.18);
}


/* --------------------------------------------------
   COLORES
-------------------------------------------------- */

.pokemon--grass {
  background:
    linear-gradient(135deg, #56d8bd, #38bfa7);
}

.pokemon--fire {
  background:
    linear-gradient(135deg, #ff6b78, #ef476f);
}

.pokemon--water {
  background:
    linear-gradient(135deg, #58c9ea, #35aeda);
}

.pokemon--bug {
  background:
    linear-gradient(135deg, #a8d86e, #7fbd52);
}

.pokemon--yellow {
  background:
    linear-gradient(135deg, #f6ce62, #e8ad3d);
}

.pokemon--normal {
  background:
    linear-gradient(135deg, #c4b7aa, #a99a8d);
}

.pokemon--purple {
  background:
    linear-gradient(135deg, #b59bea, #9275d5);
}


/* --------------------------------------------------
   CONTENIDO TARJETA
-------------------------------------------------- */

.lista__link {
  height: 100%;

  display: flex;
  flex-direction: column;

  padding: 1rem;

  color: #ffffff;

  text-decoration: none;
}

.pokemon__header {
  display: flex;

  justify-content: space-between;
  align-items: center;

  gap: 0.5rem;
}

.lista__nombre {
  text-transform: capitalize;

  font-size: 1.1rem;

  font-weight: 800;
}

.lista__id {
  font-size: 0.75rem;

  color: rgba(255, 255, 255, 0.8);

  font-weight: 600;
}


/* --------------------------------------------------
   IMAGEN
-------------------------------------------------- */

.pokemon__imagen {
  position: relative;

  flex: 1;

  display: flex;

  justify-content: center;
  align-items: center;

  min-height: 145px;
}

.pokemon__imagen img {
  position: relative;

  z-index: 2;

  width: 130px;
  height: 130px;

  object-fit: contain;

  image-rendering: auto;

  transition: transform 0.25s ease;
}

.lista__item:hover .pokemon__imagen img {
  transform: scale(1.12);
}

.pokemon__circulo {
  position: absolute;

  width: 115px;
  height: 115px;

  border-radius: 50%;

  background:
    rgba(255, 255, 255, 0.18);

  z-index: 1;
}


/* --------------------------------------------------
   FOOTER TARJETA
-------------------------------------------------- */

.pokemon__footer {
  display: flex;

  justify-content: space-between;
  align-items: center;

  font-size: 0.8rem;

  font-weight: 600;

  color: rgba(255, 255, 255, 0.9);
}

.flecha {
  font-size: 1.2rem;

  transition: transform 0.2s ease;
}

.lista__item:hover .flecha {
  transform: translateX(4px);
}


/* --------------------------------------------------
   LOADING
-------------------------------------------------- */

.estado {
  display: flex;

  align-items: center;

  gap: 0.8rem;

  color: #52607a;
}

.loader {
  width: 22px;
  height: 22px;

  border: 3px solid #dbe4ef;

  border-top-color: #38c1d9;

  border-radius: 50%;

  animation: girar 0.8s linear infinite;
}

@keyframes girar {
  to {
    transform: rotate(360deg);
  }
}


/* --------------------------------------------------
   ERROR
-------------------------------------------------- */

.error {
  color: #b91c1c;

  display: grid;

  gap: 0.7rem;

  justify-items: start;

  background: #fef2f2;

  padding: 1rem;

  border-radius: 10px;
}

.error p {
  margin: 0;
}

.retry {
  background: #0c172a;
  color: #ffffff;

  border: none;

  padding: 0.55rem 1rem;

  border-radius: 7px;

  cursor: pointer;
}


/* --------------------------------------------------
   TABLET
-------------------------------------------------- */

@media (max-width: 900px) {

  .lista {
    grid-template-columns:
      repeat(auto-fill, minmax(160px, 1fr));
  }

}


/* --------------------------------------------------
   CELULAR
-------------------------------------------------- */

@media (max-width: 600px) {

  .page {
    gap: 1rem;
  }

  .hero h1 {
    font-size: 1.8rem;
  }

  .descripcion {
    font-size: 0.9rem;
  }

  /* BUSCADOR */

  .buscador {
    padding: 0.8rem;
  }

  .buscador__controles {
    flex-direction: column;
  }

  .buscador__controles input {
    width: 100%;
    box-sizing: border-box;
  }

  .buscador__controles button {
    width: 100%;
  }

  /* GRID DE POKÉMON */

  .lista {
    grid-template-columns:
      repeat(2, minmax(0, 1fr));

    gap: 0.7rem;
  }

  /* TARJETA */

  .lista__item {
    min-height: 225px;
    border-radius: 14px;
  }

  .lista__link {
    height: 100%;
    padding: 0.8rem;
    box-sizing: border-box;
  }

  /* NOMBRE Y NÚMERO */

  .lista__nombre {
    font-size: 0.9rem;
  }

  .lista__id {
    font-size: 0.65rem;
  }

  /* IMAGEN */

  .pokemon__imagen {
    min-height: 125px;
  }

  .pokemon__imagen img {
    width: 105px;
    height: 105px;
  }

  .pokemon__circulo {
    width: 90px;
    height: 90px;
  }

  /* VER DETALLES */

  .pokemon__footer {
    font-size: 0.7rem;
    margin-top: 0.4rem;
    flex-shrink: 0;
  }
}
</style>

