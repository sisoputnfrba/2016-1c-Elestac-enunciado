# Anexo I - Bloque de Control del Programa (PCB)

Al igual que en un sistema operativo convencional, todo Programa estará identificado por una estructura denominada PCB (Process Control Block - Bloque de Control del Programa) la cual contendrá el identificador único del proceso y el _**program counter (PC)**_ del programa. Con esta información, cada proceso CPU puede retomar la ejecución desde el punto que se encontraba la última vez que el proceso fue expropiado por el Núcleo.

Junto con los campos comunes del PCB, se almacenarán algunas estructuras auxiliares para el proceso (como los tres índices) con el fin de simplificar la comprensión del trabajo práctico y su posterior desarrollo.


| Estructura | Información |
|------------|-------------|
| Identificador Único | Número identificador del proceso único en el sistema |
| Program Counter | Número de la próxima instrucción del Programa que se debe ejecutar |
| Páginas de código | Cantidad de páginas utilizadas por el código del Programa AnSISOP, empezando por la página cero |
| Índice de código | Estructura auxiliar que contiene el offset del inicio y del fin de cada sentencia del Programa |
| Índice de etiquetas | Estructura auxiliar utilizada para conocer las líneas de código correspondientes al inicio de los procedimientos y a las etiquetas |
| Índice del Stack | Estructura auxiliar encargada de ordenar los valores almacenados en el Stack |

## Índice de Código

AnSISOP es un lenguaje interpretado, por lo que las líneas de código pueden tener distintas longitudes, además de poder existir líneas en blanco o comentarios.

Dado que la UMC atiende únicamente solicitudes relacionadas con posiciones de memoria, el índice de código es una estructura que almacena el desplazamiento respecto al inicio del código del programa y la longitud de cada línea ejecutable.

De esta manera es posible saber la ubicación de cada línea *útil* en el código para poder solicitarla a la UMC.

Es vital recordar que el índice **no es de líneas sino de instrucciones**: el código para generar este índice recorrerá el código de un programa ignorando las primeras líneas vacías o con comentarios hasta encontrar la primer instrucción válida, registrará su inicio y su longitud y, luego, buscará la siguiente ignorando saltos y comentarios. Repetirá el procedimiento hasta el fin del archivo.

Por ejemplo, para un script como el siguiente:

```
#!/usr/bin/ansisop
begin
# primero declaro las variables
variables a, b
a = 20

print a

end
```

La instrucción 0 (`variables a, b`) comienza en el byte 57, con una longitud de 14 bytes, mientras que la instrucción 1 (`a = 20`) comienza en el byte 72 y mide 6 bytes. Se recomienda utilizar un array de dos valores enteros como el descrito a continuación:

![Índice de código](indice-codigo.png)

Al momento de ejecutar una instrucción, la CPU solicitará a la UMC la entrada del Índice correspondiente a la instrucción a ejecutar, determinada por el Program Counter. Teniendo el byte de inicio y longitud de la instrucción, realizará la petición a la UMC en el correspondiente valor de `Pagina`, `Offset` y `Longitud`.

## Índice de Etiquetas

El Índice de Etiquetas consta de la serialización de un diccionario que asocia el identificador de cada función y etiqueta del programa con la primer instrucción ejecutable de la misma (es decir, el valor que el Program Counter deberá tomar cuando el programa deba pasar a ejecutar esa función o saltar a esa etiqueta).

## Índice del Stack

Esta estructura, también parte del PCB, es una manera auxiliar de simplificar la operatoria del segmento de Stack de un proceso.

Cada vez que un Programa AnSISOP ingrese a una función, se deberá agregar un elemento a este índice con los siguientes atributos:


| Nombre | Tipo | Descripción |
|--------|------|-------------|
|Argumentos (args) | Lista de Argumento | Posiciones de memoria donde se almacenan las copias de los argumentos de la función |
| Variables (vars) | Lista de Variable | Identificadores y posiciones de memoria donde se almacenan las variables locales de la función |
| Dirección de Retorno (retPos) | Numérico | Posición del índice de código donde se debe retornar al finalizar la ejecución de la función |
| Posición de la variable de retorno (retVar) | Posición de memoria (Pag#, Offset, Size) | Posición de memoria donde se debe almacenar el resultado de la función provisto por la sentencia RETURN |

### Ejemplo de Índice de Stack

Para ejemplificar el Índice del Stack, utilizaremos el siguiente código AnSISOP:

```
#!/usr/bin/ansisop
begin
variables a,g
    a = 1
    g <- doble a
    print g
end

function doble
variables f
    f = $0 + $0
    return f
end
```

Como se puede observar en el código, existen dos funciones: la principal y la función doble.

Al iniciar el programa, se crea la el siguiente registro en la estructura de stack.

| `pos` | `args` | `vars` | `retPos` | `retVar` |
|-------|--------|--------|----------|----------|
| 0 | &nbsp; | ID: (Pag, Off, Size)<br />`a: (0, 0, 4)`<br />`b: (0, 4, 4)` | &nbsp; | &nbsp; |

Como se puede observar, la función principal no tiene argumentos, ni debe retornar por lo que esos campos están vacíos.

El programa ejecuta y, en un punto, llega a la línea de código útil número 3: `g <- doble a`

Aquí se debe dejar registrado en una nueva posición de esta estructura que, al concluir con la función `doble`, debe regresar a la posición 3 del código y debe almacenar el resultado en la variable `g`.

| `pos` | `args` | `vars` | `retPos` | `retVar` |
|-------|--------|--------|----------|----------|
| 0 | &nbsp; | ID: (Pag, Off, Size)<br />`a: (0, 0, 4)`<br />`g: (0, 4, 4)` | &nbsp; | &nbsp; |
| 1 | (Pag, Off, Size)<br />`(0, 8, 4)` | ID: (Pag, Off, Size)<br />`f: (0, 12, 4)` | 3 | (Pag, Off, Size)<br />`(0, 4, 4)` |

La dirección de retorno es la línea 3, y la dirección de la variable de retorno coincide con la de la variable `g`.

La función `doble` recibe un argumento (en el ejemplo el valor de `a`, que es 1), el cual debe ser copiado a una nueva posición de memoria, y tiene una variable (`f`) la cual debe ser creada en otro espacio de memoria.

Al concluir la función `doble` y la variable `g` asignada con el resultado, este nuevo registro del índice de Stack debe ser eliminado.
