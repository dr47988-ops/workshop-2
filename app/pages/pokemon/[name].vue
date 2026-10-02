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

/* Colores según el tipo principal del Pokémon */
const typeColors = {
  grass: '#49c98a',
  fire: '#f45b69',
  water: '#42b9dd',
  electric: '#f5c842',
  poison: '#a66dd4',
  bug: '#92c353',
  normal: '#a8a878',
  flying: '#8fa8dd',
  ground: '#d9ad5b',
  fairy: '#e99ab8',
  fighting: '#d5675f',
  psychic: '#e96d9a',
  rock: '#b8a058',
  ghost: '#7566a8',
  ice: '#71cbd0',
  dragon: '#7766dd',
  dark: '#66554c',
  steel: '#9fa9b8',
}

const pokemonColor = computed(() => {
  if (!pokemon.value?.tipos?.length) {
    return '#49c98a'
  }

  return typeColors[pokemon.value.tipos[0]] ?? '#49c98a'
})

const numeroPokemon = computed(() => {
  if (!pokemon.value?.id) return '0000'

  return String(pokemon.value.id).padStart(4, '0')
})
</script>

<template>
  <section class="page">

    <!-- BOTÓN VOLVER -->
    <NuxtLink to="/" class="back">
      <span class="back__arrow">←</span>
      Volver a la Pokédex
    </NuxtLink>

    <!-- LOADING -->
    <div v-if="pending" class="loading">
      <div class="loading__ball"></div>
      <p>Cargando Pokémon...</p>
    </div>

    <!-- ERROR -->
    <div v-else-if="error" class="error" role="alert">
      <div class="error__icon">!</div>

      <div>
        <h2>No pudimos encontrar este Pokémon</h2>
        <p>
          Revisá el nombre o intentá cargar la información nuevamente.
        </p>

        <button type="button" class="button" @click="refresh()">
          Reintentar
        </button>
      </div>
    </div>

    <!-- DETALLE -->
    <article
      v-else-if="pokemon"
      class="pokemon-card"
      :style="{ '--pokemon-color': pokemonColor }"
    >

      <!-- PARTE SUPERIOR -->
      <div class="pokemon-card__hero">

        <!-- decoración -->
        <div class="pokeball-decoration">
          <div class="pokeball-decoration__line"></div>
          <div class="pokeball-decoration__center"></div>
        </div>

        <div class="pokemon-card__top">
          <div>
            <p class="pokemon-card__number">
              #{{ numeroPokemon }}
            </p>

            <h1>{{ pokemon.nombre }}</h1>

            <div class="types">
              <span
                v-for="tipo in pokemon.tipos"
                :key="tipo"
                class="type"
              >
                {{ tipo }}
              </span>
            </div>
          </div>

          <span class="generation">
            Pokédex
          </span>
        </div>

        <!-- IMAGEN -->
        <div class="pokemon-card__image">
          <div class="image-circle"></div>

          <img
            v-if="pokemon.imagen"
            :src="pokemon.imagen"
            :alt="`Imagen de ${pokemon.nombre}`"
          />
        </div>
      </div>

      <!-- INFORMACIÓN -->
      <div class="pokemon-card__information">

        <div class="section-title">
          <span></span>
          <h2>Información</h2>
          <span></span>
        </div>

        <!-- STATS -->
        <dl class="stats">

          <div class="stat">
            <div class="stat__icon">↕</div>

            <div>
              <dt>Altura</dt>
              <dd>{{ pokemon.altura }} m</dd>
            </div>
          </div>

          <div class="stat">
            <div class="stat__icon">⚖</div>

            <div>
              <dt>Peso</dt>
              <dd>{{ pokemon.peso }} kg</dd>
            </div>
          </div>

        </dl>

        <!-- HABILIDADES -->
        <section
          class="abilities"
          aria-labelledby="abilities-title"
        >
          <h2 id="abilities-title">
            Habilidades
          </h2>

          <div class="abilities__list">
            <span
              v-for="habilidad in pokemon.habilidades"
              :key="habilidad"
              class="ability"
            >
              {{ habilidad }}
            </span>
          </div>
        </section>

      </div>
    </article>
  </section>
</template>

<style scoped>

/* ============================= */
/* PÁGINA */
/* ============================= */

.page {
  width: 100%;
  max-width: 900px;
  margin: 0 auto;
  display: grid;
  gap: 1.3rem;
  padding: 1rem 0 3rem;
}


