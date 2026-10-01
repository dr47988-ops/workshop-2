<template>
    <div class="layout">
        <AppHeader />
        <main class="page-content">
            <slot />
        </main>
        <footer class="layout-footer">
            <p>Nuxt app</p>
        </footer>
    </div>
</template>

<style scoped>
.layout {
    min-height: 100vh;
    background: #f5f5f5;
    color: #1f2937;
    font-family: Arial, sans-serif;
}
.page-content {
    max-width: 960px;
    margin: 0 auto;
    padding: 2rem;
}

.layout-footer {
    border-top: #1f2937 1px solid;
    text-align: center;
    padding: 1rem 0;
    color: #52607a
}
</style>
