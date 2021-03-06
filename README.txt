Laboratorio 8 - E/S

Modo de entrega: realizar un informe en formato PDF donde se respondan
las consignas de cada ejercicio. En aquellos donde deban modificar
archivos de xv6, subir las copias de los mismos en el repositorio del
laboratorio.

Buffering

Ejercicio 1

El programa write_bytes.c escribe la cantidad de bytes indicada en un
archivo. Por ejemplo, el siguiente comando escribe 100 Mb en el archivo
tmp.txt, usando un buffer de 4096 bytes:

    $ bin/write_bytes tmp.txt 104857600 4096

Responder

1.  Obtener un promedio de tiempo de ejecución del programa, usando el
    comando time, al crear un archivo de 100 Mb, con tamaños de buffer
    de 256, 1024, 4096 y 8192 bytes. Presentar una tabla que indique el
    tiempo total, de usuario, de sistema y uso de la CPU. Realizar 10
    ejecuciones para obtener el promedio.
2.  Explicar los resultados.

E/S en una CPU x86

Leer el Capítulo 8 del archivo i386.pdf, hasta la página 148 (ignorar la
sección 8.3), donde se describe la E/S en una CPU Intel 386, con
comandos específicos de E/S y mapeada en memoria.

Ejercicio 2: Comandos de E/S

1.  ¿Cuál es el tamaño del espacio de direcciones de E/S?
2.  ¿Que instrucciones son utilizadas para envíar datos desde los
    registros de la CPU a los puertos de E/S? ¿Y para E/S mapeada en
    memoria?

Terminal

Cuando ejecutan xv6 utilizando el comando make -qemu-nox, se simula la
conexión mediante una terminal RS-232. Esto es, cada caracter que
escriben y ven por pantalla, es enviado por medio de un conexión en el
puerto COM1 de la PC simulada por qemu.

En el archivo console.c se implementa gran parte de la funcionalidad de
E/S de la terminal. Los comandos envíados por el usuario se obtienen
desde desde el teclado y/o desde el puerto serial (RS232). La salida es
presentada por pantalla y también a travéz del puerto serial.

Ejercicio 3: Ciclo de vida del requerimiento de E/S

1.  Describir el ciclo de vida del requerimiento de E/S al recibir un
    caracter desde el puerto serial, hasta que es impreso en la pantalla
    de la terminal, explicando la secuencia de funciones invocadas,
    interrupciones, etc.
2.  ¿Se utilizan comandos específicos de E/S, o son mapeados en memoria?

Ejercicio 4: Independencia del dispositivo

1.  Describir como se logra la independencia de dispositivo, en el caso
    de la terminal. Pista: ver la función consoleinit() en console.c, y
    el archivo file.h.

Ejercicio 5: Salida de la terminal por la pantalla

Para simular la salida por un monitor, conectado a la tarjeta de vídeo
en la PC, hay que ejecutar xv6 utilizando el comando make -qemu (sin el
-nox).

La función cgaputc() escribe el carácter que recibe como parámetro en la
memoria de video en modo texto, suponiendo un adaptador de vídeo CGA. El
archivo cga.pdf es el manual de referencia de este adaptador.

Se utilizan los siguientes datos:

-   CRTPORT, un #define con el valor 0x3D4, que es la dirección en la
    memoria de E/S del puerto de comandos de la controladora de vídeo.
-   La variable *crt, que es un puntero a la memoria de video en modo
    texto, mapeada en la dirección 0xB8000. La memoria de vídeo es un
    arreglo de 4000 bytes que representa una grilla de 80 columnas y 25
    filas (ver página 12 en cga.pdf). Cada carácter requiere 2 bytes, el
    primero indica el color de la fuente y fondo, mientras que el
    segundo es el código ASCII.
-   La posición del cursor es almacenada por la controladora de vídeo en
    los registros Cursor Location High Register y Cursor Location Low
    Register (ver archivo cga.txt).

La función cgaputc() realiza las siguientes tareas:

1.  Obtiene la posición del cursor hardware, y lo almacena en la
    variable pos.
2.  Si el carácter es imprimible, lo escribe en la memoria de vídeo en
    la posición correspondiente.
3.  Si es un \n o un BACKSPACE, actualiza la variable pos para reflejar
    el cambio de la posición del cursor.
4.  Comprueba que la nueva posición del cursor (pos++) este dentro de
    los límites de la pantalla.
5.  Realiza el scroll (desplazamiento) de la pantalla si corresponde.
6.  Actualiza el cursor hardware con la nueva posición.

Responder:

1.  Indicar para cada una de las tareas de la función cgaput(), si es
    una operación de E/S mapeada en memoria o mediante registros
    especiales de E/S. Justificar.
2.  Modificar cgaput.c para cambiar el color con que el que se imprimen
    los caracteres por pantalla. Probar también cambiar el color de
    fondo. Ver la sección Alphanumeric Modes (pag. 10) en la
    documentación CGA (recordar que Intel usa little-endian). Copiar en
    el repositorio del laboratorio el archivo console.c modificado, con
    los comentarios correspondientes que indiquen brevemente las
    modificaciones realizadas.

E/S de disco

Para realizar estos ejercicios, van a tener que entender como funciona
el driver de disco de xv6, y parte del sistema de E/S para disco. Lean y
usen de referencia el documento xv6-ide.pdf, que describe la
implementación de la E/S de disco en xv6, y la copia del archivo ide.c
que tiene muchos comentarios adicionales.

Ejercicio 6: Ciclo de vida del requerimiento de E/S

Suponer que un programa escribe el caracter a en un archivo en disco:

    write(fd, 'a', 1);

1.  Describir el ciclo de vida del requerimiento de E/S, explicando la
    secuencia de funciones invocadas, interrupciones, etc.
2.  ¿Qué tipo de política de planificación de disco implementa xv6?
3.  ¿Utiliza DMA o la operación de E/S es dirigida por la CPU?
4.  En la función idewait() se realiza un polling para esperar que el
    disco este listo, ¿por qué se decidió usar una espera activa?

------------------------------------------------------------------------

¡Fin del Laboratorio 6!
