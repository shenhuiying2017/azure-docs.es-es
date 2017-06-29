En la tabla siguiente se enumeran las cuotas y los límites específicos de los Centros de eventos de Azure. Para más información sobre los precios de Event Hubs, consulte los [precios de Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

| Límite | Scope | Tipo | Comportamiento cuando se supera | Valor |
| --- | --- | --- | --- | --- |
| Número de Centros de eventos por espacio de nombres |Espacio de nombres |Estática |Se rechazarán las solicitudes posteriores para la creación de un nuevo espacio de nombres. |10 |
| Número de particiones del Centro de eventos |Entidad |Estática |- |32 |
| Número de grupos de consumidores por Centro de eventos |Entidad |Estática |- |20 | |
| Número de conexiones de AMQP por espacio de nombres |Espacio de nombres |estático |Las solicitudes posteriores de conexiones adicionales se rechazarán y el código que realizó la llamada recibe una excepción. |5.000 |
| Tamaño máximo de evento de Event Hubs|En todo el sistema |Estática |- |256 KB |
| Tamaño máximo del nombre de un centro de eventos |Entidad |Estática |- |50 caracteres |
| Número de destinatarios no de época por grupo de consumidores |Entidad |Estática |- |5 |
| Período de retención máximo de datos de eventos |Entidad |Estática |- |1-7 días |
| Unidades de rendimiento máximo |Espacio de nombres |estático |Si se supera el límite de unidades de rendimiento, los datos se limitan y se genera un excepción **[ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)**. Para solicitar un número mayor de unidades de rendimiento para el nivel Estándar rellene una [solicitud de soporte técnico](/azure/azure-supportability/how-to-create-azure-support-request). Las [unidades de rendimiento adicionales](../articles/event-hubs/event-hubs-auto-inflate.md) se encuentran disponibles en bloques de 20 y están sujetas a un compromiso de compra. |20 |

