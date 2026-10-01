<template>
    <header class="app-header">
        <div class="app-brand">Pokédex</div>
        <nav class="app-nav">
            <NuxtLink to="/versus" class="nav-link">Versus</NuxtLink>
            <NuxtLink to="/" class="nav-link">SSR</NuxtLink>
            <NuxtLink to="/client" class="nav-link">Cliente</NuxtLink>
            <NuxtLink to="/about" class="nav-link">About</NuxtLink>
        </nav>
    </header>
</template>

<style scoped>
.app-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1rem 2rem;
    background: #0c172a;
    color: #ffffff;
}
.app-brand {
    font-size: 1.5rem;
    font-weight: bold;
}
.app-nav {
    display: flex;
    gap: 1rem;
}
.nav-link {
    color: #ffffff;
    text-decoration: none;
}
.nav-link:hover {
    text-decoration: underline;
}

.router-link-active {
    color: #38c1d9;
}

</style>            
