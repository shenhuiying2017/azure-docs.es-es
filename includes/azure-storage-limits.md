| Recurso | Límite predeterminado |
| --- | --- |
| Número máximo de cuentas de almacenamiento por suscripción |200<sup>1</sup> |
| Capacidad máxima de la cuenta de almacenamiento |500 TB<sup>2</sup> |
| Número máximo de contenedores de blobs, blobs, recursos compartidos de archivos, tablas, colas, entidades o mensajes por cuenta de almacenamiento |Sin límite |
| Tamaño máximo de un contenedor de blobs, una tabla o una cola |La misma que la capacidad máxima de la cuenta de almacenamiento |
| Número máximo de bloques en un blob en bloques o blob de anexión |50.000 |
| Tamaño máximo un bloque en un blob en bloques |100 MB |
| Tamaño máximo de un blob en bloques |50 000 X 100 MB (4,75 TB aproximadamente) |
| Tamaño máximo de un bloque en un blob en anexos |4 MB |
| Tamaño máximo de un blob en anexos |50 000 x 4 MB (195 GB aproximadamente) |
| Tamaño máximo de un blob en páginas |8 TB |
| Tamaño máximo de una entidad de tabla |1 MB |
| Número máximo de propiedades de una entidad de tabla |252 |
| Tamaño máximo de un mensaje de una cola |64 KB |
| Tamaño máximo de un recurso compartido de archivos |5 TB |
| Tamaño máximo de un recurso compartido de archivos |1 TB |
| Número máximo de archivos en un recurso compartido de archivos |El único límite es la capacidad total de 5 TB del recurso compartido de archivos |
| Máximo de IOPS por recurso compartido |1000 |
| Número máximo de archivos en un recurso compartido de archivos |El único límite es la capacidad total de 5 TB del recurso compartido de archivos |
| Número máximo de directivas de acceso almacenadas por contenedor, recurso compartido de archivos, tabla o cola |5 |
| Tasa de solicitud total por cuenta de almacenamiento |Blobs: 20 000 solicitudes por segundo<sup>2</sup> para blobs de cualquier tamaño válido (restringido únicamente por los límites de entrada/salida de la cuenta) <br />Archivos: 1000 IOPS (tamaño de 8 KB) por recurso compartido de archivos <br />Colas: 20 000 mensajes por segundo (suponiendo un tamaño de mensaje de 1 KB)<br />Tablas: 20 000 transacciones por segundo (suponiendo un tamaño de 1 KB) |
| Rendimiento de un blob |Hasta 60 MB por segundo o hasta 500 solicitudes por segundo |
| Rendimiento de una cola (mensajes de 1 KB) |Hasta 2000 mensajes por segundo |
| Rendimiento de destino de una sola partición de tabla (entidades de 1 KB) |Hasta 2000 entidades por segundo |
| Rendimiento de un recurso compartido de archivos |Hasta 60 MB por segundo |
| Entrada máxima<sup>3</sup> por cuenta de almacenamiento (regiones de EE. UU.) |10 Gbps si GRS/ZRS<sup>4</sup> están habilitados, 20 Gbps en el caso de LRS<sup>2</sup> |
| Salida máxima<sup>3</sup> por cuenta de almacenamiento (regiones de EE. UU.) |20 Gbps si RA-GRS/GRS/ZRS<sup>4</sup> están habilitados, 30 Gbps en el caso de LRS<sup>2</sup> |
| Entrada máxima<sup>3</sup> por cuenta de almacenamiento (regiones no de EE. UU.) |5 Gbps si GRS/ZRS<sup>4</sup> están habilitados, 10 Gbps en el caso de LRS<sup>2</sup> |
| Salida máxima<sup>3</sup> por cuenta de almacenamiento (regiones no de EE. UU.) |10 Gbps si RA-GRS/GRS/ZRS<sup>4</sup> están habilitados, 15 Gbps en el caso de LRS<sup>2</sup> |

<sup>1</sup>Esto incluye las cuentas de almacenamiento Estándar y Premium. Si necesita más de 200 cuentas de almacenamiento, realice una solicitud a través del [servicio de soporte técnico de Azure](https://azure.microsoft.com/support/faq/). El equipo de Azure Storage revisará su caso empresarial y podría aprobar hasta 250 cuentas de almacenamiento. 

<sup>2</sup> Para conseguir que las cuentas de almacenamiento estándar crezcan por encima de los límites anunciados de capacidad, entrada/salida e índice de solicitudes, realice una solicitud al [soporte técnico de Azure](https://azure.microsoft.com/support/faq/). El equipo de Azure Storage revisa la solicitud y puede aprobar límites según el caso.

<sup>3</sup>El término *entrada* hace referencia todos los datos (solicitudes) que se envían a una cuenta de almacenamiento. *Salida* hace referencia a todos los datos (respuestas) recibidos desde una cuenta de almacenamiento.  

<sup>4</sup>Entre las opciones de replicación de Azure Storage se incluyen:
* **RA-GRS**: almacenamiento con redundancia geográfica con acceso de lectura. Si RA-GRS está habilitada, los destinos de salida para la ubicación secundaria son idénticos a los de la ubicación principal.
* **GRS**: almacenamiento con redundancia geográfica. 
* **ZRS**: almacenamiento con redundancia de zona. Disponible solo para blobs en bloques. 
* **LRS**: almacenamiento con redundancia local. 


