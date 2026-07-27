# Seguimiento Ligas All — Web

Página web móvil para visualizar el seguimiento de las ligas activas del proyecto [`seguimiento-ligas-all`](https://github.com/luismv2601-rgb/seguimiento-ligas-all), diseñada para instalarse en la pantalla de inicio del celular.

**En vivo:** https://luismv2601-rgb.github.io/seguimiento-ligas-all-web/

## Alcance

Página estática (`index.html`) optimizada para móvil que lee datos publicados como CSV desde el Google Sheet del proyecto y los muestra en tiempo real. No requiere servidor ni backend propio — es 100% gratis vía GitHub Pages.

**Ligas activas:** Ekstraklasa (Polonia), Primera División (Perú), Serie A (Brasil), Liga Profesional Argentina, Liga MX (México), Primera A (Colombia). El proyecto de datos usa un flag `activa` en `ligas.json` para agregar o quitar ligas de este seguimiento — cuando cambie, esta página lo refleja automáticamente sin necesitar ningún cambio de código, porque arma la lista de ligas a partir de lo que encuentra en la hoja `Racha_Actual`. El diccionario `BANDERAS` en `index.html` ya cubre las 39 ligas del catálogo completo, no solo las activas, para que las banderas ya estén listas cuando se agreguen más.

### Pantalla principal — "Detalle de las ligas"

- **Buscador** de texto que filtra en vivo la lista por nombre de liga o país (pensado para cuando haya más ligas activas).
- Cada fila muestra: bandera del país, nombre de la liga, país, y a la derecha **racha actual / umbral / récord histórico** (ej. `2/6/20`) más un semáforo (verde/amarillo/rojo según qué tan cerca esté la racha del umbral propio de esa liga — no hay un umbral fijo como en la v2).

### Detalle por liga (al hacer clic en una fila)

Se abre una vista propia de esa liga con una card de racha vigente arriba, y 2 sub-pestañas:

- **Análisis** — ficha con todos los campos de la pestaña `Analisis` del Sheet (excepto `liga_id`): país, liga, temporadas analizadas, total de partidos y empates, promedio y desviación estándar de racha, umbral de alerta, racha máxima histórica, y partidos/porcentaje secuenciales.
- **Partidos** — todos los partidos de la **temporada actual** de esa liga (se calcula automáticamente como la temporada más reciente presente en los datos, no un número fijo), marcando empates y si el partido se jugó en paralelo con otro de su misma ronda.

La última actualización del sistema (hora Perú) se muestra al inicio. Un botón flotante ↻ permite recargar los datos sin reabrir la app.

---

## Cómo funciona la publicación de datos

A diferencia de la v2 (que usaba "Publicar en la web" desde el menú de Google Sheets), acá el Google Sheet se compartió programáticamente como **"Cualquiera con el enlace — Lector"** vía la API de Drive, y la página lee cada pestaña con el endpoint de exportación CSV:

```
https://docs.google.com/spreadsheets/d/{ID_DEL_SHEET}/export?format=csv&gid={ID_DE_LA_PESTAÑA}
```

Los `gid` de cada pestaña están al inicio del `<script>` en `index.html`. Si se agrega o quita una pestaña del Sheet, hay que actualizar esa lista.

**Ojo con los decimales:** la hoja usa configuración regional española (coma decimal, ej. `47,5`), y Google Sheets envuelve esos valores entre comillas en el CSV para no romper el formato (`"47,5"`). El parser de `index.html` (`parseCSVLinea`) respeta esas comillas, y `numeroDecimalComaOPunto()` convierte coma a punto antes de cualquier comparación numérica. Si se toca el parser, hay que mantener ambas cosas.

---

## Nota sobre visibilidad del repositorio

Este repositorio es **público** porque GitHub Pages gratuito requiere repositorios públicos para servir sitios estáticos. No contiene ninguna credencial, API key ni información sensible — solo HTML y JavaScript estático que lee CSVs de acceso público (resultados de fútbol).

---

Ver CHANGELOG.md para el historial de versiones.
