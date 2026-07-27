# Changelog

## v2.0.0 - 2026-07-27
- Rediseño completo de la navegación: se eliminan las pestañas superiores "Partidos" y "Ranking"; ahora solo existe "Detalle de las ligas" con un buscador de texto (filtra en vivo por nombre/país) y la lista de ligas
- Al hacer clic en una liga se abre su propia vista de detalle con 2 sub-pestañas: **Análisis** (todos los campos de la hoja Analisis, sin `liga_id`) y **Partidos** (todos los partidos de la temporada actual, calculada automáticamente, ya no solo los últimos 20)
- Se eliminan los `<select>` nativos del celular (mostraban texto gigante al desplegar); toda la interacción es con listas propias con estilo
- Cada fila de liga muestra ahora la bandera del país (diccionario con las 39 ligas del catálogo) y el formato de racha pasa de `racha/umbral` a **`racha/umbral/récord histórico`**
- Ícono cambiado de ⚽ a 🏃
- Ya no se consume la hoja `Ranking_Empates` desde la web

## v1.0.0 - 2026-07-27
- Página estática (`index.html`) para visualizar en el celular el proyecto `seguimiento-ligas-all`
- 3 pestañas: Resumen (semáforo por liga con umbral variable), Partidos (últimos 20 por liga, con badge de empate y modalidad secuencial/paralelo), Ranking (empates por equipo)
- Lista de ligas armada dinámicamente desde la hoja `Racha_Actual` (no hardcodeada)
- Sheet compartido como "cualquiera con el enlace" vía API de Drive, leído con el endpoint `/export?format=csv&gid=X`
- Publicado gratis vía GitHub Pages
