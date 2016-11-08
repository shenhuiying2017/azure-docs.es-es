---
title: Ver registros de diagnóstico de Azure Data Lake Store | Microsoft Docs
description: 'Sepa cómo configurar registros de diagnóstico y tener acceso a ellos para Azure Data Lake Store '
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun

ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/19/2016
ms.author: nitinme

---
# Acceso a los registros de diagnóstico de Azure Data Lake Store
Sepa cómo habilitar el registro de diagnósticos en su cuenta de Data Lake Store y cómo ver los registros recopilados relativos a su cuenta.

Las organizaciones pueden habilitar el registro de diagnósticos en sus cuentas de Azure Data Lake Store para recopilar trazas de auditoría de acceso a datos que proporcionan información como, por ejemplo, la lista de usuarios que tienen acceso a los datos, con qué frecuencia se tiene acceso a ellos, qué cantidad de datos se almacena en la cuenta, etc.

## Requisitos previos
* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Habilite su suscripción de Azure** para la versión preliminar pública de Azure Data Lake Store. Consulte las [instrucciones](data-lake-store-get-started-portal.md#signup).
* **Cuenta del Almacén de Azure Data Lake**. Siga las instrucciones que se describen en [Introducción al Almacén de Azure Data Lake mediante el Portal de Azure](data-lake-store-get-started-portal.md).

## Habilitar el registro de diagnósticos en la cuenta de Data Lake Store
1. Inicie sesión en el nuevo [Portal de Azure](https://portal.azure.com).
2. Abra la cuenta de Data Lake Store y, en la hoja de la cuenta de Data Lake Store, haga clic en **Configuración** y en **Configuración de diagnóstico**.
3. En la hoja **Diagnóstico**, realice los siguientes cambios para configurar el registro de diagnósticos.
   
    ![Activación del registro de diagnósticos](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Habilitar registros de diagnóstico")
   
   * Establezca el **Estado** en **Activado** para habilitar el registro de diagnósticos.
   * Puede optar por almacenar o procesar los datos de dos maneras diferentes.
     * Seleccione la opción **Exportar a Centro de eventos** para transmitir los datos de registro a un Centro de eventos de Azure. Lo más probable es que use esta opción si tiene una canalización de procesamiento de bajada para analizar los registros entrantes en tiempo real. Si selecciona esta opción, debe proporcionar los detalles del Centro de eventos de Azure que quiera usar.
     * Seleccione la opción **Exportar a cuenta de almacenamiento** para almacenar los registros en una cuenta de almacenamiento de Azure. Use esta opción si quiere archivar los datos que se procesarán por lotes más adelante. Si selecciona esta opción, debe proporcionar una cuenta de almacenamiento de Azure para guardar los registros.
   * Indique si quiere obtener los registros de auditoría, los registros de solicitudes o ambos.
   * Especifique el número de días durante los que deben conservarse los datos.
   * Haga clic en **Guardar**.

Una vez habilitada la configuración de diagnóstico, puede ver los registros en la pestaña **Registros de diagnóstico**.

## Ver registros de diagnóstico de la cuenta de Data Lake Store
Existen dos formas de ver los datos de registro para la cuenta de Data Lake Store.

* Desde la vista de configuración de la cuenta de Data Lake Store
* Desde la cuenta de Almacenamiento de Azure donde se almacenan los datos

### Uso de la vista de configuración de Data Lake Store
1. En la hoja **Configuración** de su cuenta de Data Lake Store, haga clic en **Registros de diagnóstico**.
   
    ![Visualización del registro de diagnósticos](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Ver registros de diagnósticos")
2. En la hoja **Registros de diagnóstico**, debería ver los registros clasificados por **Registros de auditoría** y **Request Logs** (Registros de solicitudes).
   
   * Los registros de solicitudes capturan todas las solicitudes API realizadas en la cuenta de Data Lake Store.
   * Los registros de auditoría son parecidos a los de solicitud, pero proporcionan un desglose mucho más detallado de las operaciones que tienen lugar en la cuenta de Data Lake Store. Por ejemplo, una llamada de API de carga única en los registros de solicitud podría producir varias operaciones "Append" en los registros de auditoría.
3. Haga clic en el vínculo **Descargar** de cada entrada de registro para descargar los registros.

### En la cuenta de Almacenamiento de Azure que contiene los datos de registro
1. Abra la hoja de la cuenta de Almacenamiento de Azure asociada con Data Lake Store para el registro y haga clic en Blobs. La hoja **Servicio Blob** muestra dos contenedores.
   
    ![Visualización del registro de diagnósticos](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Ver registros de diagnósticos")
   
   * El contenedor **insights-logs-audit** contiene los registros de auditoría.
   * El contenedor **insights-logs-requests** contiene los registros de solicitudes.
2. Dentro de estos contenedores, los registros se almacenan con la siguiente estructura.
   
    ![Visualización del registro de diagnósticos](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Ver registros de diagnósticos")
   
    Por ejemplo, la ruta de acceso completa a un registro de auditoría sería `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    De forma similar, la ruta de acceso completa a un registro de solicitudes sería `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

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

#### Esquema de un registro de solicitud
| Nombre | Tipo | Description |
| --- | --- | --- |
| Twitter en tiempo |String |Marca de tiempo (en UTC) del registro. |
| resourceId |String |Identificador del recurso en el que tuvo lugar la operación. |
| categoría |String |Categoría del registro. Por ejemplo, **Requests**. |
| operationName |String |Nombre de la operación que se registra. Por ejemplo, getfilestatus. |
| resultType |String |Estado de la operación. Por ejemplo, 200. |
| callerIpAddress |String |Dirección IP del cliente que realiza la solicitud. |
| correlationId |String |Identificador del registro que se puede usar para agrupar un conjunto de entradas de registro relacionadas. |
| identidad |Objeto |Identidad que ha generado el registro. |
| propiedades |JSON |Vea más abajo para obtener más información. |

#### Esquema de propiedades de un registro de solicitud
| Nombre | Tipo | Description |
| --- | --- | --- |
| HttpMethod |String |Método HTTP usado en la operación. Por ejemplo, GET. |
| Ruta de acceso |String |Ruta de acceso en la que se ha realizado la operación. |
| RequestContentLength |int |Longitud del contenido de la solicitud HTTP. |
| ClientRequestId |String |Identificador que distingue de manera única esta solicitud. |
| StartTime |String |Hora a la que el servidor ha recibido la solicitud. |
| EndTime |String |Hora a la que el servidor ha enviado una respuesta. |

### Registros de auditoría
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

#### Esquema de un registro de auditoría
| Nombre | Tipo | Description |
| --- | --- | --- |
| Twitter en tiempo |String |Marca de tiempo (en UTC) del registro. |
| resourceId |String |Identificador del recurso en el que tuvo lugar la operación. |
| categoría |String |Categoría del registro. Por ejemplo, **Audit**. |
| operationName |String |Nombre de la operación que se registra. Por ejemplo, getfilestatus. |
| resultType |String |Estado de la operación. Por ejemplo, 200. |
| correlationId |String |Identificador del registro que se puede usar para agrupar un conjunto de entradas de registro relacionadas. |
| identidad |Objeto |Identidad que ha generado el registro. |
| propiedades |JSON |Vea más abajo para obtener más información. |

#### Esquema de propiedades de un registro de auditoría
| Nombre | Tipo | Description |
| --- | --- | --- |
| StreamName |String |Ruta de acceso en la que se ha realizado la operación. |

## Ejemplos para procesar los datos de registro
Azure Data Lake Store proporciona un ejemplo de cómo procesar y analizar los datos de registro. Puede encontrar el ejemplo en [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample).

## Otras referencias
* [Información general del Almacén de Azure Data Lake](data-lake-store-overview.md)
* [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)

<!---HONumber=AcomDC_0914_2016-->