<template>
  <section class="page">
    <h1>SSR vs Cliente</h1>

    <p>
      La misma PokéAPI y los mismos 20 Pokémon, cargados de dos formas diferentes.
    </p>

    <div class="grid">
      <article class="card card--ssr">
        <span class="badge badge--ssr">SSR</span>

        <h2>Con SSR</h2>

        <p><code>await useFetch(...)</code></p>

        <ul>
          <li>Nuxt obtiene los Pokémon utilizando <code>useFetch</code>.</li>
          <li>El HTML inicial ya contiene datos de los Pokémon.</li>
          <li>Los datos están disponibles desde la primera carga.</li>
          <li>Se puede comprobar con “Ver código fuente”.</li>
        </ul>

        <NuxtLink to="/" class="button">
          Ver versión SSR
        </NuxtLink>
      </article>

      <article class="card card--client">
        <span class="badge badge--client">Cliente</span>

        <h2>Sin SSR</h2>

        <p><code>onMounted + fetch</code></p>

        <ul>
          <li>La página se monta primero en el navegador.</li>
          <li>Después se realiza la petición a PokéAPI.</li>
          <li>Mientras llegan los datos se muestra el estado de carga.</li>
          <li>Los Pokémon se agregan después desde el cliente.</li>
        </ul>

        <NuxtLink to="/client" class="button">
          Ver versión cliente
        </NuxtLink>
      </article>
    </div>

    <section class="demo">
      <h2>¿Cómo comprobar la diferencia?</h2>

      <ol>
        <li>Abre la versión SSR.</li>
        <li>Usa “Ver código fuente de la página”.</li>
        <li>Busca el nombre <code>bulbasaur</code>.</li>
        <li>En la versión SSR los datos ya vienen en el HTML inicial.</li>
        <li>Compara el resultado con la versión Cliente.</li>
      </ol>
    </section>
  </section>
</template>

<style scoped>
.page {
  display: grid;
  gap: 1.5rem;
}

.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 1rem;
}

.card {
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.08);
  padding: 1.25rem;
  display: grid;
  gap: 0.75rem;
}

.card--ssr {
  border-top: 4px solid #16a34a;
}

.card--client {
  border-top: 4px solid #ea580c;
}

.card h2 {
  margin: 0;
}

.card ul {
  margin: 0;
  padding-left: 1.2rem;
  display: grid;
  gap: 0.4rem;
}

.badge {
  width: fit-content;
  padding: 0.3rem 0.7rem;
  border-radius: 999px;
  font-size: 0.8rem;
  font-weight: 600;
}

.badge--ssr {
  background: #dcfce7;
  color: #166534;
}

.badge--client {
  background: #ffedd5;
  color: #9a3412;
}

.button {
  display: inline-block;
  background: #0c172a;
  color: #fff;
  text-decoration: none;
  padding: 0.6rem 1rem;
  border-radius: 4px;
  width: fit-content;
}

.demo {
  background: #fff;
  border-radius: 8px;
  padding: 1.25rem;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.08);
}

.demo h2 {
  margin-top: 0;
}

.demo ol {
  margin: 0.75rem 0 0;
  padding-left: 1.2rem;
  display: grid;
  gap: 0.4rem;
}

@media (max-width: 600px) {
  .page {
    gap: 1rem;
  }

  .grid {
    grid-template-columns: 1fr;
  }
}
</style>