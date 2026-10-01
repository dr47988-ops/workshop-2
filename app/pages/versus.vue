<template>
  <section class="page">
    <h1>SSR vs Cliente</h1>
    <p>Misma API (JSONPlaceholder), dos formas de pedir los datos.</p>

    <div class="grid">
      <article class="card card--ssr">
        <h2>Con SSR</h2>
        <p><code>await useFetch(...)</code></p>
        <ul>
          <li>El servidor pide los datos</li>
          <li>El HTML ya trae los posts</li>
          <li>Mejor para SEO y primera carga</li>
          <li>Log en la terminal de Nuxt</li>
        </ul>
        <NuxtLink to="/" class="button">Ver versión SSR</NuxtLink>
      </article>

      <article class="card card--client">
        <h2>Sin SSR (cliente)</h2>
        <p><code>onMounted</code> + <code>fetch</code></p>
        <ul>
          <li>El navegador pide los datos</li>
          <li>Primero loading, después lista</li>
          <li>HTML inicial sin posts</li>
          <li>Log en la consola del browser</li>
        </ul>
        <NuxtLink to="/client" class="button">Ver versión cliente</NuxtLink>
      </article>
    </div>

    <section class="demo">
      <h2>Demo en clase</h2>
      <ol>
        <li>Abrí cada ruta</li>
        <li>Clic derecho → Ver código fuente de la página</li>
        <li>Buscá un título como <code>sunt aut</code></li>
        <li>En SSR aparece. En Cliente no.</li>
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
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
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

.card ul {
  margin: 0;
  padding-left: 1.2rem;
  display: grid;
  gap: 0.35rem;
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

.demo ol {
  margin: 0.75rem 0 0;
  padding-left: 1.2rem;
  display: grid;
  gap: 0.4rem;
}
</style>
