
**HPFS**

HPFS sigla de High Performance File System, o sistema de archivos de altas prestaciones, 
fue creado específicamente para el sistema operativo OS/2 para mejorar las limitaciones del sistema de archivos FAT.
Fue escrito por Gordon Letwin y otros empleados de Microsoft, y agregado a OS/2 versión 1.2,
Otra de sus características es que, aunque poseía tabla de archivos (como FAT), ésta se encontraba posicionada físicamente en el centro de la partición, de tal manera que redundaba en menores tiempos de acceso a la hora de leerla/escribirla.
https://es.wikipedia.org/wiki/HPFS



Además, era necesario que un nuevo sistema de archivos extendiera el sistema de nomenclatura, la organización y la seguridad para las crecientes demandas del mercado de servidores de red. HPFS mantiene la organización de directorio de FAT, pero agrega la ordenación automática del directorio basada en nombres de archivo. Los nombres de archivo se extienden hasta 254 caracteres de doble byte. HPFS también permite crear un archivo de "datos" y atributos especiales para permitir una mayor flexibilidad en lo que se refiere a admitir otras convenciones de nomenclatura y seguridad. Además, la unidad de asignación cambia de clústeres a sectores físicos (512 bytes), lo que reduce el espacio en disco perdido.

Bajo HPFS, las entradas del directorio contienen más información que bajo FAT. Además del archivo de atributo, esto incluye información sobre la fecha y la hora de modificación, creación y acceso. En lugar de señalar al primer clúster del archivo, bajo HPFS las entradas del directorio señalan a FNODE. FNODE puede contener los datos del archivo, o punteros que pueden señalar a datos del archivo o a otras estructuras que señalarán a datos del archivo.

HPFS intenta asignar la mayor cantidad de datos de un archivo en sectores contiguos como sea posible. De esta forma aumenta la velocidad al hacer un procesamiento secuencial de un archivo.

HPFS organiza una unidad en una serie de bandas de 8 MB y siempre que sea posible un archivo está contenido dentro de una de estas bandas. Entre cada una de estas bandas hay 2K mapas de bits de asignación, que hacen un seguimiento de los sectores dentro de una banda que se han asignado y que no se han asignado. La creación de bandas aumenta el rendimiento porque el cabezal de la unidad de disco no tiene que volver a la parte superior lógica (normalmente el cilindro 0) del disco, sino al mapa de bits de asignación de banda más cercano, para determinar dónde se almacenará un archivo. Además, HPFS incluye un par de objetos de datos especiales únicos que son: el bloque súper y el bloque de reserva.


http://sistemasoperativoskike.blogspot.mx/2010/11/diferencias-entre-fat-fat32-hpfs-y-ntfs.html
