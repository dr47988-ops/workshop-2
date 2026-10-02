<script setup>
const pokemon = ref([])
const pending = ref(true)
const error = ref(null)

useHead({
  title: 'Pokédex Cliente'
})

const typeColors = {
  grass: '#49c98a',
  fire: '#f45b69',
  water: '#42b9dd',
  electric: '#f5c842',
  poison: '#a66dd4',
  bug: '#92c353',
  normal: '#b0a99f',
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
  steel: '#9fa9b8'
}

function getTypeColor(type) {
  return typeColors[type] ?? '#94a3b8'
}

onMounted(async () => {
  console.log('Cliente: pidiendo Pokémon desde el navegador')

  pending.value = true
  error.value = null

  try {
    const response = await fetch(
      'https://pokeapi.co/api/v2/pokemon?limit=20'
    )

    if (!response.ok) {
      throw new Error('No se pudieron obtener los Pokémon')
    }

    const data = await response.json()

    /*
      Pedimos el detalle de cada Pokémon DESDE EL NAVEGADOR.
      Esto permite obtener sus tipos y mantener esta página
      como demostración de carga del lado del cliente.
    */
    const detalles = await Promise.all(
      data.results.map(async (item) => {
        const detailResponse = await fetch(item.url)

        if (!detailResponse.ok) {
          throw new Error(
            `No se pudo obtener el detalle de ${item.name}`
          )
        }

        const detail = await detailResponse.json()

        return {
          name: detail.name,
          id: detail.id,

          image:
            detail.sprites.other?.['official-artwork']?.front_default ??
            detail.sprites.front_default,

          types: detail.types.map(
            (typeItem) => typeItem.type.name
          )
        }
      })
    )

    pokemon.value = detalles
  } catch (err) {
    console.error(err)
    error.value = err
  } finally {
    pending.value = false
  }
})
</script>

<template>
  <section class="page">

    <!-- ENCABEZADO -->
    <div class="page-header">

      <div>
        <p class="badge">
          <span class="badge__dot"></span>
          Modo cliente · onMounted + fetch
        </p>

        <h1>Pokédex Cliente</h1>

        <p class="description">
          Los primeros 20 Pokémon cargados directamente
          desde el navegador.
        </p>
      </div>

      <div class="client-info">
        <span>CLIENT</span>
        <strong>20 Pokémon</strong>
      </div>

    </div>


    <!-- EXPLICACIÓN CLIENTE -->
    <div class="client-message">
      <div class="client-message__icon">
        &lt;/&gt;
      </div>

      <div>
        <strong>Carga desde el navegador</strong>

        <p>
          Esta versión utiliza
          <code>onMounted</code> +
          <code>fetch</code>.
          Los Pokémon aparecen después de cargar la página.
        </p>
      </div>
    </div>


    <!-- LOADING -->
    <div v-if="pending" class="loading">

      <div class="pokeball-loader">
        <span></span>
      </div>

      <div>
        <strong>Cargando Pokédex...</strong>
        <p>Obteniendo Pokémon desde la PokéAPI</p>
      </div>

    </div>


    <!-- ERROR -->
    <div v-else-if="error" class="error">

      <div class="error__icon">
        !
      </div>

      <div>
        <strong>No pudimos cargar los Pokémon</strong>
        <p>
          Revisá tu conexión e intentá nuevamente.
        </p>
      </div>

    </div>


    <!-- POKÉMON -->
    <div v-else class="pokemon-grid">

      <NuxtLink
        v-for="item in pokemon"
        :key="item.name"
        :to="`/pokemon/${item.name}`"
        class="pokemon-card"
        :style="{
          '--type-color': getTypeColor(item.types[0])
        }"
      >

        <!-- NÚMERO -->
        <span class="pokemon-card__number">
          #{{ String(item.id).padStart(4, '0') }}
        </span>


        <!-- IMAGEN -->
        <div class="pokemon-card__image">

          <div class="pokemon-card__circle"></div>

          <img
            :src="item.image"
            :alt="`Imagen de ${item.name}`"
          >

        </div>


        <!-- INFORMACIÓN -->
        <div class="pokemon-card__content">

          <h2>
            {{ item.name }}
          </h2>

          <div class="types">

            <span
              v-for="type in item.types"
              :key="type"
              class="type"
            >
              {{ type }}
            </span>

          </div>

        </div>

      </NuxtLink>

    </div>

  </section>
