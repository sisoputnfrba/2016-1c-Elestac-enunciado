# Anexo II – Especificación del Lenguaje AnSISOP

AnSISOP es un lenguaje de programación interpretado de propósito general y bastante bajo nivel. Su sintaxis es simple y, en general, no es muy recomendable para utilizar en tareas productivas. En cambio, su objetivo es principalmente académico: ayudar a entender los conceptos y mecanismos que un sistema operativo debe tener en cuenta para manejar la ejecución de los programas.

El lenguaje se divide en dos capas: la sintaxis de alto nivel, utilizada para escribir los scripts, y las operaciones Primitivas que la CPU deberá ejecutar para llevar a cabo las instrucciones de los primeros. El alumno deberá desarrollar el intérprete del programa y las diversas primitivas que serán invocadas.

Para la evaluación del trabajo práctico no se proveerán programas con errores de sintaxis ni de semántica.

## Sintaxis

* El lenguaje AnSISOP es case-sensitive; es decir, `hola` y `Hola` son diferentes
* El código principal del programa estará comprendido entre las palabras reservadas `begin` y `end`. `begin` solo indica por donde comenzará a ejecutar el programa.
* Las sentencias finalizan con un salto de línea. Los saltos adicionales son ignorados.
* Toda línea comenzada por un caracter numeral (`#`) es un comentario y debe ser ignorado.
* Todo programa deberá terminar con una línea en blanco.
* Un `:` seguido de una palabra es una etiqueta que será utilizada para permitir saltos dentro del código con `jump`, `jz` y `jnz`, explicados más adelante.
* Todas las variables dentro de una función son locales.
* Una función puede llamar a otra función.

### Variables

Las variables locales se declaran luego de la sentencia `variables`. Son solamente de tipo entero con signo y su identificador es un caracter alfabético [a-zA-Z]. Su valor no debe ser inicializado. Se las indica en el código solo con su nombre.

Las variables dadas como parámetros de funciones se nombran con un único dígito [0-9] y se accederá a ellas en el código anteponiendo el signo `$` (`$0` refiere al primer parámetro, y así).

Las variables compartidas[^15] se declaran e inicializan en la configuración del Núcleo, se nombran como cadenas sin restricción de nombre, y se las indica en el código como `!identificador`.

### Asignación

Con el nombre de la variable a la izquierda de un signo igual, se le podrá asignar a una variable como valor:
* Un número entero u otra variable (local, parámetro o compartida; no semaforos)
* El resultado de una operación aritmética la cual podrá ser suma o resta

### Salto condicional

Las instrucciones de salto condicional *saltar-si-no-es-cero* (`jnz` - _jump on not-zero_) y *saltar-si-es-cero* (`jz` - _jump on zero_) recibirán como parámetro una variable que evaluarán y una etiqueta a la que deberán saltar en caso de que se cumpla la condición.

Estas instrucciones, por definición del enunciado, tendrán como origen y destino el procedimiento actual. En otras palabras, el código de una función o procedimiento no podrá saltar dentro del código de otro.

> Este código de ejemplo incrementa la variable `i` de uno a diez e imprime dichos valores en pantalla
```
#!/usr/bin/ansisop
begin
variables i,b
	i = 1
	:inicio_for
	i = i + 1
	print i
	b = i - 10
	jnz b inicio_for
	#fuera del for
end
```
> Observe que si la variable `i` no es igual a 10 entonces `b = i - 10` no es cero, entonces la instrucción de salto condicional iría a la etiqueta `inicio_for` hasta llegar a la 10ma iteración, donde no saltará más.

### Entrada/Salida: `io`

La llamada al sistema `io` recibirá _dos_ parámetros.
1. Una cadena como identificador del sistema del dispositivo de entrada/salida
1. La cantidad de operaciones de entrada/salida que se realizarán en éste dispositivo

### Impresión en pantalla

Existen dos formas de impresión. `textPrint` seguido de una cadena imprimirá la cadena tal cual aparece en el código fuente. La palabra reservada `print` será utilizada para mostrar el valor de la variable que reciba como parámetro.

La información deberá ser mostrada en la terminal del programa y registrada en el log del sistema.

#### Ejemplo:
```
a = 0
textPrint La variable a vale
print a
```

#### Resultado:
```
La variable a vale
0
```

### Funciones

La definición de las funciones estará dada por la palabra reservada `function` seguida del nombre de la misma. Todas las funciones retornan un valor y no existe en el lenguaje el concepto de procedimiento.

#### Código de ejemplo

Este código de ejemplo imprime variables.

```
#!/usr/bin/ansisop
function prueba
	variables a,b
	a = 2
	b = 16
	print b
	print a
	a = a + b
	return a
end

begin
variables a, b
	a = 20
	print a
	b <- prueba
	print b
	print a
end
```

Lo que se ve por pantalla sería (Nótese la localidad/scope de las variables):

```
20
16
2
18
20
```

[^15] Llamamos "variables compartidas" a aquellas manejadas por el Núcleo, de las que todos los CPUs tiene acceso. No son "variables globales" ya que "global" refiere al scope/contexto de cada programa. No existen en AnSISOP las variables globales.
