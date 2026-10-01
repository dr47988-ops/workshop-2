<script setup>
const posts = ref([])
const pending = ref(true)
const error = ref(null)

onMounted(async () => {
  console.log('Cliente: pidiendo posts en el navegador')
  pending.value = true
  error.value = null

  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/posts')
    if (!response.ok) throw new Error('Respuesta no ok')
    const data = await response.json()
    posts.value = data.slice(0, 10)
  } catch (err) {
    error.value = err
  } finally {
    pending.value = false
  }
})
</script>

<template>
  <section class="page">
    <p class="badge">Modo cliente · onMounted + fetch</p>
    <h1>Posts (navegador)</h1>
    <p>
      Primero se pinta la página vacía/loading. Después el navegador pide los
      datos. En “Ver código fuente” <strong>no</strong> deberían aparecer los
      títulos. El log sale en la consola del navegador.
    </p>

    <p v-if="pending">Cargando posts...</p>
    <p v-else-if="error" class="error">No se pudieron cargar los posts.</p>

    <ul v-else class="posts">
      <li v-for="post in posts" :key="post.id" class="posts__item">
        <NuxtLink :to="`/posts/${post.id}`" class="posts__link">
          <strong>#{{ post.id }}</strong>
          {{ post.title }}
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
  background: #ffedd5;
  color: #9a3412;
  padding: 0.35rem 0.7rem;
  border-radius: 999px;
  font-size: 0.85rem;
}

.error {
  color: #b91c1c;
}

.posts {
  list-style: none;
  margin: 0;
  padding: 0;
  display: grid;
  gap: 0.75rem;
}

.posts__item {
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.08);
}

.posts__link {
  display: block;
  padding: 1rem;
  color: inherit;
  text-decoration: none;
}

.posts__link:hover {
  color: #38c1d9;
}
</style>