</template>


<style scoped>

/* =====================================
   PÁGINA
===================================== */

.page {
  width: 100%;
  max-width: 1050px;
  margin: 0 auto;
  display: grid;
  gap: 1.5rem;
  padding: 1rem 0 3rem;
}


/* =====================================
   ENCABEZADO
===================================== */

.page-header {
  display: flex;
  align-items: flex-end;
  justify-content: space-between;
  gap: 2rem;
}

.page-header h1 {
  margin: 0.8rem 0 0.4rem;
  color: #0f172a;
  font-size: clamp(2rem, 4vw, 3rem);
  letter-spacing: -0.04em;
}

.description {
  margin: 0;
  color: #64748b;
  line-height: 1.6;
}


/* =====================================
   BADGE
===================================== */

.badge {
  margin: 0;
  width: fit-content;
  display: flex;
  align-items: center;
  gap: 0.45rem;

  background: #fff3e8;
  color: #b45309;

  padding: 0.45rem 0.8rem;
  border-radius: 999px;

  font-size: 0.78rem;
  font-weight: 700;
}

.badge__dot {
  width: 7px;
  height: 7px;
  background: #f97316;
  border-radius: 50%;
}


/* =====================================
   INFO DERECHA
===================================== */

.client-info {
  display: grid;
  justify-items: end;
  gap: 0.2rem;
}

.client-info span {
  color: #94a3b8;
  font-size: 0.7rem;
  font-weight: 800;
  letter-spacing: 0.15em;
}

.client-info strong {
  color: #0f172a;
}


/* =====================================
   EXPLICACIÓN CLIENTE
===================================== */

.client-message {
  display: flex;
  align-items: center;
  gap: 1rem;

  padding: 1rem 1.2rem;

  background: #ffffff;
  border: 1px solid #e5e7eb;
  border-left: 4px solid #f97316;

  border-radius: 14px;

  box-shadow:
    0 4px 15px rgba(15, 23, 42, 0.05);
}

.client-message__icon {
  width: 45px;
  height: 45px;
  flex-shrink: 0;

  display: grid;
  place-items: center;

  background: #fff3e8;
  color: #ea580c;

  border-radius: 12px;

  font-weight: 900;
}

.client-message strong {
  color: #0f172a;
}

.client-message p {
  margin: 0.2rem 0 0;
  color: #64748b;
  font-size: 0.88rem;
}

.client-message code {
  color: #ea580c;
  font-weight: 700;
}


/* =====================================
   GRID
===================================== */

.pokemon-grid {
  display: grid;

  grid-template-columns:
    repeat(4, minmax(0, 1fr));

  gap: 1rem;
}


/* =====================================
   TARJETA
===================================== */

.pokemon-card {
  position: relative;

  min-height: 245px;

  overflow: hidden;

  display: flex;
  flex-direction: column;

  background:
    linear-gradient(
      145deg,
      var(--type-color),
      color-mix(
        in srgb,
        var(--type-color) 75%,
        white
      )
    );

  border-radius: 20px;

  padding: 1rem;

  color: #ffffff;
  text-decoration: none;

  box-shadow:
    0 8px 20px rgba(15, 23, 42, 0.1);

  transition:
    transform 0.25s ease,
    box-shadow 0.25s ease;
}


/* =====================================
   HOVER
===================================== */

.pokemon-card:hover {
  transform: translateY(-6px);

  box-shadow:
    0 16px 30px rgba(15, 23, 42, 0.18);
}


/* =====================================
   NÚMERO
===================================== */

.pokemon-card__number {
  position: relative;
  z-index: 3;

  align-self: flex-end;

  color: rgba(255, 255, 255, 0.8);

  font-size: 0.75rem;
  font-weight: 800;
}


/* =====================================
   IMAGEN
===================================== */

.pokemon-card__image {
  position: relative;

  flex: 1;

  display: grid;
  place-items: center;

  min-height: 140px;
}

.pokemon-card__circle {
  position: absolute;

  width: 125px;
  height: 125px;

  border-radius: 50%;

  background:
    rgba(255, 255, 255, 0.17);
}

.pokemon-card__image img {
  position: relative;
  z-index: 2;

  width: 145px;
  height: 145px;

  object-fit: contain;

  filter:
    drop-shadow(
      0 10px 8px rgba(0, 0, 0, 0.15)
    );

  transition:
    transform 0.3s ease;
}

