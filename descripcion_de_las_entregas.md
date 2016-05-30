# Descripción de las entregas

Para permitir una mejor distribución de las tareas y orientar al alumno en el proceso de desarrollo de su trabajo, se definieron una serie de puntos de control y fechas que el alumno podrá utilizar para comparar su grado de avance respecto del esperado.

## Checkpoint 1 - Obligatorio

**Fecha:** 30 de Abril

**Objetivos:**
- Familiarizarse con Linux y su consola, el entorno de desarrollo y el repositorio
- Aplicar las Commons Libraries, principalmente las funciones para listas, archivos de configuración y logs
- Implementar el Parser de AnSISOP en un programa de ejemplo
- Desarrollar un modelo de intérprete de comandos para la consola de la UMC
- Desarrollar un binario que sirva como intérprete de un script en Linux
- Realizar la estructura básica de comunicación entre los programas

**Requisitos mínimos:**
- Entorno instalado y funcionando. Todos los integrantes deben haber subido commits a Github.
- Tener todos los procesos del sistema creados, conectándose entre sí
- Poder conectar una consola al Núcleo y mediante un comando de prueba solicitarle que inicie un programa y que este mensaje sea enviado a la CPU, luego de la CPU al UMC y luego aquí al Administrador de Swap.  Será suficiente con que cada proceso imprima el mensaje recibido por pantalla.

**Distribución recomendada:**

**Consola**: 0.5 personas **Núcleo**: 1.5 persona. **UMC**: 1 persona. **Swap**: 1 persona. **CPU**: 1 persona.

**Lectura recomendada:**
- http://faq.utn.so/arrancar
- [Beej Guide to Network Programming](http://beej.us/guide/bgnet/output/html/multipage/index.html)
- [Linux POSIX Threads](http://www.yolinux.com/TUTORIALS/LinuxTutorialPosixThreads.html)
- [SisopUTNFRBA Commons Libraries](https://github.com/sisoputnfrba/so-commons-library)

## Checkpoint 2 - Obligatorio

**Fecha:** 21 de Mayo

**Objetivos:**
- Modificar el código de la UMC para que permita recibir múltiples conexiones por sockets y gestionarlas utilizando hilos independientes. Utilizar semáforos para sincronizar hilos que acceden las listas compartidas de la UMC
- Diseñar el diagrama de estados de un PCB en el sistema y crear la estructura del PCB junto con sus cambios en cada estado.
- Permitir que el proceso Swap pueda operar sobre un archivo binario y desarrollar las operaciones y estructuras requeridas para administrar el espacio. 
- Iniciar la implementación de las primitivas del CPU

**Requisitos mínimos:**
- El proceso Núcleo debe poder conocer una serie de PCBs y gestionarlos en sus distintos estados en función del algoritmo Round Robin. En todo momento debe indicar sus estados y transiciones.
- El proceso CPU debe, dado un código en AnSISOP, invocar las correspondientes primitivas y las primeras 4 deben intercambiar un mensaje de ejemplo con la UMC.
- El proceso UMC debe ser capaz de recibir pedidos de lecturas, y directamente reenviarlos al proceso Swap. También debe interpretar los pedidos para iniciar y finalizar Programas AnSISOP.
- El proceso Swap debe ser capaz de recibir nuevos programas AnSISOP. También debe poder eliminar estos procesos. Ante un pedido de lectura, debe poder buscar en su partición, para devolver el contenido de página adecuado.

**Distribución recomendada:**

**Consola**: 0.25 persona. **Núcleo**: 0.75 persona. **UMC**: 1 persona. **Swap**: 1.5 personas. **CPU**: 1.5 personas.

**Lectura recomendada:**
- Sistemas Operativos, Silberschatz, Galvin - Capítulo 4: Hilos
- Sistemas Operativos, Silberschatz, Galvin - Capítulo 5: Planificación del Procesador

## Checkpoint 3 - Obligatorio - En laboratorio

**Fecha:** 11 de Junio

**Objetivos:**
- Implementar la interfaz de mensajes de la UMC y su correspondiente validación.
- Implementar el algoritmo Clock en la UMC y LRU para su TLB.
- Las operaciones AnSISOP deben impactar en las estructuras de la UMC y del proceso Swap cuando corresponda.
- Desarrollar los hilos de entrada/salida
- El núcleo debe poder recibir nuevos Programas AnSISOP desde diversas consolas y enviar los PCB READY a ejecutar a los distintos CPUs disponibles.
- El proceso CPU deberá ejecutar programas simples impactando las modificaciones en las estructuras del PCB.

**Requisitos mínimos:**
- Varias instancias de un programa simple ejecutado debería poder ser ejecutado en el sistema
- Estructuras como el PCB o las estructuras administrativas de procesos como el UMC o el SWAP deben ser consistentes con la ejecución

**Distribución recomendada:**

**Núcleo**: 1 persona. **UMC**: 2 personas. **Swap**: 0.5 persona. **CPU**: 1.5 personas.

## Entrega Final

**Fecha:** 2 de Julio

**Objetivos:**
- Finalizar las primitivas del CPU y permitir la ejecución de programas AnSISOP en su especificación completa
- Implementar el algoritmo Clock Modificado en la UMC
- Dar soporte a las instrucciones de semáforo y a las variables de memoria compartida
- Realizar pruebas de stress sobre el sistema, ejecutando varios Programas AnSISOP de manera simultánea e interrumpiendo su ejecución 

**Distribución recomendada:**

**CPU**: 1.5 persona. **Núcleo**: 1 persona. **UMC**: 0.5 persona **Stress Test y debugging**: 2 personas.

**Lectura recomendada:**
- Test del trabajo práctico provistos por la cátedra
