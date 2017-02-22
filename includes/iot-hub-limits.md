En la tabla siguiente se enumeran los límites asociados a los diferentes niveles de servicio (S1, S2, S3, F1). Para obtener información sobre el costo de cada *unidad* en cada nivel, consulte los [Precios del Centro de IoT](https://azure.microsoft.com/pricing/details/iot-hub/).

| Recurso | S1 Estándar | S2 Estándar | S3 Estándar | F1 Gratis |
| --- | --- | --- | --- | --- |
| Mensajes por día |400.000 |6.000.000 |300.000.000 |8.000 |
| Unidades máximas |200 |200 |200 |1 |

> [!NOTE]
> Si prevé usar más de 200 unidades con un centro de nivel S1, S2 o S3, póngase en contacto con el soporte técnico de Microsoft.
> 
> 

En la tabla siguiente se enumeran los límites que se aplican a los recursos de Centro de IoT:

| Recurso | Límite |
| --- | --- |
| Cantidad máxima de Centros de IoT de pago por suscripción a Azure |10 |
| Cantidad máxima de Centros de IoT gratis por suscripción a Azure |1 |
| Número máximo de identidades del dispositivo<br/>  devueltas en una sola llamada |1000 |
| Retención máxima de mensajes del Centro de IoT para los mensajes de dispositivo a la nube |7 días |
| Tamaño máximo de mensaje del dispositivo a la nube |256 KB |
| Tamaño máximo de lote de dispositivo a la nube |256 KB |
| Número máximo de mensajes en lote del dispositivo a la nube |500 |
| Tamaño máximo de mensaje de nube a dispositivo |64 KB |
| TTL máximo para los mensajes de nube a dispositivo |2 días |
| Número máximo de entregas para los mensajes de nube a dispositivo  <br/> messages |100 |
| Número máximo de entregas de mensajes de comentarios  <br/> en respuesta a un mensaje de nube a dispositivo |100 |
| TTL máximo para los mensajes de comentarios en  <br/> respuesta a un mensaje de nube a dispositivo |2 días |
| Tamaño máximo del dispositivo gemelo <br/> (etiquetas, propiedades notificadas y propiedades deseadas) | 8 KB |
| Tamaño máximo del valor de cadena del dispositivo gemelo | 512 bytes |
| Profundidad máxima del objeto en el dispositivo gemelo | 5 |
| Tamaño máximo de carga del método directo | 8 KB |
| Retención máxima del historial de trabajos | 30 días |
| Número máximo de trabajos simultáneos | 10 (para S3), 5 para (S2), 1 (para S1) |
| Número máximo de puntos de conexión adicionales | 10 (para S1, S2, S3) |
| Número máximo de reglas de enrutamiento de mensajes | 100 (para S1, S2, S3) |


> [!NOTE]
> Si necesita más de 10 Centros de IoT de pago en una suscripción de Azure, póngase en contacto con el servicio de soporte técnico de Microsoft.
> 
> 

El servicio de Centro de IoT limita las solicitudes cuando se superan las cuotas siguientes:

| Limitación | Valor por centro |
| --- | --- |
| Operaciones de registro de identidad <br/> (crear, recuperar, enumerar, actualizar, eliminar) <br/> importación y exportación masiva o individual |5000/min/unidad (para S3)  <br/> &100;/min/unidad (para S1 y S2). |
| Conexiones de dispositivos |6000/s/unidad (para S3), 120/s/unidad (para S2), 12/s/unidad (para S1). <br/>Mínimo de 100/s. |
| Envíos de dispositivo a nube |6000/s/unidad (para S3), 120/s/unidad (para S2), 12/s/unidad (para S1). <br/>Mínimo de 100/s. |
| Envíos de nube a dispositivo |5000/min/unidad (para S3), 100/min/unidad (para S1 y S2). |
| Recepciones de nube a dispositivo |50000/min/unidad (para S3), 1000/min/unidad (para S1 y S2). |
| Operaciones de carga de archivos |Notificaciones de carga de 5000 archivos/min/unidad (para S3), notificaciones de carga de 100 archivos/min/unidad (para S1 y S2). <br/> Puede haber&10000; URI de SAS fuera para una cuenta de Azure Storage al mismo tiempo.<br/> &10; URI/dispositivo de SAS puede estar fuera al mismo tiempo. |
| Métodos directos | 1500/s/unidad (para S3), 30/s/unidad (para S2), 10/s/unidad (para S1). |
| Lecturas de dispositivos gemelos | 50/s/unidad (para S3), 10/s o 1/s/unidad como máximo (para S2), 10/s (para S1) |
| Actualizaciones de dispositivos gemelos | 50/s/unidad (para S3), 10/s o 1/s/unidad como máximo (para S2), 10/s (para S1) |
| Operaciones de trabajos <br/> (crear, actualizar, enumerar, eliminar) | 5000/min./unidad (para¡ S3), 100/min./unidad (para S2), 100/min./unidad (para S1) |
| Resultado de operaciones por dispositivo de trabajos | 50/s/unidad (para S3), 10/s o 1/s/unidad como máximo (para S2), 10/s (para S1) |

<!--HONumber=Feb17_HO1-->