/* ============================= */
/* VOLVER */
/* ============================= */

.back {
  width: fit-content;
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  color: #52607a;
  font-weight: 700;
  text-decoration: none;
  transition: 0.2s ease;
}

.back__arrow {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  display: grid;
  place-items: center;
  background: #ffffff;
  box-shadow: 0 3px 10px rgba(15, 23, 42, 0.1);
}

.back:hover {
  color: #0c172a;
  transform: translateX(-3px);
}


/* ============================= */
/* TARJETA PRINCIPAL */
/* ============================= */

.pokemon-card {
  overflow: hidden;
  border-radius: 28px;
  background: #ffffff;
  box-shadow:
    0 20px 50px rgba(15, 23, 42, 0.12),
    0 3px 10px rgba(15, 23, 42, 0.06);
}


/* ============================= */
/* HERO */
/* ============================= */

.pokemon-card__hero {
  position: relative;
  min-height: 420px;
  padding: 2rem 2.5rem;
  background:
    linear-gradient(
      135deg,
      var(--pokemon-color),
      color-mix(in srgb, var(--pokemon-color) 75%, white)
    );
  overflow: hidden;
}


/* ============================= */
/* CABECERA */
/* ============================= */

.pokemon-card__top {
  position: relative;
  z-index: 3;
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
}

.pokemon-card__number {
  margin: 0 0 0.25rem;
  color: rgba(255, 255, 255, 0.8);
  font-size: 0.95rem;
  font-weight: 800;
  letter-spacing: 0.1em;
}

.pokemon-card h1 {
  margin: 0;
  color: #ffffff;
  font-size: clamp(2.4rem, 6vw, 4.3rem);
  line-height: 1;
  text-transform: capitalize;
  letter-spacing: -0.04em;
  text-shadow: 0 3px 10px rgba(0, 0, 0, 0.12);
}

.generation {
  background: rgba(255, 255, 255, 0.22);
  border: 1px solid rgba(255, 255, 255, 0.4);
  backdrop-filter: blur(8px);
  color: #ffffff;
  padding: 0.5rem 0.9rem;
  border-radius: 999px;
  font-size: 0.8rem;
  font-weight: 700;
}


/* ============================= */
/* TIPOS */
/* ============================= */

.types {
  display: flex;
  gap: 0.5rem;
  flex-wrap: wrap;
  margin-top: 1rem;
}

.type {
  padding: 0.45rem 1rem;
  border-radius: 999px;
  background: rgba(255, 255, 255, 0.9);
  color: #263238;
  font-size: 0.85rem;
  font-weight: 800;
  text-transform: capitalize;
  box-shadow: 0 3px 10px rgba(0, 0, 0, 0.08);
}


/* ============================= */
/* IMAGEN POKÉMON */
/* ============================= */

.pokemon-card__image {
  position: absolute;
  z-index: 2;
  right: 4%;
  bottom: -15px;
  width: min(430px, 52%);
  aspect-ratio: 1;
  display: grid;
  place-items: center;
}

.pokemon-card__image img {
  position: relative;
  z-index: 2;
  width: 92%;
  height: 92%;
  object-fit: contain;
  filter: drop-shadow(0 20px 18px rgba(0, 0, 0, 0.2));
  transition: transform 0.3s ease;
}

.pokemon-card:hover .pokemon-card__image img {
  transform: translateY(-6px) scale(1.02);
}

.image-circle {
  position: absolute;
  width: 78%;
  height: 78%;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.2);
}


/* ============================= */
/* POKÉBOLA DECORATIVA */
/* ============================= */

.pokeball-decoration {
  position: absolute;
  width: 360px;
  height: 360px;
  border: 34px solid rgba(255, 255, 255, 0.11);
  border-radius: 50%;
  right: -80px;
  bottom: -110px;
}

.pokeball-decoration__line {
  position: absolute;
  width: 100%;
  height: 34px;
  background: rgba(255, 255, 255, 0.11);
  top: 50%;
  left: 0;
  transform: translateY(-50%);
}

.pokeball-decoration__center {
  position: absolute;
  width: 100px;
  height: 100px;
  border: 25px solid rgba(255, 255, 255, 0.11);
  background: var(--pokemon-color);
  border-radius: 50%;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}


/* ============================= */
/* INFORMACIÓN */
/* ============================= */

.pokemon-card__information {
  padding: 2rem 2.5rem 2.5rem;
  display: grid;
  gap: 1.5rem;
}

