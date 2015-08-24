Recurso|Límite predeterminado
---|---
Número máximo de cuentas de almacenamiento por suscripción|100<sup>1</sup>
TB por cuenta de almacenamiento|500 TB
Tamaño máximo de un contenedor de blobs, una tabla o una cola|500 TB
Número máximo de bloques en un blob en bloques|50\.000
Tamaño máximo un bloque en un blob en bloques|4 MB
Tamaño máximo de un blob en bloques|50 000 x 4 MB (195 GB aproximadamente) 
Tamaño máximo de un blob en páginas |1 TB
Tamaño máximo de una entidad de tabla|1 MB
Número máximo de propiedades de una entidad de tabla|252
Tamaño máximo de un mensaje de una cola|64 KB
Tamaño máximo de un recurso compartido de archivos|5 TB
Tamaño máximo de un recurso compartido de archivos|1 TB
Número máximo de archivos en un recurso compartido de archivos|El único límite es la capacidad total de 5 TB del recurso compartido de archivos
Número máximo de contenedores de blobs, blobs, recursos compartidos de archivos, tablas, colas, entidades o mensajes por cuenta de almacenamiento|El único límite es los 500 TB de capacidad de la cuenta de almacenamiento
Máximo de 8 kB de IOPS por disco persistente (máquina virtual de nivel Básico)|300<sup>2</sup>
Máximo de 8 kB de IOPS por disco persistente (máquina virtual de nivel Estándar)|500<sup>2</sup>
Velocidad de solicitudes (suponiendo un tamaño de objeto de 1 KB) por cuenta de almacenamiento|Hasta 20.000 IOPS, entidades por segundo o mensajes por segundo
Rendimiento de un blob|Hasta 60 MB por segundo o hasta 500 solicitudes por segundo
Rendimiento de una cola (mensajes de 1 KB)|Hasta 2000 mensajes por segundo
Rendimiento de una partición de tabla (entidades de 1 KB)|Hasta 2000 entidades por segundo
Rendimiento de destino para un solo recurso compartido de archivos (vista previa)|Hasta 60 MB por segundo
Entrada máxima<sup>3</sup> por cuenta de almacenamiento (regiones de EE. UU.)|10 Gbps si GRS<sup>4</sup> está habilitado, 20 Gbps para LRS
Salida máxima<sup>3</sup> por cuenta de almacenamiento (regiones de EE. UU.)|20 Gbps si GRS<sup>4</sup> está habilitado, 30 Gbps para LRS
Entrada máxima<sup>3</sup> por cuenta de almacenamiento (regiones de Europa y Asia)|5 Gbps si GRS<sup>4</sup> está habilitado, 10 Gbps para LRS
Salida máxima<sup>3</sup> por cuenta de almacenamiento (regiones de Europa y Asia)|10 Gbps si GRS<sup>4</sup> está habilitado, 15 Gbps para LRS

<sup>1</sup>Si necesitas más de 100 cuentas de almacenamiento, ponte en contacto con el [soporte técnico de Azure](http://azure.microsoft.com/support/faq/) para obtener ayuda.

<sup>2</sup>El límite total de velocidad de solicitudes para una cuenta de almacenamiento es 20 000 IOPS. Si una máquina virtual usa el número máximo de IOPS por disco, para evitar una posible limitación, asegúrese de que el número total de IOPS en todos los discos duros virtuales de las máquinas virtuales no supere el límite de la cuenta de almacenamiento (20.000 IOPS).

Puede calcular aproximadamente el número de discos muy usados admitidos por una única cuenta de almacenamiento en función del límite de transacciones. Por ejemplo, para una máquina virtual de nivel Básico, el número máximo de discos muy usados es alrededor de 66 (20.000/300 IOPS por disco) y, para una máquina virtual de nivel Estándar, aproximadamente 40 (20.000/500 IOPS por disco). Sin embargo, la cuenta de almacenamiento admite un mayor número de discos si no todos están siendo muy usados al mismo tiempo.

<sup>3</sup>*Entrada* hace referencia a todos los datos (solicitudes) que se envían a una cuenta de almacenamiento. *Salida* hace referencia a todos los datos (respuestas) recibidos desde una cuenta de almacenamiento.

<sup>4</sup>GRS hace referencia al almacenamiento con redundancia geográfica. ZRS hace referencia al almacenamiento con redundancia de zona y solo está disponible para los blobs en bloques. LRS hace referencia al almacenamiento con redundancia local.

<!---HONumber=August15_HO7-->