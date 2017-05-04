# Fstab_CronTab

FSTAB:

A una maquina virtual añadirle 2 discos duros:

Creamos el disco A:

1er. paso - Particionar
Para saber si se intaló en el IDE 1 maestro, utilizamos:

~~~
#> fdisk -l
~~~

Mostrará una lista de los dispositivos conectados a los IDE's.
Entonces procedes a particionar la unidad recien instalada:
 
~~~
#> fdisk /dev/sdc
~~~

Una vez en fdisk, presionamos 'm' para una lista de los comandos de fdisk, en este caso usaremos:

~~~
Command (m for help): n    (añade nueva partición)
Command action
   e   extended
   p   primary partition (1-4)
p                          (tiene que ser partición primaria)
Partition number (1-4): 1
First cylinder (1-5580, default 1):
Using default value 1
Last cylinder or +size or +sizeM or +sizeK (1-558, default 5580): +10000M
~~~

Primero indicamos una nueva partición (n), después que es del tipo primaria (p),
el número de partición y por último el tamaño para esta partición.
(si se quisiera todo el disco en una sola partición, bastaría con presionar enter)
En este caso se dividirá el disco en más de una partición asi que 
repetiriamos lo anterior pero empezando en el siguiente cilindro disponible y 
sería la partición 2. Hay que indicar el tipo de sistema de archivos que tendrá la partición,
esto con el comando t:

~~~
t
Partition number (1-4) : 1
Hex code (L to list codes): 83 
~~~

Por último, guardamos los cambios con:

~~~
w
~~~

2do. paso - Crear filesystem
Para crear el filesystem o sistema de archivos se usa el comando mkfs
creamos el sistema de archivos en la unidad:
(/dev/sdc1 es la partición primaria 1 de ese disco)

~~~
#> mkfs -t ext3 /dev/sdc1
~~~

3er. paso - Punto de montaje
Solo hay que crear un directorio que es donde posteriormente montaremos el nuevo dispositivo. 
lo crearemos en la raíz para identificarlo mas fácil.

~~~
#> mkdir /Linux
~~~

4to. paso - Modificar el archivo fstab
Este archivo contiene una configuración en forma de 6 columnas que 
indican al sistema como deben montarse las unidades o dispositivos del equipo. 
Hay que agregar la siguiente línea al final de las que ya están:

~~~
#> vi /etc/fstab
...
/dev/sdc1   /Linux     ext3      defaults    2    1
...
:wq
~~~

5to. paso - Montarlo
No puedes usar un dispositivo en Linux como un disco duro si este no esta montado.

~~~
#> mount -t ext3 /dev/sdc1  /Linux
~~~

Lo que estás haciendo es decirle al sistema: 
"monta el dispositivo /dev/sdc1 con el tipo de sistema de archivos ext3 en el punto de 
montaje (directorio) /Linux" 
Aunque ya que este dispositivo esta indicado en /etc/fstab bastaría para montarlo
hacer lo siguiente:

~~~
#> mount /Linux

(o simplemente ejecutamos mount con la opción -a, para montar todo lo que encuentre en /etc/fstab)

#> mount -a
~~~

Listo es todo. El disco duro nuevo esta instalado y disponible para usuarse.
lo siguiente seria crear la otra particion (Fat)

Para el disco B realizariamos lo mismo pero creando las particiones Linux, NTFS y Fat

CRONTAB:

Cada hora en punto ejecutamos la sincronización horaria y mandamos la salida a /dev/null/

~~~
* * * * * root ntpdate -u hora.roa.es > /dev/null/
~~~

El trabajo que se debe ejecutar es:
Añadir al fichero /etc/trabajos (no existe hay que crearlo) el código del trabajo y la hora de ejecución.

Programar un trabajo (A) para ejecutarse en el minuto 30 de cada hora de cada día.

~~~
30 * * * * root (A) >  /etc/trabajos
~~~

Programar un trabajo (B) para ejecutarse cada día a las 20:30h.

~~~
30 20 * * * root (B) >  /etc/trabajos
~~~

Programar un trabajo (C) para ejecutarse de lunes a viernes a las 20:30h.

~~~
30 20 * * 1,2,3,4,5 root (C) >  /etc/trabajos
~~~

Programar un trabajo (D) para ejecutarse los martes y los jueves a las 20:30h.

~~~
30 20 * * 2,4 root (D) >  /etc/trabajos
~~~

Programar un trabajo (E) para ejecutarse los días 10 y 20 de todos los meses a las 20:30h.

~~~
30 20 10,20 * * root (E) >  /etc/trabajos
~~~

Programar un trabajo (F) para ejecutarse cada 15 minutos.

~~~
15 * * * * root (F) >  /etc/trabajos
~~~

Programar un trabajo (G) para ejecutarse cada día a las 00:00h.

~~~
00 0 * * * root (G) >  /etc/trabajos
~~~

Programar un trabajo (H) para ejecutarse cada primer día de mes a las 00:00h.

~~~
00 0 1 * * root (H) >  /etc/trabajos
~~~
