# Changelog

## v4.0.0 - 2026-08-01

Rediseño de la pantalla principal. Con 24 ligas activas la lista plana ya obligaba a recorrerla entera para ver qué estaba por saltar.

- **Tema claro y oscuro**, con el claro como predeterminado. Toda la paleta pasa a variables CSS (24 variables, 58 usos). El botón es un círculo de 30px arriba a la derecha; la elección se guarda y se aplica antes de pintar para que el oscuro no arranque con un flash blanco
- **La lista se agrupa por continente**, en acordeón y **plegada por defecto**: se ven solo los nombres y se despliegan al tocarlos. Se guarda qué grupos están abiertos —no cuáles están cerrados— para que la primera visita arranque con todo plegado. Al buscar, los grupos desaparecen y los resultados salen planos
- **Bloque "En alerta" arriba de todo** con las ligas que superaron su umbral, sin importar el continente. No se repiten dentro de su grupo; el encabezado avisa cuántas tiene arriba
- **Pastillas de país en vez de emoji de bandera.** Los emoji los dibuja el sistema operativo: salen ondeados, ocupan más alto del necesario y en varios Android ya se veían como dos letras. Ahora es una pastilla plana con el código y el color dominante de la bandera; el color del texto se calcula por luminancia, porque sobre el amarillo de Colombia y Ecuador el blanco no se lee
- **Lista más densa**: padding de fila de 14 a 9px, nombre de 15 a 13.5px, semáforo de 14 a 11px. Entran varias ligas más por pantalla sin perder área de toque
- **Ícono propio**: se reemplaza el emoji del corredor por un SVG de pelota plana, en el favicon y en el título
- **Sub-pestaña "Próximos"** con los partidos programados de los próximos 14 días, agrupados por día y con la distancia en lenguaje natural
- **Corrección:** el clic en los continentes no desplegaba nada. El `onclick` se generaba con `JSON.stringify(region)`, cuyas comillas dobles cerraban el atributo antes de tiempo y dejaban el handler roto. Ahora recibe el id normalizado y el nombre viaja en `data-region`

## v3.0.0 - 2026-08-01

El proyecto de datos pasó de 6 a 17 ligas activas, y eso rompió tres supuestos de la v2: el peso de la descarga, el orden alfabético y el diccionario de banderas.

- **Los partidos ya no se bajan enteros.** La hoja `Partidos` llegó a 1.034 KB y se descargaba completa al abrir la app, aunque se mirara una sola liga. Ahora el arranque pide solo `Analisis`, `Racha_Actual`, `Estado` y `Analisis 2` (~6 KB), y los partidos se piden al entrar a una liga, filtrados del lado de Google con consultas gviz (0,5 a 13 KB por liga). Se cachean por liga y el botón ↻ limpia la caché
- Las filas de partidos se filtran por `fecha` y no por `fixture_id`: esa columna tiene tipos mezclados en el Sheet y gviz devuelve vacías las celdas de la temporada en curso
- **Sub-pestaña nueva "Extremos"**, que lee la hoja `Analisis 2`: una escala que ubica la racha de hoy contra el umbral, el doble y el récord histórico, y un bloque por temporada con las rachas que llegaron al doble, su porcentaje sobre los empates y los largos concretos
- **El orden de la lista cambió**: primero el semáforo, y dentro de cada color por `pct_secuenciales` descendente. En una liga secuencial la racha es un dato limpio; en una que juega en paralelo el orden entre partidos simultáneos lo decide la API y la racha puede variar en ±1. `racha / umbral` quedó como tercer desempate
- Cada fila de la lista muestra ahora el **% de partidos en serie**
- `BANDERAS` pasa de 36 a 52 países: se suman Bielorrusia, Armenia y Letonia (que se veían con la bandera genérica) más 13 de Europa del este y del norte, para que estén listas al sumar ligas

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
