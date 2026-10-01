<script setup>
const route = useRoute()

const { data: post, pending, error } = await useFetch(
  () => `https://jsonplaceholder.typicode.com/posts/${route.params.id}`,
)

if (import.meta.server) {
  console.log('SSR: post cargado en el servidor', post.value?.id)
}
</script>

<template>
  <section class="page">
    <NuxtLink to="/" class="back">← Volver a posts</NuxtLink>

    <p v-if="pending">Cargando post...</p>
    <p v-else-if="error" class="error">No se pudo cargar este post.</p>

    <article v-else-if="post" class="post">
      <p class="post__meta">Post #{{ post.id }} · user {{ post.userId }}</p>
      <h1>{{ post.title }}</h1>
      <p>{{ post.body }}</p>
    </article>
  </section>
</template>

<style scoped>
.page {
  display: grid;
  gap: 1rem;
}

.back {
  color: #38c1d9;
  text-decoration: none;
}

.error {
  color: #b91c1c;
}

.post {
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.08);
  padding: 1.5rem;
  display: grid;
  gap: 0.75rem;
}

.post__meta {
  margin: 0;
  color: #52607a;
  font-size: 0.9rem;
}

.post h1 {
  margin: 0;
  text-transform: capitalize;
}

.post p {
  margin: 0;
  line-height: 1.5;
}
</style>
