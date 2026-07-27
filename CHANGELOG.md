# Changelog

## v1.0.0 - 2026-07-27
- Página estática (`index.html`) para visualizar en el celular el proyecto `seguimiento-ligas-all`
- 3 pestañas: Resumen (semáforo por liga con umbral variable), Partidos (últimos 20 por liga, con badge de empate y modalidad secuencial/paralelo), Ranking (empates por equipo)
- Lista de ligas armada dinámicamente desde la hoja `Racha_Actual` (no hardcodeada)
- Sheet compartido como "cualquiera con el enlace" vía API de Drive, leído con el endpoint `/export?format=csv&gid=X`
- Publicado gratis vía GitHub Pages
