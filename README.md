# Seguimiento Ligas All — Web

Página web móvil para visualizar el seguimiento de las ligas activas del proyecto [`seguimiento-ligas-all`](https://github.com/luismv2601-rgb/seguimiento-ligas-all), diseñada para instalarse en la pantalla de inicio del celular.

## Alcance

Página estática (`index.html`) optimizada para móvil que lee datos publicados como CSV desde el Google Sheet del proyecto y los muestra en tiempo real. No requiere servidor ni backend propio — es 100% gratis vía GitHub Pages.

**Ligas activas:** Ekstraklasa (Polonia), Primera División (Perú), Serie A (Brasil), Liga Profesional Argentina, Liga MX (México), Primera A (Colombia). El proyecto de datos usa un flag `activa` en `ligas.json` para agregar o quitar ligas de este seguimiento — cuando cambie, esta página lo refleja automáticamente sin necesitar ningún cambio de código, porque arma la lista de ligas a partir de lo que encuentra en la hoja `Racha_Actual`.

**Pestañas:**

- **Resumen** — semáforo de racha por liga: verde, amarillo o rojo según qué tan cerca esté la racha actual del **umbral propio de cada liga** (a diferencia de la v2, acá el umbral no es fijo en 5 — se calcula estadísticamente por liga, ver el proyecto de datos).
- **Partidos** — selector de liga con card de racha activa (incluye récord histórico y % de partidos secuenciales) y listado de los últimos 20 partidos, marcando empates y si el partido se jugó en paralelo con otro de su misma ronda.
- **Ranking** — selector de liga con el ranking de equipos por porcentaje de empates.

La última actualización del sistema (hora Perú) se muestra al inicio. Un botón flotante ↻ permite recargar los datos sin reabrir la app.

---

## Cómo funciona la publicación de datos

A diferencia de la v2 (que usaba "Publicar en la web" desde el menú de Google Sheets), acá el Google Sheet se compartió programáticamente como **"Cualquiera con el enlace — Lector"** vía la API de Drive, y la página lee cada pestaña con el endpoint de exportación CSV:

```
https://docs.google.com/spreadsheets/d/{ID_DEL_SHEET}/export?format=csv&gid={ID_DE_LA_PESTAÑA}
```

Los `gid` de cada pestaña están al inicio del `<script>` en `index.html`. Si se agrega o quita una pestaña del Sheet, hay que actualizar esa lista.

---

## Nota sobre visibilidad del repositorio

Este repositorio es **público** porque GitHub Pages gratuito requiere repositorios públicos para servir sitios estáticos. No contiene ninguna credencial, API key ni información sensible — solo HTML y JavaScript estático que lee CSVs de acceso público (resultados de fútbol).

---

Ver CHANGELOG.md para el historial de versiones.
