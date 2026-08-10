# Changelog

## v5.3.1 - 2026-08-09

Salen otras 4 ligas del sistema, las que juegan casi todo en paralelo.

- **Fuera 3 banderas:** Luxemburgo, Montenegro y Estados Unidos. `BANDERAS` queda en **57**
- **Inglaterra se queda**, aunque salió el Championship: le queda la Premier League. Es la trampa de este cambio — el país sigue teniendo liga activa, así que borrar su bandera la habría dejado con una pastilla gris
- Con Luxemburgo afuera, la nota sobre el rojo-blanco-azul pierde una: Países Bajos ya solo se compara contra Croacia, que lleva escudo
- Ningún grupo desaparece: Norte y Centroamérica baja a 8 ligas pero sigue

## v5.3.0 - 2026-08-09

Se retiran 4 ligas del sistema y con ellas el continente asiático.

- **Fuera el grupo `Asia`.** China y Corea del Sur eran las dos únicas ligas asiáticas, así que salió de `ORDEN_CONTINENTES`. Quedan tres grupos: Sudamérica, Norte y Centroamérica, Europa
- **Fuera 4 banderas:** Irlanda del Norte, Kosovo, China y Corea del Sur. `BANDERAS` queda en **60**, una por cada país con liga activa. También salen de `PAISES`, el diccionario de respaldo
- La nota sobre las cruces rojas sobre blanco pasa de tres banderas a dos: sin Irlanda del Norte quedan Inglaterra, que va limpia, y Georgia con los cuatro cuadrados. El disco central ya no hace falta para distinguirlas
- Las ligas dejan de aparecer porque se borraron sus filas del Sheet desde el proyecto de datos. Sacarlas de `ligas.json` no alcanzaba: esta página arma la lista desde `Racha_Actual`

## v5.2.0 - 2026-08-06

Rediseño de la escala del cuadro de detalle. Antes los cuatro valores iban en una fila de texto debajo de la barra, sin relación visual con el punto que nombraban.

- **Umbral, doble y récord pasan arriba de la barra**, cada uno posicionado sobre el punto exacto que le corresponde en la escala
- **La racha de ahora va debajo**, con un marcador propio que atraviesa la barra entera. Va en azul oscuro y no en el color de las marcas fijas, para que se lea como el dato y no como otra referencia
- Textos de 11 a **12px** y números de 11 a **17px en negrita**
- **"¿Qué tan rara es esta racha?" y la frase quedan centradas**
- Las referencias que caen demasiado cerca se fusionan en una sola etiqueta (`Doble/Récord 12`), que es lo que pasa cuando el récord de una liga coincide con el doble de su umbral. Y contra los bordes la etiqueta se apoya en el borde en vez de centrarse, si no quedaba cortada cuando la racha está en 0 o en su máximo
- El valor mas alto de la escala (casi siempre el récord) queda al 98% del ancho y no al 92%: el aire sobrante al final pasa de 8% a 2%, lo justo para que su tick no se corte contra el borde. Las marcas intermedias se reparten mejor a lo largo de toda la barra
- Los ticks de referencia pasan a oscuros: sobre el tramo ya relleno de blanco, un tick blanco desaparecía

## v5.1.0 - 2026-08-06

- **El bloque "En alerta" se ordena por quién juega antes**, y a igualdad de horario por mayor % en serie. Antes mandaba el % en serie y la proximidad solo desempataba, así que una racha viva que jugaba en 2 horas podía quedar debajo de una que jugaba el sábado. Las que no tienen partido programado van al final
- **El cuadro de color del detalle pasa a mostrar "¿Qué tan rara es esta racha?"**: la barra con las marcas de umbral y doble, la leyenda Ahora/Umbral/Doble/Récord y la frase. Reemplaza al número grande de racha, al texto "partidos consecutivos sin empate", a la etiqueta y a la fecha de actualización — la racha y el umbral ya estaban en la leyenda, y la fecha ya está en el encabezado. La sub-pestaña Extremos queda solo con las rachas por temporada
- La escala dentro del cuadro va en blanco sobre el fondo de color: el relleno original era un degradado del mismo color que el cuadro y habría quedado invisible
- **Bandera y "← Volver" en la misma fila**, la bandera a la izquierda y el botón a la derecha. El botón estaba arriba de todo, en su propia línea
- Si una liga todavía no tiene fila en `Analisis 2` (recién agregada, esa pestaña se regenera una vez por día), el cuadro cae al formato viejo con el número grande

## v5.0.0 - 2026-08-02

Rediseño visual y salto de 24 a 43 ligas.

- **Banderas planas en vez de pastillas.** 43 banderas dibujadas como SVG en línea (~9 KB), cuadradas con esquinas de 3,5px. Son simplificadas a propósito: a 26px un escudo real es una mancha, así que solo se dibuja la forma mínima que separa a las que se confundirían — el escudo de Eslovaquia y Eslovenia frente al tricolor ruso, la hoja de Canadá frente a Perú, y un círculo, cinco estrellas y un triángulo para los tres azul-blanco-azul centroamericanos. `PAISES` queda como fallback
- **Franja azul marino** en el título, igual en los dos temas
- **Todo cuadrado**: se quita el redondeo de filas, buscador, tarjetas, fichas, chips, sub-pestañas, encabezados, semáforo y botones flotantes. Las banderas son la única excepción
- **Las ligas en alerta aparecen también dentro de su continente**, ya no salen del grupo. El encabezado pasa de `N arriba` a `N con alarma`, y el primer número es el total real del continente
- **Cuenta regresiva** al próximo partido en el bloque de alerta, actualizada cada minuto con un único temporizador. Usa el offset de Perú explícito y no la zona del teléfono
- La fila se reparte para que **las de alerta midan lo mismo que las demás**: racha y cuenta forman una columna de 54px alineada con el nombre y el país, y la cuenta ocupa espacio vertical que ya estaba libre. Sin cuenta, la columna se centra
- **Sub-pestañas reordenadas** a Extremos, Partidos, Próximos y Análisis, abriendo en Extremos
- `PAISES`, el fallback, pasa de 52 a 75 países, y se suman 19 ligas nuevas del proyecto de datos

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
