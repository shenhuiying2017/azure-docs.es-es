| Recurso | Límite predeterminado |
| --- | --- |
| Número máximo de cuentas de almacenamiento por suscripción | 200<sup>1</sup> |
| Capacidad máxima de la cuenta de almacenamiento | 500 TiB<sup>2</sup> |
| Número máximo de contenedores de blobs, blobs, recursos compartidos de archivos, tablas, colas, entidades o mensajes por cuenta de almacenamiento | Sin límite |
| Tasa de solicitud total por cuenta de almacenamiento | 20 000 solicitudes por segundo<sup>2</sup> |
| Entrada máxima<sup>3</sup> por cuenta de almacenamiento (regiones de EE. UU.) | 10 Gbps si GRS/ZRS<sup>4</sup> están habilitados, 20 Gbps en el caso de LRS<sup>2</sup> |
| Salida máxima<sup>3</sup> por cuenta de almacenamiento (regiones de EE. UU.) | 20 Gbps si RA-GRS/GRS/ZRS<sup>4</sup> están habilitados, 30 Gbps en el caso de LRS<sup>2</sup> |
| Entrada máxima<sup>3</sup> por cuenta de almacenamiento (regiones no de EE. UU.) | 5 Gbps si GRS/ZRS<sup>4</sup> están habilitados, 10 Gbps en el caso de LRS<sup>2</sup> |
| Salida máxima<sup>3</sup> por cuenta de almacenamiento (regiones no de EE. UU.) | 10 Gbps si RA-GRS/GRS/ZRS<sup>4</sup> están habilitados, 15 Gbps en el caso de LRS<sup>2</sup> |

<sup>1</sup>Incluye las cuentas de almacenamiento Estándar y Premium. Si necesita más de 200 cuentas de almacenamiento, realice una solicitud a través del [servicio de soporte técnico de Azure](https://azure.microsoft.com/support/faq/). El equipo de Azure Storage revisará su caso empresarial y podría aprobar hasta 250 cuentas de almacenamiento. 

<sup>2</sup> Para conseguir que las cuentas de almacenamiento estándar crezcan por encima de los límites anunciados de capacidad, entrada/salida e índice de solicitudes, realice una solicitud al [soporte técnico de Azure](https://azure.microsoft.com/support/faq/). El equipo de Azure Storage revisa la solicitud y puede aprobar límites según el caso.

<sup>3</sup> limitado únicamente por los límites de entrada/salida de la cuenta. *Entrada* hace referencia a todos los datos (solicitudes) que se envían a una cuenta de almacenamiento. *Salida* hace referencia a todos los datos (respuestas) recibidos desde una cuenta de almacenamiento.  

<sup>4</sup>Las opciones de redundancia de Azure Storage incluyen:
* **RA-GRS**: almacenamiento con redundancia geográfica con acceso de lectura. Si RA-GRS está habilitada, los destinos de salida para la ubicación secundaria son idénticos a los de la ubicación principal.
* **GRS**: almacenamiento con redundancia geográfica. 
* **ZRS**: almacenamiento con redundancia de zona. Disponible solo para blobs en bloques. 
* **LRS**: almacenamiento con redundancia local. 