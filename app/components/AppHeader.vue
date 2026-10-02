<template>
    <header class="app-header">

        <div class="app-brand">
            <span class="pokeball">◉</span>
            <span>Pokédex</span>
        </div>

        <nav class="app-nav">
            <NuxtLink to="/" class="nav-link">
                SSR
            </NuxtLink>

            <NuxtLink to="/client" class="nav-link">
                Cliente
            </NuxtLink>

            <NuxtLink to="/versus" class="nav-link">
                Versus
            </NuxtLink>
        </nav>

    </header>
</template>

<style scoped>
.app-header {
    display: flex;
    justify-content: space-between;
    align-items: center;

    padding: 0.9rem 2rem;

    background: #0c172a;
    color: #ffffff;

    box-shadow: 0 3px 10px rgba(0, 0, 0, 0.15);
}

/* Nombre de la aplicación */
.app-brand {
    display: flex;
    align-items: center;
    gap: 0.55rem;

    color: #ffffff;

    font-size: 1.4rem;
    font-weight: 700;
}

/* Icono */
.pokeball {
    display: flex;
    align-items: center;
    justify-content: center;

    width: 28px;
    height: 28px;

    border-radius: 50%;
    border: 2px solid #ffffff;

    background: #ef4444;
    color: #ffffff;

    font-size: 1rem;
}

/* Navegación */
.app-nav {
    display: flex;
    align-items: center;
    gap: 0.5rem;
}

/* Links */
.nav-link {
    color: #e2e8f0;
    text-decoration: none;

    font-weight: 600;

    padding: 0.5rem 0.8rem;
    border-radius: 8px;

    transition: all 0.2s ease;
}

/* Efecto al pasar el mouse */
.nav-link:hover {
    color: #ffffff;
    background: rgba(255, 255, 255, 0.1);
}

/* Página seleccionada */
.nav-link.router-link-exact-active {
    color: #0c172a;
    background: #facc15;
}

/* Responsive */
@media (max-width: 600px) {

    .app-header {
        padding: 0.75rem 1rem;
    }

    .app-brand {
        font-size: 1.05rem;
        gap: 0.35rem;
    }

    .pokeball {
        width: 23px;
        height: 23px;
        font-size: 0.85rem;
    }

    .app-nav {
        gap: 0.15rem;
    }

    .nav-link {
        font-size: 0.78rem;
        padding: 0.4rem 0.5rem;
    }
}
</style>