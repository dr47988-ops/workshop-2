# Pokédex con Nuxt y PokéAPI

Aplicación tipo Pokédex desarrollada con Nuxt utilizando la PokéAPI.
Muestra los primeros 20 Pokémon con su nombre, imagen y acceso a su detalle.
Cada Pokémon tiene una ruta dinámica `/pokemon/[name]` con sus tipos, altura, peso y habilidades.
La aplicación permite buscar Pokémon por nombre y muestra un mensaje cuando no se encuentra.
La ruta principal `/` utiliza `useFetch` para cargar la lista mediante SSR.
La ruta `/client` utiliza `onMounted` y `fetch` para cargar los Pokémon desde el navegador.
También se incluye la ruta `/versus` para comparar la carga SSR con la carga del lado del cliente.
La aplicación cuenta con estados de carga, manejo de errores y un diseño responsive.
Para ejecutar el proyecto se utiliza `npm install` y luego `npm run dev`.