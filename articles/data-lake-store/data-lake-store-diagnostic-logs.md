---
title: "Ver registros de diagnóstico de Azure Data Lake Store | Microsoft Docs"
description: "Sepa cómo configurar registros de diagnóstico y tener acceso a ellos para Azure Data Lake Store  "
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 5e1c3df24b0fc3e733981ab3f8814a9e6641f5f1
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-store"></a>Acceso a los registros de diagnóstico de Azure Data Lake Store
Sepa cómo habilitar el registro de diagnósticos en su cuenta de Data Lake Store y cómo ver los registros recopilados relativos a su cuenta.

Las organizaciones pueden habilitar el registro de diagnósticos en sus cuentas de Azure Data Lake Store para recopilar trazas de auditoría de acceso a datos que proporcionan información como, por ejemplo, la lista de usuarios que tienen acceso a los datos, con qué frecuencia se tiene acceso a ellos, qué cantidad de datos se almacena en la cuenta, etc. Cuando está habilitado, el diagnóstico o las solicitudes se registran del mejor modo posible. Tanto las entradas de registro de solicitudes como de diagnóstico se crean solo si se presentan solicitudes al punto de conexión de servicio.

## <a name="prerequisites"></a>Requisitos previos
* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
* **Cuenta del Almacén de Azure Data Lake**. Siga las instrucciones que se describen en [Introducción a Azure Data Lake Store mediante Azure Portal](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-store-account"></a>Habilitar el registro de diagnósticos en la cuenta de Data Lake Store
1. Inicie sesión en el nuevo [Azure Portal](https://portal.azure.com).
2. Abra la cuenta de Data Lake Store y, en la hoja de la cuenta de Data Lake Store, haga clic en **Configuración** y en **Registros de diagnóstico**.
3. En la hoja **Registros de diagnóstico**, haga clic en **Activar diagnósticos**.

    ![Habilitar el registro de diagnóstico](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Habilitar registros de diagnóstico")

3. En la hoja **Diagnóstico** , realice los siguientes cambios para configurar el registro de diagnósticos.
   
    ![Habilitar el registro de diagnóstico](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Habilitar registros de diagnóstico")
   
   * En el **Nombre**, escriba un valor para la configuración del registro de diagnóstico.
   * Puede optar por almacenar o procesar los datos de maneras diferentes.
     
        * Seleccione la opción **Archive to a storage account** (Archivar en cuenta de almacenamiento) para almacenar los registros en una cuenta de Azure Storage. Use esta opción si quiere archivar los datos que se procesarán por lotes más adelante. Si selecciona esta opción, debe proporcionar una cuenta de Azure Storage para guardar los registros.
        
        * Seleccione la opción **Stream to an event hub** (Transmitir a un centro de eventos) para transmitir los datos de registro a una instancia de Azure Event Hubs. Lo más probable es que use esta opción si tiene una canalización de procesamiento de bajada para analizar los registros entrantes en tiempo real. Si selecciona esta opción, debe proporcionar los detalles del Centro de eventos de Azure que quiera usar.

        * Seleccione la opción de **Send to Log Analytics** (Enviar a Log Analytics) para usar el servicio Azure Log Analytics para analizar los datos de registro generados. Si selecciona esta opción, debe proporcionar los detalles para el área de trabajo de Operations Management Suite que usaría para realizar análisis de registros. Consulte [Visualización o análisis de los datos recopilados con la búsqueda de registros de Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) para más información sobre el uso de Log Analytics.
     
   * Indique si quiere obtener los registros de auditoría, los registros de solicitudes o ambos.
   * Especifique el número de días durante los que deben conservarse los datos. La retención solo es aplicable si está utilizando la cuenta de Azure Storage para archivar datos de registro.
   * Haga clic en **Save**(Guardar).

Una vez habilitada la configuración de diagnóstico, puede ver los registros en la pestaña **Registros de diagnóstico** .

## <a name="view-diagnostic-logs-for-your-data-lake-store-account"></a>Ver registros de diagnóstico de la cuenta de Data Lake Store
Existen dos formas de ver los datos de registro para la cuenta de Data Lake Store.

* Desde la vista de configuración de la cuenta de Data Lake Store
* Desde la cuenta de Azure Storage donde se almacenan los datos

### <a name="using-the-data-lake-store-settings-view"></a>Uso de la vista de configuración de Data Lake Store
1. En la hoja **Configuración** de su cuenta de Data Lake Store, haga clic en **Registros de diagnóstico**.
   
    ![Ver registro de diagnóstico](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Ver registros de diagnóstico") 
2. En la hoja **Registros de diagnóstico**, debería ver los registros clasificados por **Registros de auditoría** y **Registros de solicitudes**.
   
   * Los registros de solicitudes capturan todas las solicitudes API realizadas en la cuenta de Data Lake Store.
   * Los registros de auditoría son parecidos a los de solicitud, pero proporcionan un desglose mucho más detallado de las operaciones que tienen lugar en la cuenta de Data Lake Store. Por ejemplo, una llamada de API de carga única en los registros de solicitud podría producir varias operaciones "Append" en los registros de auditoría.
3. Para descargar los registros, haga clic en el vínculo **Descargar** de cada entrada de registro.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>En la cuenta de Azure Storage que contiene los datos de registro
1. Abra la hoja de la cuenta de Azure Storage asociada con Data Lake Store para el registro y haga clic en Blobs. La hoja **Blob service** muestra dos contenedores.
   
    ![Ver registro de diagnóstico](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Ver registros de diagnóstico")
   
   * El contenedor **insights-logs-audit** contiene los registros de auditoría.
   * El contenedor **insights-logs-requests** contiene los registros de solicitudes.
2. Dentro de estos contenedores, los registros se almacenan con la siguiente estructura.
   
    ![Ver registro de diagnóstico](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Ver registros de diagnóstico")
   
    Por ejemplo, la ruta de acceso completa a un registro de auditoría sería `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    De forma similar, la ruta de acceso completa a un registro de solicitudes sería `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Comprender la estructura de los datos de registro
Los registros de auditoría y de solicitud tienen un formato JSON. En esta sección, veremos la estructura de JSON de los registros de auditoría y de solicitud.

### <a name="request-logs"></a>Request Logs
Este es un ejemplo de una entrada en el registro de solicitud con formato JSON. Cada blob tiene un objeto raíz llamado **registros** que contiene una matriz de objetos de registro.

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Esquema de un registro de solicitud
| NOMBRE | type | DESCRIPCIÓN |
| --- | --- | --- |
| Twitter en tiempo |string |Marca de tiempo (en UTC) del registro. |
| ResourceId |string |Identificador del recurso en el que tuvo lugar la operación. |
| categoría |string |Categoría del registro. Por ejemplo, **Requests**. |
| operationName |string |Nombre de la operación que se registra. Por ejemplo, getfilestatus. |
| resultType |string |Estado de la operación. Por ejemplo, 200. |
| callerIpAddress |string |Dirección IP del cliente que realiza la solicitud. |
| correlationId |string |Identificador del registro que se puede usar para agrupar un conjunto de entradas de registro relacionadas. |
| identidad |Objeto |Identidad que ha generado el registro. |
| propiedades |JSON |Vea más abajo para obtener más información. |

#### <a name="request-log-properties-schema"></a>Esquema de propiedades de un registro de solicitud
| NOMBRE | type | DESCRIPCIÓN |
| --- | --- | --- |
| HttpMethod |string |Método HTTP usado en la operación. Por ejemplo, GET. |
| Ruta de acceso |string |Ruta de acceso en la que se ha realizado la operación. |
| RequestContentLength |int |Longitud del contenido de la solicitud HTTP. |
| ClientRequestId |string |Identificador que distingue de manera única esta solicitud. |
| StartTime |string |Hora a la que el servidor ha recibido la solicitud. |
| EndTime |string |Hora a la que el servidor ha enviado una respuesta. |

### <a name="audit-logs"></a>Registros de auditoría
Este es un ejemplo de una entrada en el registro de auditoría con formato JSON. Cada blob tiene un objeto raíz llamado **registros** que contiene una matriz de objetos de registro

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Esquema de un registro de auditoría
| NOMBRE | type | DESCRIPCIÓN |
| --- | --- | --- |
| Twitter en tiempo |string |Marca de tiempo (en UTC) del registro. |
| ResourceId |string |Identificador del recurso en el que tuvo lugar la operación. |
| categoría |string |Categoría del registro. Por ejemplo, **Audit**. |
| operationName |string |Nombre de la operación que se registra. Por ejemplo, getfilestatus. |
| resultType |string |Estado de la operación. Por ejemplo, 200. |
| correlationId |string |Identificador del registro que se puede usar para agrupar un conjunto de entradas de registro relacionadas. |
| identidad |Objeto |Identidad que ha generado el registro. |
| propiedades |JSON |Vea más abajo para obtener más información. |

#### <a name="audit-log-properties-schema"></a>Esquema de propiedades de un registro de auditoría
| NOMBRE | type | DESCRIPCIÓN |
| --- | --- | --- |
| StreamName |string |Ruta de acceso en la que se ha realizado la operación. |

## <a name="samples-to-process-the-log-data"></a>Ejemplos para procesar los datos de registro
Al enviar registros de Azure Data Lake Store a Azure a Log Analytics (consulte [Visualización o análisis de los datos recopilados con la búsqueda de registros de Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) para más información sobre el uso de Log Analytics), la consulta siguiente devolverá una tabla que contiene una lista de nombres para mostrar de usuarios, la hora de los eventos y el recuento de eventos para la hora del evento junto con un gráfico visual. Se puede modificar fácilmente para que muestre el identificador único de usuario u otros atributos:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Store proporciona un ejemplo de cómo procesar y analizar los datos de registro. Puede encontrar el ejemplo en [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 

## <a name="see-also"></a>Otras referencias
* [Información general de Azure Data Lake Store](data-lake-store-overview.md)
* [Protección de los datos en Data Lake Store](data-lake-store-secure-data.md)

