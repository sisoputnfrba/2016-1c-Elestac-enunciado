# Proceso Swap

Este proceso simulará el administrador de memoria virtual de un sistema operativo. Será el encargado de administrar la memoria virtual del sistema, para ello, al iniciarse, creará un archivo de tamaño <u>configurable</u> (en bytes), el cual representará nuestra _partición de swap_, y quedará a la espera de la conexión del **proceso UMC**.

Inicialmente el archivo de swap deberá ser rellenado con el caracter `\0`, a fines de inicializar la partición[^9]. El tamaño de las páginas escritas en swap es <u>configurable</u>[^10], así como también el nombre de este archivo.

Para que el manejo del espacio libre y ocupado en esta partición sea sencillo, se utilizará un esquema de asignación contigua. La partición de swap será considerada inicialmente como un hueco del total de su tamaño, medido en cantidad de páginas que puede alojar. Ante la llegada de un Programa AnSISOP, asignará el tamaño necesario para que este sea guardado, dejando el espacio restante como libre. Esto mismo sucederá con los siguientes Programas AnSISOP puestos en ejecución. Al finalizar un Programa AnSISOP, el espacio que tenía asignado será marcado como libre.

Para administrar el espacio utilizado, se utilizará un BitMap para saber si un espacio está disponible o no, y además como complemento se empleará una estructura de control que deberá contar con (mínimamente) los siguientes campos: PID, número de página y la posición de la misma dentro del swap. <u>La unidad mínima de asignación será una página<u>[^11].

Cuando le sea informada la creación de un nuevo Programa AnSISOP, procederá a buscar un hueco donde quepa. En caso de que el espacio total disponible no sea suficiente, deberá rechazar el proceso, siendo su inicialización cancelada. En cambio, cuando la _fragmentación externa_[^12] sea la que no permite crear nuevos procesos, se deberá compactar la partición. Todos los pedidos que lleguen mientras dure este procedimiento deberán esperar a que el mismo finalice. El procedimiento de compactación deberá esperar una cantidad de tiempo <u>configurable</u> (en milisegundos), simulando el tiempo de compactación.

Ante un pedido de lectura de página realizado por el **Administrador de Memoria**, este módulo devolverá el contenido de esta página. Ante un pedido de escritura de página, sobreescribirá el contenido de esta página. Por último, cuando se le informe la finalización de un Programa AnSISOP, deberá borrarlo de la _partición de swap_ marcando las páginas ocupadas como disponibles.

## Archivo de Configuración

| Nombre de Campo | Valor de Ejemplo |
|-----------------|------------------|
| `Puerto_Escucha` | 6000 |
| `Nombre_Swap` | `swap.data` |
| `Cantidad_Paginas` | 512 |
| `Tamaño_Pagina` | 256[^13] |
| `Retardo_Compactacion` | 60000 |

[^9] Se recomienda al alumno investigar sobre la utilización del comando `dd` para crear los archivos

[^10] Recuerde que el tamaño de las **páginas** es el mismo que el de los **marcos**

[^11] Para más información sobre asignación contigua, remitirse al capítulo 11 de "Fundamentos de Sistemas Operativos" de Abraham Silberschatz

[^12] Es muy importante no confundir la fragmentación externa con la falta de espacio disponible. En el primer caso el espacio está, pero no puede ser asignado por estar fragmentado.

[^13] Recuerde que el tamaño de página debe ser el mismo en todo el sistema. Este valor puede hacerlo parametrizable por archivo de configuración o consultarlo al proceso UMC al establecer conexión.