# Anexo III - Primitivas de AnSISOP

Para evitar la complejidad que presenta realizar un analizador de sintaxis y dado que estas tareas no son inherentes al contenido de la materia, se le facilita al alumno un Parser que se encargará de interpretar cada línea de código y de ejecutar las Primitivas correspondientes, cuyo código será desarrollado por el alumno. Tanto su implementación directa o parcial es aconsejable.

El parser podrá obtenerse desde https://github.com/sisoputnfrba/ansisop-parser

## Primitivas de AnSISOP
1. `definirVariable`
1. `obtenerPosicionVariable`
1. `dereferenciar`
1. `asignar`
1. `obtenerValorCompartida`
1. `asignarValorCompartida`
1. `irAlLabel`
1. `llamarFuncion`
1. `retornar`
1. `imprimir`
1. `imprimirTexto`
1. `entradaSalida`
1. `wait`
1. `signal`

### 1. `definirVariable`

Reserva en el Contexto de Ejecución Actual el espacio necesario para una variable llamada `identificador_variable` y la registra en el Stack, retornando la posición del valor de esta nueva variable del stack.

El valor de la variable queda indefinido: no deberá inicializarlo con ningún valor default.

Esta función se invoca una vez por variable, a pesar de que este varias veces en una línea. Por ejemplo, evaluar `variables a, b, c` llamará tres veces a esta función con los parámetros `a`, `b` y `c`.

```
t_posicion definirVariable(t_nombre_variable identificador_variable);
```

### 2. `obtenerPosicionVariable`

Devuelve el desplazamiento respecto al inicio del segmento Stack en que se encuentra el valor de la variable `identificador_variable` del contexto actual. En caso de error, retorna -1.

```
t_posicion obtenerPosicionVariable(t_nombre_variable identificador_variable);
```

### 3. `dereferenciar`

Obtiene el valor resultante de leer a partir de `direccion_variable`, sin importar cual fuera el contexto actual.

```
t_valor_variable dereferenciar(t_posicion direccion_variable);
```

### 4. `asignar`

Copia un valor en la variable ubicada en `direccion_variable`.

```
void asignar(t_posicion direccion_variable, t_valor_variable valor)
```

### 5. `obtenerValorCompartida`

Solicita al Núcleo el valor de una variable compartida.

```
t_valor_variable obtenerValorCompartida(t_nombre_compartida variable)
```

### 6. `asignarValorCompartida`

Solicita al Núcleo asignar el valor a la variable compartida. Devuelve el valor asignado.

```
t_valor_variable asignarValorCompartida(t_nombre_compartida variable, t_valor_variable valor)
```

### 7. `irAlLabel`

Devuelve el número de la primer instrucción ejecutable de etiqueta y -1 en caso de error.

```
t_puntero_instruccion irAlLabel(t_nombre_etiqueta etiqueta)
```

### 8. `llamarFuncion`

Preserva el contexto de ejecución actual para poder retornar luego al mismo, junto con la posición de la variable entregada por `donde_retornar`. Modifica las estructuras correspondientes para mostrar un nuevo contexto vacío. Retorna el número de instrucción a ejecutar.

Los parámetros serán definidos luego de esta instrucción de la misma manera que una variable local, con identificadores numéricos empezando por el 0.

No se pretende que se pueda retornar a una variable compartida. Sí a un parámetro o variable local.

```
t_puntero_instruccion llamarFuncion(t_nombre_etiqueta etiqueta, t_posicion donde_retornar,
t_puntero_instruccion linea_en_ejecuccion)
```

### 9. `retornar`

Modifica el Contexto de Ejecución Actual por el Contexto anterior al que se está ejecutando, recuperando el Cursor de Contexto Actual, el Program Counter y la dirección donde retornar, asignando el valor de retorno en esta, previamente apilados en el Stack.

```
t_puntero_instruccion retornar(t_valor_variable retorno)
```

### 10. `imprimir`

Envía al Núcleo el contenido de `valor_mostrar`, para que este le reenvíe a la correspondiente consola del Programa en ejecución. Devuelve la cantidad de caracteres impresos.

```
int imprimir(t_valor_variable valor_mostrar)
```

### 11. `imprimirTexto`

Envía al Núcleo una cadena de `texto` para que este la reenvíe a la correspondiente consola del Programa en ejecución. No admite parámetros adicionales, secuencias de escape o variables. Devuelve la cantidad de caracteres impresos.

```
int imprimirTexto(char* texto)
```

### 12. `entradaSalida`

Informa al Núcleo que el Programa actual pretende utilizar el `dispositivo` durante `tiempo` unidades de tiempo.

```
int entradaSalida(t_nombre_dispositivo dispositivo, int tiempo)
```

### 13. `wait`

Informa al Núcleo que ejecute la función wait para el semáforo con el nombre `identificador_semaforo`. El Núcleo deberá decidir si bloquearlo o no.

```
int wait(t_nombre_semaforo identificador_semaforo)
```

### 14. `signal`

Comunica al Núcleo que ejecute la función signal para el semáforo con el nombre `identificador_semaforo`. El Núcleo decidirá si esto conlleva desbloquear a otros procesos.

```
int signal(t_nombre_semaforo identificador_semaforo)
```