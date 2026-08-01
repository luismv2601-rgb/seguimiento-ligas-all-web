# Seguimiento Ligas All — Web

Página web móvil para visualizar el seguimiento de las ligas activas del proyecto [`seguimiento-ligas-all`](https://github.com/luismv2601-rgb/seguimiento-ligas-all), diseñada para instalarse en la pantalla de inicio del celular.

**En vivo:** https://luismv2601-rgb.github.io/seguimiento-ligas-all-web/

## Alcance

Página estática (`index.html`) optimizada para móvil que lee datos publicados como CSV desde el Google Sheet del proyecto y los muestra en tiempo real. No requiere servidor ni backend propio — es 100% gratis vía GitHub Pages.

**Ligas activas:** hoy son 17 (12 europeas, 4 sudamericanas y Liga MX). La página **no las tiene hardcodeadas**: arma la lista con lo que encuentra en la hoja `Racha_Actual`, así que agregar o quitar una liga en `ligas.json` del proyecto de datos se refleja acá sin tocar código. El diccionario `BANDERAS` en `index.html` cubre 52 países, más que las ligas activas, para que la bandera ya esté lista cuando se sume una nueva.

### Pantalla principal — "Detalle de las ligas"

- **Buscador** de texto que filtra en vivo por nombre de liga o país.
- Cada fila muestra: bandera, nombre de la liga, país, **% de partidos en serie**, y a la derecha **racha actual / umbral / récord histórico** (ej. `9/5/15`) más un semáforo (verde/amarillo/rojo según qué tan cerca está la racha del umbral propio de esa liga — no hay un umbral fijo como en la v2).

#### El orden de la lista

Dos niveles:

1. **Semáforo** — lo que ya está en alerta, arriba.
2. **`pct_secuenciales` descendente** dentro de cada color.

El segundo criterio no es cosmético. En una liga secuencial la racha es un dato limpio; en una que juega en paralelo, el orden entre partidos que arrancan a la misma hora lo decide la API, así que la racha puede variar en ±1. Entre dos ligas en alerta conviene mirar primero la que da el dato más confiable. Como tercer desempate queda `racha / umbral`.

### Detalle por liga (al hacer clic en una fila)

Se abre una vista propia con una card de racha vigente arriba, y 3 sub-pestañas:

- **Análisis** — ficha con los campos de la pestaña `Analisis` del Sheet (excepto `liga_id`).
- **Partidos** — todos los partidos de la **temporada actual** de esa liga. La temporada no se deduce de la fecha del último partido, porque en las ligas europeas la temporada 2026 se juega entre agosto de 2026 y mayo de 2027; se pregunta al Sheet con `select max(E)`.
- **Extremos** — lee la hoja `Analisis 2`. Arriba, una escala que ubica la racha de hoy contra el umbral, el doble y el récord histórico, con una frase que interpreta dónde cayó. Abajo, un bloque por temporada con cuántas rachas llegaron al doble del umbral, qué porcentaje representan sobre los empates de esa temporada y los largos concretos como chips, con el mayor resaltado.

La última actualización del sistema (hora Perú) se muestra al inicio. Un botón flotante ↻ recarga los datos y limpia la caché de partidos.

---

## Cómo funciona la publicación de datos

A diferencia de la v2 (que usaba "Publicar en la web" desde el menú de Google Sheets), acá el Google Sheet se compartió programáticamente como **"Cualquiera con el enlace — Lector"** vía la API de Drive, y la página lee cada pestaña con el endpoint de exportación CSV:

```
https://docs.google.com/spreadsheets/d/{ID_DEL_SHEET}/export?format=csv&gid={ID_DE_LA_PESTAÑA}
```

Los `gid` de cada pestaña están al inicio del `<script>` en `index.html`. Si se agrega o quita una pestaña del Sheet, hay que actualizar esa lista.

### Los partidos no se bajan enteros

La hoja `Partidos` ya pesa más de 1 MB y crece con cada liga que se suma. Bajarla completa al abrir la app era el grueso del tiempo de carga, y para colmo la mayoría de esos datos no se miraban nunca.

Al abrir solo se piden `Analisis`, `Racha_Actual`, `Estado` y `Analisis 2` (~6 KB en total). Los partidos se piden **recién al entrar a una liga**, filtrados del lado de Google con el lenguaje de consultas de gviz:

```
https://docs.google.com/spreadsheets/d/{ID}/gviz/tq?tqx=out:csv&gid=0&tq=select * where B = 128 and E = 2026
```

Son dos consultas por liga: la temporada más reciente (~20 bytes) y sus partidos (0,5 a 13 KB). Se cachean por liga; el botón ↻ limpia la caché.

**Ojo con `fixture_id`:** esa columna tiene tipos mezclados en el Sheet — `cargar_historico.py` la escribe como número y `actualizar.py` como texto. gviz infiere un tipo por columna y devuelve **vacías** las celdas que no coinciden, que son justamente las de la temporada en curso. Por eso las filas se filtran por `fecha` y no por `fixture_id`.

**Ojo con los decimales:** la hoja usa configuración regional española (coma decimal, ej. `47,5`), y Google Sheets envuelve esos valores entre comillas en el CSV para no romper el formato (`"47,5"`). El parser de `index.html` (`parseCSVLinea`) respeta esas comillas, y `numeroDecimalComaOPunto()` convierte coma a punto antes de cualquier comparación numérica. Si se toca el parser, hay que mantener ambas cosas.

**Ojo con `Analisis 2`:** arranca con dos filas de título antes de los encabezados (de ahí `parseCSVDesde`), y sus celdas de temporada son texto legible del estilo `6 de 81 (7,4%) -> 20, 13, 12`. La página las parsea con `RE_CELDA_EXTREMO`. Ese formato lo genera `analisis_extremos.py` en el proyecto de datos: si cambia allá, hay que cambiar la expresión acá.

---

## Nota sobre visibilidad del repositorio

Este repositorio es **público** porque GitHub Pages gratuito requiere repositorios públicos para servir sitios estáticos. No contiene ninguna credencial, API key ni información sensible — solo HTML y JavaScript estático que lee CSVs de acceso público (resultados de fútbol).

---

Ver CHANGELOG.md para el historial de versiones.
