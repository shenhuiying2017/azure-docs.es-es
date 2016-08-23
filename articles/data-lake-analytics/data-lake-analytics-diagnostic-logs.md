<properties 
   pageTitle="Visualización de registros de diagnóstico de Azure Data Lake Analytics | Microsoft Azure" 
   description="Sepa cómo configurar registros de diagnóstico y tener acceso a ellos para Azure Data Lake Analytics " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="Blackmist" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="08/10/2016"
   ms.author="larryfr"/>

# Acceso a los registros de diagnóstico de Azure Data Lake Analytics

Sepa cómo habilitar el registro de diagnósticos en su cuenta de Data Lake Analytics y cómo ver los registros recopilados relativos a su cuenta.

Las organizaciones pueden habilitar el registro de diagnósticos para su cuenta de Azure Data Lake Analytics para recopilar trazas de auditoría de acceso a datos. Estos registros proporcionan información como:

* Una lista de los usuarios que tienen acceso a los datos.
* Frecuencia con la que se accede a los datos.
* Cantidad de datos que se almacena en la cuenta.

## Requisitos previos

- **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Habilite su suscripción de Azure** para la versión preliminar pública de Data Lake Analytics. Consulte las [instrucciones](data-lake-analytics-get-started-portal.md#signup).
- **Cuenta de Azure Data Lake Analytics**. Siga las instrucciones que se describen en [Introducción a Azure Data Lake Analytics mediante el Portal de Azure](data-lake-analytics-get-started-portal.md).

## Habilitación del registro de diagnósticos para la cuenta de Data Lake Analytics

1. Inicie sesión en el nuevo [Portal de Azure](https://portal.azure.com).

2. Abra la cuenta de Data Lake Analytics y, en la hoja de la cuenta de Data Lake Analytics, haga clic en **Configuración** y en **Configuración de diagnóstico**.

3. En la hoja **Diagnóstico**, realice los siguientes cambios para configurar el registro de diagnósticos.

	![Activación del registro de diagnósticos](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Habilitar registros de diagnóstico")

	* Establezca el **Estado** en **Activado** para habilitar el registro de diagnósticos.
	* Puede optar por almacenar o procesar los datos de dos maneras diferentes.
		* Seleccione **Exportar a Centro de eventos** para transmitir los datos de registro a un Centro de eventos de Azure. Use esta opción si tiene una canalización de procesamiento de bajada para analizar los registros entrantes en tiempo real. Si selecciona esta opción, debe proporcionar los detalles del Centro de eventos de Azure que quiera usar.
		* Seleccione **Exportar a cuenta de almacenamiento** para almacenar los registros en una cuenta de Almacenamiento de Azure. Utilice esta opción si desea archivar los datos. Si selecciona esta opción, debe proporcionar una cuenta de Almacenamiento de Azure para guardar los registros.
	* Indique si quiere obtener los registros de auditoría, los registros de solicitudes o ambos.
	* Especifique el número de días durante los que deben conservarse los datos.
	* Haga clic en **Guardar**.

Una vez habilitada la configuración de diagnóstico, puede ver los registros en la pestaña **Registros de diagnóstico**.

## Visualización de registros de diagnóstico de la cuenta de Data Lake Analytics

Existen dos formas de ver los datos de registro para la cuenta de Data Lake Analytics.

* Desde la configuración de la cuenta de Data Lake Analytics
* Desde la cuenta de Almacenamiento de Azure donde se almacenan los datos

### Uso de la vista de configuración de Data Lake Analytics

1. En la hoja **Configuración** de su cuenta de Data Lake Analytics, haga clic en **Registros de diagnóstico**.

	![Visualización del registro de diagnósticos](./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs.png "Ver registros de diagnósticos")

2. En la hoja **Registros de diagnóstico**, debería ver los registros clasificados por **Registros de auditoría** y **Request Logs** (Registros de solicitudes).
	* Los registros de solicitudes capturan todas las solicitudes API realizadas en la cuenta de Data Lake Analytics.
	* Los registros de auditoría son parecidos a los de solicitud, pero proporcionan un desglose mucho más detallado de las operaciones que tienen lugar en la cuenta de Data Lake Analytics. Por ejemplo, una llamada de API de carga única en los registros de solicitud podría producir varias operaciones "Append" en los registros de auditoría.

3. Haga clic en el vínculo **Descargar** para que una entrada de registro descargue los registros.

### En la cuenta de Almacenamiento de Azure que contiene los datos de registro

1. Abra la hoja de la cuenta de Almacenamiento de Azure asociada con Data Lake Analytics para el registro y haga clic en Blobs. La hoja **Servicio Blob** muestra dos contenedores.

	![Visualización del registro de diagnósticos](./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs-storage-account.png "Ver registros de diagnósticos")

	* El contenedor **insights-logs-audit** contiene los registros de auditoría.
	* El contenedor **insights-logs-requests** contiene los registros de solicitudes.

2. Dentro de estos contenedores, los registros se almacenan con la siguiente estructura.

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json
    
    > [AZURE.NOTE] Las entradas `##` de la ruta de acceso contienen el año, mes, día y hora en que se creó el registro. Data Lake Analytics crea un archivo cada hora, por lo que `m=` siempre contiene un valor de `00`.

	Por ejemplo, la ruta de acceso completa a un registro de auditoría sería:
    
        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

	De forma similar, la ruta de acceso completa a un registro de solicitudes sería:
    
        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## Comprender la estructura de los datos de registro

Los registros de auditoría y de solicitud tienen un formato JSON. En esta sección, veremos la estructura de JSON de los registros de auditoría y de solicitud.

### Registros de solicitud

Este es un ejemplo de una entrada en el registro de solicitud con formato JSON. Cada blob tiene un objeto raíz llamado **registros** que contiene una matriz de objetos de registro.

	{
	"records": 
	  [		
		. . . .
		,
		{
			 "time": "2016-07-07T21:02:53.456Z",
			 "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
			 "category": "Requests",
			 "operationName": "GetAggregatedJobHistory",
			 "resultType": "200",
			 "callerIpAddress": "::ffff:1.1.1.1",
			 "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
			 "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
			 "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
		}
		,
		. . . .
	  ]
	}

#### Esquema de un registro de solicitud

| Nombre | Tipo | Description |
|-----------------|--------|--------------------------------------------------------------------------------|
| Twitter en tiempo | String | Marca de tiempo (en UTC) del registro. |
| resourceId | String | Identificador del recurso en el que tuvo lugar la operación. |
| categoría | String | Categoría del registro. Por ejemplo, **Requests**. |
| operationName | String | Nombre de la operación que se registra. Por ejemplo, GetAggregatedJobHistory. |
| resultType | String | Estado de la operación. Por ejemplo, 200. |
| callerIpAddress | String | Dirección IP del cliente que realiza la solicitud. |
| correlationId | String | El identificador del registro. Este valor puede utilizarse para agrupar un conjunto de entradas de registro relacionadas |
| identidad | Objeto | Identidad que ha generado el registro. |
| propiedades | JSON | Consulte la siguiente sección (Esquema de propiedades de un registro de solicitud) para más información |

#### Esquema de propiedades de un registro de solicitud

| Nombre | Tipo | Description |
|----------------------|--------|-----------------------------------------------------------|
| HttpMethod | String | Método HTTP usado en la operación. Por ejemplo, GET. |
| Ruta de acceso | String | Ruta de acceso en la que se ha realizado la operación. |
| RequestContentLength | int | Longitud del contenido de la solicitud HTTP. |
| ClientRequestId | String | Identificador que distingue de manera única esta solicitud. |
| StartTime | String | Hora a la que el servidor ha recibido la solicitud. |
| EndTime | String | Hora a la que el servidor ha enviado una respuesta. |

### Registros de auditoría

Este es un ejemplo de una entrada en el registro de auditoría con formato JSON. Cada blob tiene un objeto raíz llamado **registros** que contiene una matriz de objetos de registro

	{
	"records": 
	  [		
		. . . .
		,
		{
			 "time": "2016-07-28T19:15:16.245Z",
			 "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
			 "category": "Audit",
			 "operationName": "JobSubmitted",
			 "identity": "user@somewhere.com",
			 "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job", 
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
		}
		,
		. . . .
	  ]
	}

#### Esquema de un registro de auditoría

| Nombre | Tipo | Description |
|-----------------|--------|--------------------------------------------------------------------------------|
| Twitter en tiempo | String | Marca de tiempo (en UTC) del registro. |
| resourceId | String | Identificador del recurso en el que tuvo lugar la operación. |
| categoría | String | Categoría del registro. Por ejemplo, **Audit**. |
| operationName | String | Nombre de la operación que se registra. Por ejemplo, JobSubmitted. |
| resultType | String | Un subestado para el estado del trabajo (operationName). |
| resultSignature | String | Detalles adicionales sobre el estado del trabajo (operationName). |
| identidad | String | El usuario que solicitó la operación. Por ejemplo: susan@contoso.com. |
| propiedades | JSON | Consulte la siguiente sección (Esquema de propiedades de un registro de auditoría) para más información |

> [AZURE.NOTE] __resultType__ y __resultSignature__ proporcionan información sobre el resultado de una operación y solo contienen un valor si se ha completado una operación. Por ejemplo, contienen un valor cuando __operationName__ contiene un valor de __JobStarted__ o __JobEnded__.

#### Esquema de propiedades de un registro de auditoría

| Nombre | Tipo | Description |
|------------|--------|------------------------------------------|
| JobId | String | El identificador asignado al trabajo |
| JobName | String | El nombre que se proporcionó para el trabajo |
| JobRunTime | String | El tiempo de ejecución utilizado para procesar el trabajo |
| SubmitTime | String | La hora (en UTC) en la que se envió el trabajo |
| StartTime | String | La hora en la que el trabajo empezó a ejecutarse tras el envío (en UTC). |
| EndTime | String | La hora de finalización del trabajo. |
| Paralelismo | String | El número de unidades de Data Lake Analytics solicitadas para este trabajo durante el envío. |

> [AZURE.NOTE] __SubmitTime__, __StartTime__, __EndTime__ y __Paralelismo__ proporcionan información sobre una operación y solo contendrán algún valor si una operación se ha iniciado o completado. Por ejemplo, __SubmitTime__ contiene un valor después de que __operationName__ indique __JobSubmitted__.

## Ejemplos para procesar los datos de registro

Azure Data Lake Analytics proporciona un ejemplo de cómo procesar y analizar los datos de registro. Puede encontrar el ejemplo en [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample).


## Otras referencias

- [Información general de Azure Data Lake Analytics](data-lake-analytics-overview.md)

<!---HONumber=AcomDC_0810_2016-->