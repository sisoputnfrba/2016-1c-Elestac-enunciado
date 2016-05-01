# Proceso CPU

Este proceso es uno de los más importantes del trabajo ya que es el encargado de interpretar y ejecutar las operaciones escritas en código AnSISOP de un Programa.

Estará en permanente contacto con el Proceso UMC, tanto para obtener información del Programa en ejecución, como para actualizar las estructuras requeridas luego de ejecutar una operación.

Al iniciar, se conectará al proceso Núcleo y quedará a la espera de que este le envíe el PCB de un Programa AnSISOP para ejecutarlo.

Incrementará el valor del registro Program Counter del PCB y utilizará el índice de código para solicitar a la UMC la próxima sentencia a ejecutar. Al recibirla, la parseará, ejecutará las operaciones requeridas, actualizará los valores del Programa en la UMC, actualizará el Program Counter en el PCB y notificará al Núcleo que concluyó un **quantum**[^14].

### Ejemplo

Luego de recibir de la UMC la instrucción `a = b + 3`, el parser la interpretará y ejecutará las siguientes **primitivas**:

1. `obtener_direccion('b')`: utilizando el índice del stack, devolverá la posición de la variable `b`. Por ejemplo: Pag: 5, Offset: 8, Size: 4.
2. `obtener_valor(Pag: 5, Offset: 8, Size: 4)`: pedirá a la UMC el valor de la variable `b`, en este caso los 4 bytes a partir del offset 8 de la página 5. Supongamos b = 9.
3. `obtener_direccion('a')`: como 1), pero para saber dónde guardar el resultado. Por ejemplo, Pag: 4, Offset: 0, Size: 4.
4. `almacenar(Pag: 4, Offset: 0, Size: 4, 11)`: almacenará en la UMC el resultado de la suma `(9 + 3 = 11)`.

El ingreso a funciones o procedimientos requiere que se asienten datos como el punto de retorno o las variables locales en el índice de Stack - Ver detalle en el Anexo IV - Stack

## Hot plug

En cualquier momento de la ejecución del sistema pueden conectarse nuevas instancias del proceso CPU. Será responsabilidad del Núcleo aceptar esas nuevas conexiones, y tener en cuenta a las nuevas CPUs a la hora de planificar.

Mediante la señal SIGUSR1, se le podrá notificar a un CPU que deberá desconectarse una vez concluida la ejecución del Programa actual, dejando de dar servicio al sistema.

## Fin de la ejecución

Al ejecutar la última sentencia, el CPU deberá notificar al Núcleo que el proceso finalizó para que este se ocupe de solicitar la eliminación de las estructuras utilizadas por el sistema.

## Excepciones

Existe la posibilidad que el Proceso CPU reciba un mensaje de excepción como resultado de una solicitud a la UMC. En este caso deberá notificarla por pantalla y concluir la ejecución del Programa actual.

[^14] En este punto el alumno ya puede notar que si el proceso fuera desalojado y su PCB enviado a otro CPU, este tendría ahí y en la UMC toda la información necesaria para continuar su normal ejecución