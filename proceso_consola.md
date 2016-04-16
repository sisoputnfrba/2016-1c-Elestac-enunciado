# Proceso Consola

El Proceso Consola es un proceso simple que permite enviar a ejecutar Programas AnSISOP al sistema distribuido, y funcionar como interfaz del mismo para recibir los resultados de su ejecución o los mensajes que el Programa AnSISOP necesite imprimir por pantalla.

## Arquitectura del Proceso Consola

La Consola es un simple intérprete de comandos que permitirá al usuario del sistema iniciar nuevos Programas AnSISOP y visualizar los mensajes que éste imprima.

Al iniciar, leerá su archivo de configuración, se conectará mediante sockets[^1] al Proceso Núcleo y, luego de un intercambio de mensajes inicial (handshake) , enviará el código del Programa AnSISOP al Núcleo. A partir de ese momento, el proceso quedará a la espera de mensajes del Núcleo correspondientes a las sentencias `imprimir` e `imprimirTexto`, con los valores que deberá mostrar en pantalla.

* Existirá una instancia de este proceso por cada Programa AnSISOP a ejecutar en el sistema
* El Proceso Consola deberá poder ser utilizado como intérprete de scripts AnSISOP mediante el encabezado hashbang (`#!`)[^2]
* La terminación del proceso Consola implica la finalización de la ejecución del Programa AnSISOP en el sistema y viceversa

[^1] Siempre que en el enunciado se lea la palabra socket, se refiere a los sockets `STREAM` tipo `AF_INET`

[^2] Ver http://mgarciaisaia.github.io/tutorial-c/blog/2014/03/20/she-bangs-she-bangs/