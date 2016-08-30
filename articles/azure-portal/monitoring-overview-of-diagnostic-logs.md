<properties
	pageTitle="Información general sobre los registros de diagnóstico de Azure | Microsoft Azure"
	description="Aprenda qué son los registros de diagnóstico de Azure y cómo puede usarlos para entender los eventos que se producen en un recurso de Azure."
	authors="johnkemnetz"
	manager="rboucher"
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="johnkem"/>

# Información general sobre los registros de diagnóstico de Azure
Los **registros de diagnóstico de Azure** son registros emitidos por un recurso que proporcionan datos exhaustivos y frecuentes acerca del funcionamiento de ese recurso. El contenido de estos registros varía según el tipo de recurso (por ejemplo, los registros del sistema de eventos de Windows son una categoría de registro de diagnóstico para máquinas virtuales, mientras que los registros de blob, tabla y cola son categorías para cuentas de almacenamiento). Se diferencian del [registro de actividad (antes conocido como registro de auditoría o registro operativo)](monitoring-overview-activity-logs.md) en que proporcionan información sobre las operaciones realizadas en recursos de su suscripción. No todos los recursos admiten el nuevo tipo de registros de diagnóstico que se describe aquí. La lista de servicios admitidos de más adelante muestra qué tipos de recursos admiten los nuevos registros de diagnóstico.

![Ubicación lógica de registros de diagnóstico](./media/monitoring-overview-of-diagnostic-logs/logical-placement-chart.png)

## Qué se puede hacer con los registros de diagnóstico
Estas son algunas de las cosas que puede hacer con los registros de diagnóstico:

- Guardarlos en una **cuenta de almacenamiento** para archivarlos o inspeccionarlos manualmente. Puede especificar el tiempo de retención (en días) mediante **Configuración de diagnóstico**.
- [Transmitirlos a **centros de eventos**](monitoring-stream-diagnostic-logs-to-event-hubs.md) para la ingestión en un servicio de terceros o una solución de análisis personalizado como PowerBI.

## Configuración de diagnóstico
Los registros de diagnóstico para recursos no de proceso se configuran mediante Configuración de diagnóstico. **Configuración de diagnóstico** para un control de recurso:

- Dónde se envían los registros de diagnóstico (cuenta de almacenamiento, centros de eventos u OMS).
- Qué categorías de registro se envían.
- Cuánto tiempo debe retenerse cada categoría de registro en una cuenta de almacenamiento: con una retención de cero días los registros se mantienen indefinidamente. Si se establecen directivas de retención pero el almacenamiento de registros en una cuenta de almacenamiento está deshabilitado (por ejemplo, si solo se han seleccionado las opciones de centros de eventos u OMS), las directivas de retención no surten ningún efecto.