.pokemon-card:hover img {
  transform:
    translateY(-5px)
    scale(1.07);
}


/* =====================================
   INFORMACIÓN
===================================== */

.pokemon-card__content {
  position: relative;
  z-index: 3;
}

.pokemon-card h2 {
  margin: 0 0 0.55rem;

  color: #ffffff;

  font-size: 1.15rem;

  text-transform: capitalize;

  text-shadow:
    0 2px 5px rgba(0, 0, 0, 0.12);
}


/* =====================================
   TIPOS
===================================== */

.types {
  display: flex;
  flex-wrap: wrap;
  gap: 0.35rem;
}

.type {
  padding:
    0.3rem
    0.65rem;

  background:
    rgba(255, 255, 255, 0.85);

  color: #263238;

  border-radius: 999px;

  font-size: 0.72rem;
  font-weight: 800;

  text-transform: capitalize;

  backdrop-filter:
    blur(5px);
}


/* =====================================
   LOADING
===================================== */

.loading {
  min-height: 350px;

  display: grid;
  place-items: center;
  align-content: center;

  gap: 1rem;

  text-align: center;

  color: #475569;
}

.loading p {
  margin: 0.25rem 0 0;
  color: #94a3b8;
}


/* Pokébola loading */

.pokeball-loader {
  position: relative;

  width: 55px;
  height: 55px;

  border: 4px solid #0f172a;

  border-radius: 50%;

  background:
    linear-gradient(
      to bottom,
      #ef4444 0%,
      #ef4444 45%,
      #0f172a 45%,
      #0f172a 55%,
      #ffffff 55%,
      #ffffff 100%
    );

  animation:
    bounce 0.8s infinite alternate;
}

.pokeball-loader span {
  position: absolute;

  width: 15px;
  height: 15px;

  background: white;

  border: 4px solid #0f172a;

  border-radius: 50%;

  top: 50%;
  left: 50%;

  transform:
    translate(-50%, -50%);
}

@keyframes bounce {
  from {
    transform:
      translateY(0);
  }

  to {
    transform:
      translateY(-8px);
  }
}


/* =====================================
   ERROR
===================================== */

.error {
  display: flex;
  align-items: center;

  gap: 1rem;

  padding: 1.25rem;

  background: #ffffff;

  border-radius: 14px;

  box-shadow:
    0 5px 20px rgba(15, 23, 42, 0.08);
}

.error__icon {
  width: 45px;
  height: 45px;

  display: grid;
  place-items: center;

  flex-shrink: 0;

  border-radius: 50%;

  background: #fee2e2;
  color: #b91c1c;

  font-weight: 900;
}

.error strong {
  color: #991b1b;
}

.error p {
  margin: 0.2rem 0 0;
  color: #64748b;
}


/* =====================================
   TABLET
===================================== */

@media (max-width: 900px) {

  .pokemon-grid {
    grid-template-columns:
      repeat(3, minmax(0, 1fr));
  }
}


/* =====================================
   TABLET PEQUEÑA
===================================== */

@media (max-width: 650px) {

  .page-header {
    align-items: flex-start;
  }

  .client-info {
    display: none;
  }

  .pokemon-grid {
    grid-template-columns:
      repeat(2, minmax(0, 1fr));
  }

  .pokemon-card {
    min-height: 220px;
  }

  .pokemon-card__image img {
    width: 125px;
    height: 125px;
  }

  .pokemon-card__circle {
    width: 110px;
    height: 110px;
  }
}


/* =====================================
   CELULAR
===================================== */

@media (max-width: 420px) {

  .page-header h1 {
    font-size: 1.8rem;
  }

  .client-message {
    align-items: flex-start;
  }

  .pokemon-grid {
    gap: 0.7rem;
  }

  .pokemon-card {
    min-height: 195px;
    padding: 0.8rem;
    border-radius: 16px;
  }

  .pokemon-card__image {
    min-height: 110px;
  }

  .pokemon-card__image img {
    width: 105px;
    height: 105px;
  }

  .pokemon-card__circle {
    width: 90px;
    height: 90px;
  }

  .pokemon-card h2 {
    font-size: 0.95rem;
  }

  .type {
    font-size: 0.62rem;
    padding: 0.25rem 0.5rem;
  }
}

</style>