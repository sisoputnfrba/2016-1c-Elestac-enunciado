# Proceso Unidad de Memoria Central (UMC)

El Proceso UMC[^7] es el responsable en el sistema de brindar a los Programas espacio en memoria para que estos realicen sus operaciones. Simulará un mecanismo de paginación por demanda, utilizando una TLB y un espacio de swap. Para esto utilizará una serie de estructuras administrativas internas que deberá crear y mantener.

Controlará la cantidad de marcos otorgados a cada proceso, siendo este un valor configurable, con asignación fija y reemplazo local. La asignación de frames a un determinado Programa se hará efectiva sólo cuando este lo necesite, pudiendo nunca llegar al límite mencionado. 

## Arquitectura de la UMC

Al iniciar, solicitará **un bloque de memoria contigua[^8]** de tamaño configurable por archivo de configuración, para simular la memoria principal del sistema. Luego creará las estructuras administrativas necesarias para poder gestionar dicho espacio, permitiendo a cada Programa AnSISOP en funcionamiento utilizar un conjunto de páginas de tamaño fijo.

A la UMC se conectarán, mediante sockets, el proceso Núcleo y los diversos CPUs.  Por cada conexión, la UMC creará un hilo dedicado a atenderlo, que quedará a la espera de solicitudes de operaciones. La UMC deberá validar cada pedido recibido, y responder en consecuencia.

* Ante cualquier solicitud de acceso a la tabla de páginas o a memoria principal, el proceso UMC deberá esperar una cantidad de tiempo configurable -en milisegundos, e igual para ambos tipos de acceso-, simulando el tiempo de acceso a memoria.
* Es importante destacar la naturaleza multi-hilo del proceso, por lo que será parte del desarrollo del trabajo atacar los problemas de concurrencia que surgieran.
* Para simplificar el desarrollo de este proceso, a diferencia de la realidad, las estructuras administrativas no deben ser almacenadas en el mismo espacio de memoria utilizado por los Programas.

## Cache TLB

La UMC utilizará una estructura en memoria que emulará una única cache TLB, con las columnas que sean necesarias para su buen funcionamiento. Su cantidad de entradas será configurable, y no variará durante la ejecución de este proceso. También se deberá poder deshabilitar el uso de esta caché con una opción en el archivo de configuración. El algoritmo de reemplazo a utilizar aquí será LRU. Ante un cambio de proceso, se realizará una limpieza (`flush`) en las entradas que correspondan.

## Operaciones de la UMC

El Proceso UMC, simulando aspectos de un controlador de memoria real, maneja una interfaz reducida, que <u>no puede ser ampliada</u>.

### Inicializar programa
Parámetros: [Identificador del Programa] [Páginas requeridas]

Cuando el proceso Núcleo comunique el inicio de un nuevo Programa AnSISOP, se crearán las estructuras necesarias para administrarlo correctamente. Además, deberá informar tal situación al Proceso Swap, junto con la cantidad de páginas de datos a utilizar, para que este asigne el espacio necesario en su partición.

### Solicitar bytes de una página
Parámetros: [#página], [offset] y [tamaño]

Ante un pedido de lectura de página de alguno de los procesos CPU, se realizará la traducción a marco (frame) y se devolverá el contenido correspondiente. En caso de que la página no se encuentre en memoria principal, será solicitada al proceso Swap, corriendo luego el algoritmo correspondiente para cargarla en memoria principal.

### Almacenar bytes en una página
Parámetros: [#página], [offset], [tamaño] y [buffer]

Ante un pedido de escritura de página de alguno de los procesadores, se realizará la traducción a marco (frame), y se actualizará su contenido.

En caso de que la página no se encuentre en memoria principal, será solicitada al proceso Swap, corriendo luego el algoritmo correspondiente para cargarla en memoria principal. Es importante recordar que las escrituras a Swap se hacen únicamente cuando se reemplaza una página que fue modificada previamente.


### Finalizar programa
Parámetros:  [Identificador del Programa]

Cuando el proceso Núcleo informe el fin de un Programa AnSISOP, se deberá eliminar las estructuras usadas para administrarlo. Además, deberá informar tal situación al proceso Swap, para que este libere el espacio utilizado en su partición.

### Otras operaciones

También soporta algunas operaciones que simplifican el desarrollo del trabajo práctico y su operatoria.

* Handshake: [Tipo: Núcleo/CPU]
* Cambio de proceso activo: [Identificador del Programa]

### Algoritmos de reemplazo
Cuando un Programa AnSISOP necesite acceder a una página, es posible que esta no se encuentre en memoria principal. Para solucionar esta situación, se deberán implementar los algoritmos Clock y Clock Modificado, siendo ambos configurables.

## Consola

El Proceso UMC, al iniciar, quedará a la espera de comandos enviados por teclado permitiendo al menos las siguientes funcionalidades. El diseño de la misma y la sintaxis de los comandos queda a criterio del equipo.

* `retardo`: Este comando permitirá modificar la cantidad de milisegundos que debe esperar el proceso UMC antes de responder una solicitud. Este parámetro será de ayuda para evaluar el funcionamiento del sistema.
* `dump`: Este comando generará un reporte en pantalla y en un archivo en disco del estado actual de:
  * Estructuras de memoria: Tablas de páginas de todos los procesos o de un proceso en particular.
  * Contenido de memoria: Datos almacenados en la memoria de todos los procesos o de un proceso en particular.
* `flush`
  * `tlb`: Este comando deberá limpiar completamente el contenido de la TLB.
  * `memory`: Este comando marcará todas las páginas del proceso como modificadas.

## Archivo de Configuración

Al iniciar, el Proceso UMC deberá leer los siguientes parámetros de un archivo de configuración, el cual podrá ser parametrizable como primer argumento del programa. 

| Parámetro | Tipo de dato | Descripción |
|-----------|--------------|-------------|
| `PUERTO`  | [numérico] | Puerto TCP utilizado para recibir las conexiones del Núcleo y los CPUs |
| `IP_SWAP` | [ip] | Dirección IP del proceso Swap |
| `PUERTO_SWAP` | [numérico]  | Puerto TCP donde se encuentra escuchando el proceso Swap |
| `MARCOS` | [numérico] | Cantidad de marcos disponibles en el sistema |
| `MARCO_SIZE` | [numérico] | Valor en bytes del tamaño de marco del sistema |
| `MARCO_X_PROC` | [numérico] | Cantidad máxima de marcos asignable a cada Programa AnSISOP |
| `ALGORITMO` | [alfanumérico] | Algoritmo de reemplazo de páginas |
| `ENTRADAS_TLB` | [numérico] | Cantidad disponible de entradas en la TLB. 0 = Deshabilitada |
| `RETARDO` | [numérico] | Cantidad de milisegundos que el sistema debe esperar antes de responder una solicitud |

[^7] A pesar de que la implementación de la memoria virtual en un sistema real es una combinación entre el sistema operativo y el CPU (MMU), se implementa en este caso como un solo proceso aparte, para facilitar su comprensión e integración con el sistema

[^8] Utilizando alguna función de la familia de `malloc`