Estas configuraciones se establecen con facilidad mediante la hoja Diagnósticos para un recurso en el Portal de Azure, mediante los comandos de Azure PowerShell y de la CLI, o mediante la [API de REST Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [AZURE.WARNING] Los registros de diagnóstico y las métricas para recursos de proceso (por ejemplo, máquinas virtuales o Service Fabric) usan [un mecanismo diferente para la configuración y la selección de salidas](../azure-diagnostics.md).

## Cómo habilitar la recopilación de registros de diagnóstico
La recopilación de registros de diagnóstico se puede habilitar como parte de la creación de un recurso o después de crear un recurso mediante la hoja del recurso en el Portal. También puede habilitar los registros de diagnóstico en cualquier momento mediante comandos de Azure PowerShell o de la CLI, o con la API de REST Insights.

> [AZURE.TIP] Es posible que estas instrucciones no se apliquen directamente a cada recurso. Consulte los vínculos de esquema al final de esta página para ver los pasos especiales que se pueden aplicar a determinados tipos de recursos.

[Este artículo muestra cómo puede usar una plantilla de recursos para habilitar Configuración de diagnóstico al crear un recurso](./monitoring-enable-diagnostic-logs-using-template.md).

### Habilitación de los registros de diagnóstico en el portal
Para habilitar los registros de diagnóstico en el Portal de Azure al crear algunos tipos de recursos, haga lo siguiente:

1.	Vaya a **Nuevo** y elija el recurso que le interesa.
2.	Después de establecer la configuración básica y seleccionar un tamaño, en la hoja **Configuración**, en **Supervisión**, seleccione **Habilitado** y elija una cuenta de almacenamiento donde almacenar los registros de diagnóstico. Cuando envíe diagnósticos a una cuenta de almacenamiento, se le cobra según las tarifas de datos normales relativas a almacenamiento y transacciones. ![Habilitar los registros de diagnóstico durante la creación de recursos](./media/monitoring-overview-of-diagnostic-logs/enable-portal-new.png)
3.	Haga clic en **Aceptar** y cree el recurso.

Para habilitar los registros de diagnóstico en el Portal de Azure una vez creado un recurso, haga lo siguiente:

1.	Vaya a la hoja del recurso y abra la hoja **Diagnósticos**.
2.	Haga clic en **Activado** y seleccione una cuenta de almacenamiento o un centro de eventos. ![Habilitar los registros de diagnóstico después de la creación de recursos](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3.	En **Registros**, seleccione qué **categorías de registro** desea recopilar o transmitir.
4.	Haga clic en **Save**.

### Habilitación de los registros de diagnóstico mediante programación
Para habilitar los registros de diagnóstico con cmdlets de Azure PowerShell, use los siguientes comandos.

Para habilitar el almacenamiento de registros de diagnóstico en una cuenta de almacenamiento, use este comando:

    Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -StorageAccountId [your storage account id] -Enabled $true

El identificador de la cuenta de almacenamiento es el identificador de recurso para la cuenta de almacenamiento a la que desea enviar los registros.

Para habilitar el streaming de registros de diagnóstico a un centro de eventos, use este comando:

    Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -ServiceBusRuleId [your service bus rule id] -Enabled $true

El identificador de regla del Bus de servicio es una cadena con este formato: `{service bus resource ID}/authorizationrules/{key name}`.

Para habilitar los registros de diagnóstico con la CLI de Azure, use los siguientes comandos:

Para habilitar el almacenamiento de registros de diagnóstico en una cuenta de almacenamiento, use este comando:

    azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true

El identificador de la cuenta de almacenamiento es el identificador de recurso para la cuenta de almacenamiento a la que desea enviar los registros.

Para habilitar el streaming de registros de diagnóstico a un centro de eventos, use este comando:

    azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true

El identificador de regla del Bus de servicio es una cadena con este formato: `{service bus resource ID}/authorizationrules/{key name}`.

Para cambiar Configuración de diagnóstico con la API de REST Insights, consulte [este documento](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## Servicios admitidos y esquema para registros de diagnóstico
El esquema para los registros de diagnóstico varía según la categoría de registro y el recurso. A continuación, se muestran los servicios admitidos y su esquema.

| Servicio | Esquema y documentos |
|-------------------------------|-----------------------------------------------------------------------------------------------------------------|
| Equilibrador de carga de software | [Análisis del registros para el Equilibrador de carga de Azure (vista previa)](../load-balancer/load-balancer-monitor-log.md) |
| Grupos de seguridad de red | [Análisis del registro para grupos de seguridad de red (NSG)](../virtual-network/virtual-network-nsg-manage-log.md) |
| Puertas de enlace de aplicaciones | [Registro de diagnóstico para la Puerta de enlace de aplicaciones](../application-gateway/application-gateway-diagnostics.md) |
| Almacén de claves | [Registro del Almacén de claves de Azure](../key-vault/key-vault-logging.md) |
| Búsqueda de Azure | [Habilitación y uso de Análisis de tráfico de búsqueda](../search/search-traffic-analytics.md) |
| Almacén de Data Lake | [Acceso a los registros de diagnóstico de Azure Data Lake Store](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Análisis de Data Lake | [Acceso a los registros de diagnóstico de Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Aplicaciones lógicas | No hay ningún esquema disponible. |

## Pasos siguientes
- [Transmisión de registros de diagnóstico de Azure a **Centros de eventos**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [Cambio de Configuración de diagnóstico mediante la API de REST Insights](https://msdn.microsoft.com/library/azure/dn931931.aspx)

<!---HONumber=AcomDC_0817_2016-->