.section-title {
  display: grid;
  grid-template-columns: 1fr auto 1fr;
  align-items: center;
  gap: 1rem;
}

.section-title span {
  height: 1px;
  background: #e5e7eb;
}

.section-title h2 {
  margin: 0;
  color: #52607a;
  font-size: 0.85rem;
  text-transform: uppercase;
  letter-spacing: 0.12em;
}


/* ============================= */
/* ALTURA Y PESO */
/* ============================= */

.stats {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1rem;
  margin: 0;
}

.stat {
  display: flex;
  align-items: center;
  gap: 1rem;
  padding: 1.2rem;
  border-radius: 16px;
  background: #f8fafc;
  border: 1px solid #edf1f5;
}

.stat__icon {
  width: 44px;
  height: 44px;
  flex-shrink: 0;
  border-radius: 12px;
  display: grid;
  place-items: center;
  background: color-mix(
    in srgb,
    var(--pokemon-color) 18%,
    white
  );
  color: #1f2937;
  font-size: 1.25rem;
  font-weight: 800;
}

.stats dt {
  color: #64748b;
  font-size: 0.8rem;
  font-weight: 600;
}

.stats dd {
  margin: 0.15rem 0 0;
  color: #0f172a;
  font-size: 1.3rem;
  font-weight: 800;
}


/* ============================= */
/* HABILIDADES */
/* ============================= */

.abilities {
  display: grid;
  gap: 0.8rem;
}

.abilities h2 {
  margin: 0;
  color: #0f172a;
  font-size: 1rem;
}

.abilities__list {
  display: flex;
  gap: 0.65rem;
  flex-wrap: wrap;
}

.ability {
  padding: 0.6rem 1rem;
  border-radius: 10px;
  background: color-mix(
    in srgb,
    var(--pokemon-color) 15%,
    white
  );
  border: 1px solid
    color-mix(
      in srgb,
      var(--pokemon-color) 30%,
      white
    );
  color: #263238;
  font-weight: 700;
  text-transform: capitalize;
}


/* ============================= */
/* LOADING */
/* ============================= */

.loading {
  min-height: 300px;
  display: grid;
  place-items: center;
  align-content: center;
  gap: 1rem;
  color: #52607a;
}

.loading__ball {
  width: 42px;
  height: 42px;
  border-radius: 50%;
  border: 5px solid #e2e8f0;
  border-top-color: #ef4444;
  animation: spin 0.8s linear infinite;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}


/* ============================= */
/* ERROR */
/* ============================= */

.error {
  display: flex;
  align-items: center;
  gap: 1rem;
  padding: 1.5rem;
  background: #fff;
  border-radius: 18px;
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.08);
}

.error__icon {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  display: grid;
  place-items: center;
  background: #fee2e2;
  color: #b91c1c;
  font-size: 1.5rem;
  font-weight: 900;
}

.error h2 {
  margin: 0 0 0.3rem;
}

.error p {
  margin: 0 0 0.8rem;
  color: #64748b;
}

.button {
  background: #0c172a;
  color: #ffffff;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  padding: 0.65rem 1rem;
  font-weight: 700;
}


/* ============================= */
/* TABLET */
/* ============================= */

@media (max-width: 720px) {

  .page {
    padding: 0.5rem 0 2rem;
  }

  .pokemon-card {
    border-radius: 22px;
  }

  .pokemon-card__hero {
    min-height: 500px;
    padding: 1.5rem;
  }

  .pokemon-card__image {
    width: 90%;
    max-width: 380px;
    right: 50%;
    transform: translateX(50%);
    bottom: -15px;
  }

  .pokemon-card h1 {
    font-size: 2.8rem;
  }

  .pokemon-card__information {
    padding: 1.5rem;
  }
}


/* ============================= */
/* CELULAR */
/* ============================= */

@media (max-width: 480px) {

  .pokemon-card__hero {
    min-height: 440px;
  }

  .pokemon-card__top {
    align-items: flex-start;
  }

  .pokemon-card h1 {
    font-size: 2.3rem;
  }

  .generation {
    font-size: 0.7rem;
  }

  .pokemon-card__image {
    width: 95%;
  }

  .stats {
    grid-template-columns: 1fr;
  }

  .pokemon-card__information {
    padding: 1.25rem;
  }

  .back {
    font-size: 0.9rem;
  }
}
</style>