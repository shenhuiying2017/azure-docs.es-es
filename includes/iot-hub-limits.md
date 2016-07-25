En la tabla siguiente se enumeran los límites asociados a los diferentes niveles de servicio (S1, S2, S3, F1). Para obtener información sobre el costo de cada *unidad* en cada nivel, consulte los [Precios del Centro de IoT](https://azure.microsoft.com/pricing/details/iot-hub/).

| Recurso | S1 Estándar | S2 Estándar | S3 Estándar | F1 Gratis |
| -------- | ----------- | ----------- | ----------- | ------- |
| Mensajes por día | 400\.000 | 6\.000.000 | 300\.000.000 | 8\.000 |
| Unidades máximas | 200 | 200 | 200 | 1 |

> [AZURE.NOTE] Si prevé usar más de 200 unidades con un centro de nivel S1, S2 o S3, póngase en contacto con el soporte técnico de Microsoft.

En la tabla siguiente se enumeran los límites que se aplican a los recursos de Centro de IoT:

| Recurso | Límite |
| -------- | ----- |
| Cantidad máxima de Centros de IoT de pago por suscripción a Azure | 10 |
| Cantidad máxima de Centros de IoT gratis por suscripción a Azure | 1 |
| Número máximo de identidades del dispositivo<br/> devueltas en una sola llamada | 1000 |
| Retención máxima de mensajes del Centro de IoT | 7 días |
| Tamaño máximo de mensaje del dispositivo a la nube | 256 KB |
| Tamaño máximo de lote de dispositivo a la nube | 256 KB |
| Número máximo de mensajes en lote del dispositivo a la nube | 500 |
| Tamaño máximo de mensaje de nube a dispositivo | 64 KB |
| TTL máximo para los mensajes de nube a dispositivo | 2 días |
| Número máximo de entregas para los mensajes de nube a dispositivo <br/> | 100 |
| Número máximo de entregas de mensajes de comentarios <br/> en respuesta a un mensaje de nube a dispositivo | 100 |
| TTL máximo para los mensajes de comentarios en <br/> respuesta a un mensaje de nube a dispositivo | 2 días |

> [AZURE.NOTE] Si necesita más de 10 Centros de IoT de pago en una suscripción de Azure, póngase en contacto con el servicio de soporte técnico de Microsoft.

El servicio de Centro de IoT limita las solicitudes cuando se superan las cuotas siguientes:

| Limitación | Valor por centro |
| -------- | ------------- |
| Las operaciones de registro de identidades <br/> (crear, recuperar, enumerar, actualizar y eliminar), <br/> importación y exportación masiva o individual | 5000/min/unidad (para S3) <br/> 100/min/unidad (para S1 y S2). |
| Conexiones de dispositivos | 6000/s/unidad (para S3), 120/s/unidad (para S2), 12/s/unidad (para S1). <br/>Mínimo de 100/s. |
| Envíos de dispositivo a nube | 6000/s/unidad (para S3), 120/s/unidad (para S2), 12/s/unidad (para S1). <br/>Mínimo de 100/s. |
| Envíos de nube a dispositivo | 5000/min/unidad (para S3), 100/min/unidad (para S1 y S2). |
| Recepciones de nube a dispositivo | 50000/min/unidad (para S3), 1000/min/unidad (para S1 y S2). |
| Operaciones de carga de archivos | Notificaciones de carga de 5000 archivos/min/unidad (para S3), notificaciones de carga de 100 archivos/min/unidad (para S1 y S2). <br/> 10000 URI de SAS pueden estar fuera de una cuenta de almacenamiento al mismo tiempo.<br/> 10 URI/dispositivo de SAS puede estar fuera al mismo tiempo. |

<!---HONumber=AcomDC_0713_2016-->