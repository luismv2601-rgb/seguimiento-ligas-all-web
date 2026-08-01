# Seguimiento Ligas All — Web

Página web móvil para visualizar el seguimiento de las ligas activas del proyecto [`seguimiento-ligas-all`](https://github.com/luismv2601-rgb/seguimiento-ligas-all), diseñada para instalarse en la pantalla de inicio del celular.

**En vivo:** https://luismv2601-rgb.github.io/seguimiento-ligas-all-web/

## Alcance

Página estática (`index.html`) optimizada para móvil que lee datos publicados como CSV desde el Google Sheet del proyecto y los muestra en tiempo real. No requiere servidor ni backend propio — es 100% gratis vía GitHub Pages.

**Ligas activas:** hoy son 24 (12 europeas, 7 sudamericanas, 3 norteamericanas y 2 asiáticas). La página **no las tiene hardcodeadas**: arma la lista con lo que encuentra en la hoja `Racha_Actual`, incluida la columna `region`, así que agregar o quitar una liga en `ligas.json` del proyecto de datos se refleja acá sin tocar código.

## Tema claro y oscuro

La paleta está en variables CSS: **claro por defecto**, y el oscuro se aplica con `data-tema="oscuro"` en `:root`. El botón es un círculo de 30px arriba a la derecha; el ícono muestra a dónde se va, no dónde se está.

La elección se guarda en `localStorage` y se aplica en un `<script>` del `<head>`, **antes de pintar**, para que quien elige oscuro no vea un flash blanco al abrir.

Los colores del semáforo, de las cards de racha y de los badges quedan fijos a propósito: son semánticos y funcionan sobre los dos fondos.

### Pantalla principal — "Detalle de las ligas"

- **Buscador** de texto que filtra en vivo por nombre de liga o país.
- Un bloque **"En alerta"** arriba de todo con las ligas que superaron su umbral, sin importar el continente. No se repiten después dentro de su grupo; el encabezado del continente avisa cuántas tiene arriba.
- Debajo, **los continentes como acordeón**, plegados por defecto: se ven solo los nombres y se despliegan al tocarlos.
- Cada fila muestra: pastilla del país, nombre de la liga, país, **% de partidos en serie**, y a la derecha **racha actual / umbral / récord histórico** (ej. `9/5/15`) más un semáforo.

#### Pastillas de país, no banderas

Los emoji de bandera los dibuja el sistema operativo: salen ondeados, ocupan más alto del necesario, y en varios Android ni siquiera existen —ya se veían como dos letras—. En su lugar hay una pastilla plana con el código del país y el color dominante de su bandera (`PAISES` en `index.html`, 52 países).

El color del texto **se calcula por luminancia**, porque sobre el amarillo de Colombia y Ecuador o el celeste de Argentina el blanco no se lee.

#### El orden dentro de cada grupo

1. **Semáforo**.
2. **`pct_secuenciales` descendente**.
3. **`racha / umbral`** como último desempate.

El segundo criterio no es cosmético. En una liga secuencial la racha es un dato limpio; en una que juega en paralelo, el orden entre partidos que arrancan a la misma hora lo decide la API, así que la racha puede variar en ±1. Entre dos ligas en alerta conviene mirar primero la que da el dato más confiable.

#### Detalles de implementación del acordeón

- Se guarda en `localStorage` qué grupos están **abiertos**, no cuáles están cerrados: así la primera visita, sin nada guardado, arranca con todo plegado.
- **Al buscar los grupos estorban**, así que los resultados salen planos.
- El orden de los continentes está en `ORDEN_CONTINENTES`, con América primero. Lo que no figure ahí se agrega al final por orden alfabético.
- El `onclick` recibe el **id normalizado** (`Sudamerica`), no el nombre. Pasar `"Sudamérica"` por el atributo obliga a comillas dentro de comillas y rompe el HTML — fue un bug real. El nombre viaja en `data-region`.

### Detalle por liga (al hacer clic en una fila)

Se abre una vista propia con una card de racha vigente arriba, y 4 sub-pestañas:

- **Análisis** — ficha con los campos de la pestaña `Analisis` del Sheet (excepto `liga_id`).
- **Partidos** — todos los partidos de la **temporada actual** de esa liga. La temporada no se deduce de la fecha del último partido, porque en las ligas europeas la temporada 2026 se juega entre agosto de 2026 y mayo de 2027; se pregunta al Sheet con `select max(E)`.
- **Extremos** — lee la hoja `Analisis 2`. Arriba, una escala que ubica la racha de hoy contra el umbral, el doble y el récord histórico, con una frase que interpreta dónde cayó. Abajo, un bloque por temporada con cuántas rachas llegaron al doble del umbral, qué porcentaje representan sobre los empates de esa temporada y los largos concretos como chips, con el mayor resaltado.
- **Próximos** — lee la hoja `Proximos`: los partidos con hora confirmada de los próximos 14 días, agrupados por día y con la distancia en lenguaje natural (hoy, mañana, en 3 días).

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

**Ojo con `Proximos`:** trae una fila de título antes de los encabezados (`parseCSVDesde(texto, 1)`).

**Ojo con `region`:** la columna vive al final de `Racha_Actual`, no en el medio, porque `actualizar.py` escribe las rachas por rango `D:J` y meterla antes correría las columnas. La mantiene al día `asegurar_region()` en cada corrida horaria.

---

## Nota sobre visibilidad del repositorio

Este repositorio es **público** porque GitHub Pages gratuito requiere repositorios públicos para servir sitios estáticos. No contiene ninguna credencial, API key ni información sensible — solo HTML y JavaScript estático que lee CSVs de acceso público (resultados de fútbol).

---

Ver CHANGELOG.md para el historial de versiones.
