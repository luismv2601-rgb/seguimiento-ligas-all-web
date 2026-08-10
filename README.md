# Seguimiento Ligas All — Web

Página web móvil para visualizar el seguimiento de las ligas activas del proyecto [`seguimiento-ligas-all`](https://github.com/luismv2601-rgb/seguimiento-ligas-all), diseñada para instalarse en la pantalla de inicio del celular.

**En vivo:** https://luismv2601-rgb.github.io/seguimiento-ligas-all-web/

## Alcance

Página estática (`index.html`) optimizada para móvil que lee datos publicados como CSV desde el Google Sheet del proyecto y los muestra en tiempo real. No requiere servidor ni backend propio — es 100% gratis vía GitHub Pages.

**Ligas activas:** hoy son 57 (41 europeas, 8 de Norte y Centroamérica y 8 sudamericanas). La página **no las tiene hardcodeadas**: arma la lista con lo que encuentra en la hoja `Racha_Actual`, incluida la columna `region`, así que agregar o quitar una liga en `ligas.json` del proyecto de datos se refleja acá sin tocar código.

> Ojo con el "sin tocar código": vale para las filas, no para el resto. Un país nuevo necesita su entrada en `BANDERAS`, y un continente nuevo su lugar en `ORDEN_CONTINENTES`. Y **quitar una liga de `ligas.json` no la borra de acá**: mientras sus filas sigan en `Racha_Actual` la página la muestra igual, congelada. Se retiran del Sheet con `eliminar_liga.yml` en el proyecto de datos.

## Aspecto

**Franja azul marino** (`#0b2545`) con el título y la hora de actualización, igual en los dos temas.

**Todo cuadrado.** No hay esquinas redondeadas en ningún lado — filas, buscador, tarjetas, fichas, chips, sub-pestañas, encabezados, el semáforo y los dos botones flotantes. **La única excepción son las banderas**, que conservan 3,5px.

### Tema claro y oscuro

La paleta está en variables CSS: **claro por defecto**, y el oscuro se aplica con `data-tema="oscuro"` en `:root`. El botón está arriba a la derecha; el ícono muestra a dónde se va, no dónde se está.

La elección se guarda en `localStorage` y se aplica en un `<script>` del `<head>`, **antes de pintar**, para que quien elige oscuro no vea un flash blanco al abrir.

Los colores del semáforo, de las cards de racha y de los badges quedan fijos a propósito: son semánticos y funcionan sobre los dos fondos.

### Banderas planas, no emoji

`BANDERAS` en `index.html` trae **57 banderas dibujadas como SVG en línea**, en un lienzo 3×2, una por país con liga activa. Hoy la cuenta coincide con la de ligas, pero **no tiene por qué**: un país puede aportar más de una liga, como hacía Inglaterra con la Premier y el Championship. Al quitar una liga hay que fijarse si a su país le queda alguna otra antes de borrarle la bandera. Reemplazan a los emoji, que los dibuja el sistema operativo: salían ondeados, ocupaban más alto del necesario y en varios Android ni existían.

Son versiones **simplificadas a propósito**: a 26px un escudo real es una mancha. Donde dos banderas del set se confundirían se agrega la forma mínima que las separa — el escudo de Eslovaquia y Eslovenia frente al tricolor ruso, la hoja de Canadá frente a Perú, y un círculo, cinco estrellas y un triángulo para los tres azul-blanco-azul centroamericanos.

Llevan un borde interno de medio píxel: sin él, las que tienen blanco al borde (Polonia, Chequia) se funden con el fondo en tema claro.

`PAISES` sigue existiendo como **fallback**: si se agrega una liga de un país sin bandera dibujada, sale una pastilla con su código en vez de un hueco.

### Pantalla principal — "Detalle de las ligas"

**Buscador** de texto arriba, que filtra en vivo por nombre de liga o país, y debajo **tres pestañas**:

| Pestaña | Qué muestra |
|---|---|
| **En alerta** | Las ligas que superaron su umbral, sin importar el continente. Ordenadas por quién juega antes. Cada fila lleva un cuadrito para seleccionarla |
| **Ligas** | Las 57, agrupadas por continente en acordeón. Las que están en alerta **aparecen también acá**, dentro de su grupo |
| **Seleccionados** | Las que el usuario marcó con el cuadrito |

Reusan el CSS de `.subtabs`, el mismo de las cuatro sub-pestañas del detalle de liga. Abre siempre en **En alerta**. Las pestañas muestran su cantidad entre paréntesis; la de En alerta y la de Seleccionados se ocultan cuando están en cero.

Cada fila muestra: bandera, nombre de la liga, país, **% de partidos en serie**, y a la derecha **racha actual / umbral / récord histórico** (ej. `9/5/15`) más un semáforo.

#### La pestaña "Seleccionados"

Es una **lista de seguimiento manual**. Se marca desde **En alerta** tocando el cuadrito, y la liga **queda ahí hasta que se la destilde** — también si mientras tanto empató y se le cortó la racha. Por eso las filas de Seleccionados llevan su propio cuadrito: si no, una liga que dejó de estar en alerta ya no se podría destildar desde ningún lado.

> **Trampa:** la fila entera tiene un `onclick` que abre el detalle de la liga. El cuadrito hace `stopPropagation()`; sin eso, marcar una liga te abre su detalle.

La selección vive en `localStorage`, o sea **por navegador**: no viaja entre dispositivos ni se comparte con quien abra el mismo enlace. Una selección compartida pediría un backend, que este proyecto no tiene.

#### El buscador cambia de pestaña

Al escribir algo, la app salta sola a **Ligas**. Buscar es buscar en todo el catálogo: parado en En alerta o en Seleccionados, lo que se escribe casi nunca está ahí y la pantalla parecería vacía. El filtro igual se aplica a las tres pestañas, así que los contadores nunca mienten sobre lo que se ve.

#### Cuenta regresiva en las filas de alerta

Cada liga en alerta muestra cuánto falta para su próximo partido, sacado de la hoja `Proximos`. Formato corto (`8h 32m`, `5d 11h`) porque va en una columna angosta; `sin fecha` si no hay partido en los próximos 14 días.

Se actualiza sola cada minuto con **un único temporizador** para todas las cuentas visibles.

El instante del partido se arma con el **offset de Perú explícito** (`Date.UTC(..., hora + 5, ...)`) y no con la zona del teléfono, así la cuenta da bien aunque el usuario esté viajando.

#### Cómo se reparte la fila

La columna derecha se estira a lo alto de la fila. Dentro, la racha y la cuenta forman una sola columna de 54px: la racha arriba, a la altura del **nombre de la liga**, y la cuenta debajo, a la altura del **país**. El semáforo y el chevron quedan centrados.

Cuando no hay cuenta —la mayoría de las filas— la columna va centrada, para que la racha no quede arriba con un hueco debajo.

El resultado es que **las filas en alerta miden lo mismo que las demás**: la cuenta usa espacio vertical que la columna derecha ya tenía libre.

#### El orden

En **En alerta** y en **Seleccionados** manda quién juega antes: una racha viva que juega en dos horas pesa más que una que juega el sábado. Las que no tienen partido programado van al final.

Dentro de cada continente, en cambio:

1. **Semáforo**.
2. **`pct_secuenciales` descendente**.
3. **`racha / umbral`** como último desempate.

El segundo criterio no es cosmético. En una liga secuencial la racha es un dato limpio; en una que juega en paralelo, el orden entre partidos que arrancan a la misma hora lo decide la API, así que la racha puede variar en ±1. Entre dos ligas en alerta conviene mirar primero la que da el dato más confiable.

#### Detalles de implementación del acordeón

- Se guarda en `localStorage` qué grupos están **abiertos**, no cuáles están cerrados: así la primera visita, sin nada guardado, arranca con todo plegado.
- **Al buscar los grupos estorban**, así que los resultados salen planos.
- El orden de los continentes está en `ORDEN_CONTINENTES`, con América primero. Lo que no figure ahí se agrega al final por orden alfabético.
- El `onclick` recibe el **id normalizado** (`Sudamerica`), no el nombre. Pasar `"Sudamérica"` por el atributo obliga a comillas dentro de comillas y rompe el HTML — fue un bug real. El nombre viaja en `data-region`.

### El umbral de la app: μ+2σ, y no el del Sheet

**Desde el 2026-08-10 la app no usa `umbral_alerta` para decidir qué está en alerta.** Calcula **μ+2σ** por su cuenta, a partir de `promedio_racha` y `desviacion_std` de la hoja `Analisis` — que *es* el baseline 2024-2025.

Es una **prueba**: con la regla de producción (μ+1σ) saltaban ~43 alertas por mes y no se podía actuar. Con μ+2σ las ligas en alerta bajaron de 8 a 4.

> **Ojo:** el Sheet no se tocó. `umbral_alerta` sigue en μ+1σ y **Google Calendar sigue avisando con el criterio viejo**. O sea que la app y el Calendar no coinciden a propósito. Volver atrás es revertir el `index.html`.

El cuadro de color muestra cuatro referencias arriba de la escala:

| Marca | Qué es |
|---|---|
| **μ** | Promedio de racha de la liga. Va con un decimal: redondeado se confundiría con el umbral en las ligas de promedio alto |
| **μ+2σ** | El umbral de la prueba, el que pinta la alerta |
| **T5%** | Donde arranca el 5% de rachas más largas de esa liga |
| **R** | Récord histórico |

**μ+2σ y T5% caen casi siempre pegados** (difieren en 1 o menos en 38 de las 57 ligas), así que la etiqueta fusionada `μ+2σ/T5% 9` es el caso normal.

**T5%** sale de `u = ln(0,05) / ln(1−p)`, con p la tasa de empates de la liga. Las rachas son geométricas — "cuántos partidos hasta el próximo empate" —, así que promedio y desviación se derivan de p y no aportan información nueva; por eso el cuantil no los usa.

> **Trampa de redondeo:** `analisis_extremos.py` calcula el mismo μ+2σ del lado del backend para la pestaña Extremos. Usa `floor(x+0,5)` y **no** el `round()` de Python, que es bancario (`round(6,5)` da 6) y discreparía con el `Math.round()` de JavaScript. Si se toca uno de los dos, hay que tocar el otro.

### Detalle por liga (al hacer clic en una fila)

Se abre una vista propia con una card de racha vigente arriba, y 4 sub-pestañas en este orden. **Abre en Extremos**, que es lo que más se consulta:

- **Extremos** — lee la hoja `Analisis 2`. Un bloque por temporada con cuántas rachas llegaron a **μ+2σ**, qué porcentaje representan sobre los empates de esa temporada y los largos concretos como chips, con el mayor resaltado. La escala que ubica la racha de hoy vive arriba, en el cuadro de color (ver "El umbral de la app").
- **Partidos** — todos los partidos de la **temporada actual** de esa liga. La temporada no se deduce de la fecha del último partido, porque en las ligas europeas la temporada 2026 se juega entre agosto de 2026 y mayo de 2027; se pregunta al Sheet con `select max(E)`.
- **Próximos** — lee la hoja `Proximos`: los partidos con hora confirmada de los próximos 14 días, agrupados por día y con la distancia en lenguaje natural (hoy, mañana, en 3 días).
- **Análisis** — ficha con los campos de la pestaña `Analisis` del Sheet (excepto `liga_id`).

> El orden de los botones, el de los `<div class="subpage">`, cuál lleva `active` y la lista de `cambiarSubpagina()` tienen que coincidir. Si se desfasan, la app abre en una pestaña vacía.

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

**Ojo con `Analisis 2`:** arranca con dos filas de título antes de los encabezados (de ahí `parseCSVDesde`), y sus celdas de temporada son texto legible del estilo `6 de 81 (7,4%) -> 20, 13, 12`. La página las parsea con `RE_CELDA_EXTREMO`. Ese formato lo genera `analisis_extremos.py` en el proyecto de datos: si cambia allá, hay que cambiar la expresión acá. Desde el 2026-08-10 la columna del corte se llama **`umbral_mu2s`** (antes `doble_umbral`) y las celdas cuentan rachas ≥ μ+2σ; el formato del texto no cambió, solo su significado.

**Ojo con `Proximos`:** trae una fila de título antes de los encabezados (`parseCSVDesde(texto, 1)`).

**Ojo con `region`:** la columna vive al final de `Racha_Actual`, no en el medio, porque `actualizar.py` escribe las rachas por rango `D:J` y meterla antes correría las columnas. La mantiene al día `asegurar_region()` en cada corrida horaria.

---

## Nota sobre visibilidad del repositorio

Este repositorio es **público** porque GitHub Pages gratuito requiere repositorios públicos para servir sitios estáticos. No contiene ninguna credencial, API key ni información sensible — solo HTML y JavaScript estático que lee CSVs de acceso público (resultados de fútbol).

---

Ver CHANGELOG.md para el historial de versiones.
