# Gestión del Tiempo

Tablero de trabajo académico y de proyectos, autocontenido en un único archivo HTML. No requiere instalación, servidor ni compilación: se abre `tablero.html` en un navegador y el programa entero (interfaz, lógica y estado) corre en el cliente. No depende de ninguna librería externa ni de conexión a red, salvo las dos funciones que explícitamente la solicitan (entregables en carpeta local y redacción de avisos por IA, descritas más abajo).

## Pestañas

**Tablero.** Columnas de proceso con tarjetas de trabajo. Cada tarjeta admite varios frentes y varios responsables simultáneos, etapas con casilla y sello de fecha y hora de creación y de cumplimiento, fecha de hito, fecha de corte, dependencias con otras tarjetas (grafo acíclico) y un entregable exigible que impide dar la tarea por completa mientras no se adjunte el archivo correspondiente. Las columnas pueden marcarse como cíclicas: el período (semanal, mensual o anual) es propio de cada tarjeta, no de la columna, y la marca de corte veda deliberadamente el último tramo del período (Domingo, última semana, Diciembre), que queda reservado como el día del juicio: al cruzarlo, toda tarea incumplida genera un registro no cíclico en la columna Urgente, permanente y única, que arde en un destello rojo y ámbar mientras tenga tareas pendientes. Al completar una tarjeta el programa pregunta si declararla finalizada; aceptar la asienta en la Bitácora y la retira del tablero.

**Horario.** Semana tipo con grupos de bloques fijos (cátedras, turnos, rutinas) de periodicidad indefinida. Los eventos del calendario correspondientes a la semana en curso se reflejan aquí de forma tenue, sin editarse desde esta vista.

**Calendario.** Eventos puntuales y series cíclicas con día explícito y cotas propias, agrupables en cursos de varias sesiones semanales. Los eventos pueden portar frentes y responsables como etiqueta de tiempo comprometido (sin contar como tareas), y los días con hitos de tarjetas del tablero muestran puntos que, al posar el puntero, despliegan la tarjeta completa.

**Frentes.** Líneas de trabajo transversales al tablero y al calendario. Cada frente acumula estadísticas propias: tareas activas y cerradas, avance medio, reincidencias cíclicas, tiempo total invertido y retraso medio, más una nota de 0 a 10 que pondera avance, completitud histórica y salud.

**Responsables.** Personas que toman los procesos y les dan término. Se organizan por Cargo (entidad propia, con nombre y color que heredan automáticamente todos sus responsables); cada ficha registra teléfono, correo electrónico y relación jerárquica («responde a», sin lazos circulares posibles), además de su propia cronometría de cierres.

**Desempeño.** Estrella radial con la nota de cada frente evaluable, más el promedio general.

**Gantt.** Cronograma acotado siempre al año en curso, con cinco resoluciones (semanal a anual) centradas en el día de hoy. Tiende las tarjetas del tablero, los procesos ya cerrados en la Bitácora y los eventos del calendario como tiempo comprometido, con los hilos de dependencia trazados en ámbar.

**Bitácora.** Crónica inmutable de los procesos terminados, en tabla ordenable por número correlativo, tiempo invertido o retraso frente a la fecha esperada de cierre. Cada entrada narra en prosa quién dio término al proceso, con qué frentes, en cuánto tiempo y con qué margen respecto a lo esperado.

**Informe.** Cuadro de mando de solo lectura que destila cifras cardinales del proyecto (avance, urgencias, entregables en deuda, tiempo invertido), flujo del tablero, ranking de frentes, pulso de los ciclos, productividad mensual, carga semanal de la agenda y hallazgos en prosa sobre dónde se fuga el tiempo.

**Avisos.** Tabla de tareas pendientes con su información asociada, su responsable y sus señas de contacto, lista para notificar. Permite redactar cada mensaje (o todos los vacantes de una vez) mediante la API de Anthropic, editarlo, copiarlo o despacharlo por correo.

## Persistencia

El estado completo vive en `localStorage`, bajo la clave `tablero.frentes.v5` (con migración automática desde versiones anteriores del esquema si el navegador aún las conserva). No hay servidor ni base de datos: cada navegador guarda su propia copia.

- **Exportar Recuerdos** guarda todo el estado en un archivo `recuerdos.json` descargable.
- **Importar Recuerdos** lo recupera: busca el archivo junto al propio `tablero.html` cuando este se sirve por la web, y abre el selector de archivos cuando el navegador veda esa lectura (caso típico al abrir el archivo directamente desde el disco).
- **Reset** devuelve el tablero a su estado de fábrica; es irreversible sin un respaldo previo.

## Funciones que piden permisos del navegador

**Entregables en carpeta local.** Al exigir y recibir un entregable, el programa ofrece archivarlo en una carpeta local mediante la File System Access API (el identificador de esa carpeta se guarda en IndexedDB, base `gestion-tiempo`). Esta API solo existe en navegadores basados en Chromium (Chrome, Edge); en los demás, el entregable queda igualmente registrado (nombre, tamaño, fecha) pero sin copia física, y el candado de completitud funciona idéntico en ambos casos.

**Redacción de avisos por IA.** La pestaña Avisos llama directamente a la API de Anthropic (`api.anthropic.com/v1/messages`) desde el navegador. Requiere una clave API propia, que se ingresa en el campo correspondiente y se guarda únicamente en el `localStorage` de ese navegador, bajo la clave `tablero.claveIA.v1`: **nunca viaja dentro de los Recuerdos exportables** ni se transmite a ningún otro destino. La API de Anthropic no ofrece nivel gratuito; conviene fijar un tope de gasto en la consola de desarrolladores (`platform.claude.com`) antes de usar esta función con regularidad.

## Uso

Basta con abrir `tablero.html` en cualquier navegador moderno. Para acceder a él como página web propia, con URL fija en lugar de un archivo local, puede servirse con GitHub Pages (Settings → Pages → Deploy from a branch) directamente desde este repositorio: al ser autocontenido, ninguna configuración adicional es necesaria.
