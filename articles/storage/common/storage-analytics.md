---
title: Uso del análisis de Azure Storage para recopilar datos de registros y métricas | Microsoft Docs
description: Storage Analytics permite realizar un seguimiento de los datos de métricas para todos los servicios de almacenamiento y recopilar registros de Blob Storage, Queue Storage y Table Storage.
services: storage
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 7894993b-ca42-4125-8f17-8f6dfe3dca76
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: rogarana
ms.openlocfilehash: edda01cbfe1b53d934f9f4a7bb01c645fa680873
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
ms.locfileid: "30243441"
---
# <a name="storage-analytics"></a>Storage Analytics

El análisis de Azure Storage realiza el registro y proporciona datos de métricas para una cuenta de almacenamiento. Puede usar estos datos para hacer un seguimiento de solicitudes, analizar tendencias de uso y diagnosticar problemas con la cuenta de almacenamiento.

Para utilizar Storage Analytics, debe habilitarlo para cada servicio que desee supervisar. Puede habilitarlo desde el [Azure Portal](https://portal.azure.com). Para obtener más detalles, consulte [Supervisión de una cuenta de almacenamiento en Azure Portal](storage-monitor-storage-account.md). También puede habilitar Storage Analytics mediante programación a través de la API de REST o la biblioteca de cliente. Use las operaciones [Get Blob Service Properties](https://msdn.microsoft.com/library/hh452239.aspx), [Get Queue Service Properties](https://msdn.microsoft.com/library/hh452243.aspx), [Get Table Service Properties](https://msdn.microsoft.com/library/hh452238.aspx) y [Get File Service Properties](https://msdn.microsoft.com/library/mt427369.aspx) para habilitar Storage Analytics para cada servicio.

Los datos agregados se almacenan en un blob conocido (para el registro) y en tablas conocidas (para las métricas), a los que se puede tener acceso mediante las API de Blob service y de Table service.

Storage Analytics tiene un límite de 20 TB en cuanto a la cantidad de datos almacenados, que es independiente del límite total de la cuenta de almacenamiento. Para obtener más información sobre las directivas de facturación y retención de datos, consulte [Facturación de Storage Analytics](https://msdn.microsoft.com/library/hh360997.aspx). Para obtener más información sobre los límites de las cuentas de almacenamiento, consulte [Objetivos de escalabilidad y rendimiento de Azure Storage](storage-scalability-targets.md).

Para obtener orientación exhaustiva sobre el uso de análisis de almacenamiento y otras herramientas para identificar, diagnosticar y solucionar problemas relacionados con Azure Storage, consulte [Supervisión, diagnóstico y solución de problemas de Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="about-storage-analytics-logging"></a>Acerca del registro de Storage Analytics
Storage Analytics registra información detallada sobre las solicitudes correctas y erróneas realizadas a un servicio de almacenamiento. Esta información se puede utilizar para supervisar solicitudes concretas y para diagnosticar problemas con un servicio de almacenamiento. Las solicitudes se registran en función de la mejor opción.

Solo se crean entradas del registro si hay actividad del servicio de almacenamiento. Por ejemplo, si una cuenta del almacenamiento tiene actividad en Blob service pero no en los servicios Table service o Queue service, solo se crearán los registros que pertenezcan a Blob service.

El registro de Storage Analytics no está disponible para Azure Files.

### <a name="logging-authenticated-requests"></a>Registrar solicitudes de autenticación
Se registran los siguientes tipos de solicitudes autenticadas:

* Solicitudes correctas
* Solicitudes erróneas, incluyendo errores de tiempo de espera, de limitación, de red, de autorización, etc
* Solicitudes que utilizan una firma de acceso compartido (SAS), incluyendo las solicitudes correctas y las erróneas
* Solicitudes de datos de análisis

Las solicitudes realizadas por el propio Storage Analytics, como la creación o eliminación del registro, no se registran. Puede encontrar una lista completa de los datos registrados en los temas [Operaciones y mensajes de estado registrados por Storage Analytics](https://msdn.microsoft.com/library/hh343260.aspx) y [Formato del registro de Storage Analytics](https://msdn.microsoft.com/library/hh343259.aspx).

### <a name="logging-anonymous-requests"></a>Registrar solicitudes anónimas
Se registran los siguientes tipos de solicitudes anónimas:

* Solicitudes correctas
* Errores del servidor
* Errores de tiempo de espera para el cliente y el servidor
* Solicitudes GET erróneas con el código de error 304 (Sin modificar)

El resto de solicitudes anónimas erróneas no se registran. Puede encontrar una lista completa de los datos registrados en los temas [Operaciones y mensajes de estado registrados por Storage Analytics](https://msdn.microsoft.com/library/hh343260.aspx) y [Formato del registro de Storage Analytics](https://msdn.microsoft.com/library/hh343259.aspx).

### <a name="how-logs-are-stored"></a>Cómo se almacenan los registros
Todos los registros se almacenan en blobs en bloques en un contenedor denominado $logs, que se crea automáticamente cuando se habilita Storage Analytics para una cuenta de almacenamiento. El contenedor $logs se encuentra en el espacio de nombres del blob de la cuenta de almacenamiento, por ejemplo: `http://<accountname>.blob.core.windows.net/$logs`. Este contenedor no se puede eliminar una vez habilitado Storage Analytics, aunque su contenido sí se puede eliminar.

> [!NOTE]
> El contenedor $logs no se muestra cuando se realiza una operación de lista de contenedores, como el método [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) . Se debe tener acceso a él directamente. Por ejemplo, puede utilizar el método [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) para obtener acceso a los blobs en el contenedor `$logs`.
> A medida que se registran las solicitudes, Storage Analytics cargará los resultados intermedios como bloques. Periódicamente, Storage Analytics confirmará estos bloques para que estén disponibles como blobs.
> 
> 

Es posible que haya entradas duplicadas en los registros creados durante la misma hora. Puede determinar si un registro es un duplicado comprobando el número **RequestId** y **Operation**.

### <a name="log-naming-conventions"></a>Convenciones de nomenclatura de los registros
Cada registro se escribirá en el formato siguiente:

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

En la tabla siguiente se describe cada atributo del nombre del registro:

| Atributo | DESCRIPCIÓN |
| --- | --- |
| <service-name> |El nombre del servicio de almacenamiento. Por ejemplo: blob, tabla o cola |
| AAAA |El año de cuatro dígitos para el registro. Por ejemplo: 2011 |
| MM |El mes de dos dígitos para el registro. Por ejemplo: 07. |
| DD |El mes de dos dígitos para el registro. Por ejemplo: 07. |
| hh |La hora de dos dígitos que indica la hora inicial para los registros, en formato UTC de 24 horas. Por ejemplo: 18. |
| MM |El número de dos dígitos que indica el minuto inicial para los registros. Este valor no se admite en la versión actual de Storage Analytics, y su valor será siempre 00. |
| <counter> |Un contador basado en cero con seis dígitos que indica el número de blobs del registro generados para el servicio de almacenamiento en un período de tiempo de una hora. Este contador se inicia en 000000. Por ejemplo: 000001. |

A continuación se muestra un nombre completo de registro de ejemplo que combina los ejemplos anteriores.

    blob/2011/07/31/1800/000001.log

A continuación se muestra un URI de ejemplo que se puede utilizar para obtener acceso al registro anterior.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

Cuando se registra una solicitud de almacenamiento, el nombre del registro resultante depende de la hora en la que se completó la operación solicitada. Por ejemplo, si una solicitud GetBlob se completó a las 6:30 P.M. el 31/07/2011, el registro se escribirá con el prefijo siguiente: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadatos del registro
Todos los blobs del registro se almacenan con metadatos que se pueden utilizar para identificar los datos de registro que contiene el blob. La tabla siguiente describe cada atributo de metadatos.

| Atributo | DESCRIPCIÓN |
| --- | --- |
| LogType |Describe si el registro contiene información relacionada con las operaciones de lectura, escritura o eliminación. Este valor puede incluir un tipo o una combinación de los tres, separados por comas. Ejemplo 1: escritura; Ejemplo 2: lectura, escritura; Ejemplo 3: lectura, escritura, eliminación. |
| StartTime |La primera hora de una entrada en el registro, en el formato YYYY-MM-DDThh:mm:ssZ. Por ejemplo: 2011-07-31T18:21:46Z. |
| EndTime |La hora más reciente de una entrada en el registro, en el formato YYYY-MM-DDThh:mm:ssZ. Por ejemplo: 2011-07-31T18:22:09Z. |
| LogVersion |La versión del formato del registro. Actualmente, el único valor admitido es: 1.0. |

La lista siguiente muestra metadatos completos de ejemplo utilizando los ejemplos anteriores.

* LogType=escritura
* StartTime=2011-07-31T18:21:46Z
* EndTime=2011-07-31T18:22:09Z
* LogVersion=1.0

### <a name="accessing-logging-data"></a>Obtener acceso a los datos de registro
Se puede tener acceso a todos los datos del contenedor `$logs` utilizando las API de Blob service, incluidas las API de .NET proporcionadas por la biblioteca administrada de Azure. El administrador de la cuenta de almacenamiento puede leer y eliminar registros, pero no puede crearlos ni actualizarlos. Los metadatos y el nombre del registro se pueden utilizar al consultar un registro. Es posible que los registros de una hora determinada aparezcan desordenados, pero los metadatos siempre especifican el intervalo de tiempo de las entradas de registro en un registro. Por consiguiente, se puede utilizar una combinación de nombres y metadatos de registro al buscar un registro concreto.

## <a name="about-storage-analytics-metrics"></a>Acerca de las métricas de Storage Analytics
Storage Analytics puede almacenar métricas que incluyen estadísticas acumuladas de las transacciones y datos de capacidad sobre las solicitudes realizadas a un servicio de almacenamiento. Las transacciones se notifican tanto en el nivel de operación de API como en el nivel de servicio de almacenamiento, y la capacidad se notifica en el nivel de servicio de almacenamiento. Los datos de las métricas se pueden utilizar para analizar el uso del servicio de almacenamiento, diagnosticar problemas con las solicitudes realizadas en el mismo y mejorar el rendimiento de las aplicaciones que utilizan un servicio.

Para utilizar Storage Analytics, debe habilitarlo para cada servicio que desee supervisar. Puede habilitarlo desde el [Azure Portal](https://portal.azure.com). Para obtener más detalles, consulte [Supervisión de una cuenta de almacenamiento en Azure Portal](storage-monitor-storage-account.md). También puede habilitar Storage Analytics mediante programación a través de la API de REST o la biblioteca de cliente. Use las operaciones **Get Service Properties** para habilitar Storage Analytics para cada servicio.

### <a name="transaction-metrics"></a>Métricas de transacciones
Se registra un conjunto sólido de datos a intervalos de cada hora o de cada minuto para cada servicio de almacenamiento y operación de API que se ha solicitado, incluidos entradas/salidas, disponibilidad, errores y porcentajes de solicitudes por categorías. Puede ver una lista completa de los detalles de transacción en el tema [Esquema de las tablas de métricas de Storage Analytics](https://msdn.microsoft.com/library/hh343264.aspx).

Los datos de las transacciones se registran en dos niveles: el nivel de servicio y el nivel de operación de API. En el nivel de servicio, las estadísticas que resumen todas las operaciones de API solicitadas se escriben en una entidad de la tabla cada hora, aunque no se haya realizado ninguna solicitud al servicio. En el nivel de operación de API, las estadísticas se escriben únicamente en una entidad si la operación se solicitó durante esa hora.

Por ejemplo, si realiza una operación **GetBlob** en Blob service, las métricas de análisis de Storage registrarán la solicitud y la incluirán en los datos agregados tanto para Blob service como para la operación **GetBlob**. Sin embargo, si no se solicita ninguna operación **GetBlob** durante esa hora, no escribirá ninguna entidad en la `$MetricsTransactionsBlob` para dicha operación.

Las métricas de transacciones se registran para las solicitudes del usuario y para las solicitudes realizadas por Storage Analytics. Por ejemplo, se registran las solicitudes realizadas por Storage Analytics para escribir registros y entidades de tabla. Para obtener más información sobre cómo se facturan estas solicitudes, consulte [Facturación y Storage Analytics](https://msdn.microsoft.com/library/hh360997.aspx).

### <a name="capacity-metrics"></a>Métricas de capacidad
> [!NOTE]
> Actualmente, las métricas de capacidad solo están disponibles para Blob service. Las métricas de capacidad para Table service y Queue service estarán disponibles en versiones futuras de Storage Analytics.
> 
> 

Los datos de capacidad se registran diariamente para la instancia de Blob service de una cuenta de almacenamiento, y se escriben dos entidades de tabla. Una entidad proporciona estadísticas para los datos de usuario y la otra proporciona estadísticas sobre el contenedor de blob `$logs` utilizado por Storage Analytics. La tabla `$MetricsCapacityBlob` incluye las estadísticas siguientes:

* **Capacity**: la cantidad de almacenamiento que ha utilizado la instancia de Blob service de la cuenta de almacenamiento, en bytes.
* **ContainerCount**: el número de contenedores de blobs de la instancia de Blob service de la cuenta de almacenamiento.
* **ObjectCount**: el número de blobs en bloque o en páginas confirmados y sin confirmar de la instancia de Blob service de la cuenta de almacenamiento.

Para obtener más información acerca de las métricas de capacidad, vea el [Esquema de las tablas de métricas de Storage Analytics](https://msdn.microsoft.com/library/hh343264.aspx).

### <a name="how-metrics-are-stored"></a>Cómo se almacenan las métricas
Todos los datos de métricas para cada uno de los servicios de almacenamiento se almacenan en tres tablas reservadas para ese servicio: una tabla para la información sobre transacciones, otra tabla para la información sobre las transacciones por minuto y una última tabla para la información sobre capacidad. La información sobre transacciones y transacciones por minuto consta de datos de solicitudes y respuestas, y la información sobre capacidad consta de datos de uso del almacenamiento. Se puede acceder a las métricas por hora y por minuto, y a la capacidad de la instancia de Blob service de una cuenta de almacenamiento, en tablas cuyos nombres se describen en la tabla siguiente.

| Nivel de métricas | Nombres de tabla | Versiones compatibles |
| --- | --- | --- |
| Métricas por horas, ubicación principal |$MetricsTransactionsBlob  <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue |Versiones anteriores a 2013-08-15 solamente. Si bien estos nombres todavía se admiten, se recomienda que empiece a usar las tablas que se muestran a continuación. |
| Métricas por horas, ubicación principal |$MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue |Todas las versiones, incluida 2013-08-15. |
| Métricas por minutos, ubicación principal |$MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue |Todas las versiones, incluida 2013-08-15. |
| Métricas por horas, ubicación secundaria |$MetricsHourSecondaryTransactionsBlob  <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue |Todas las versiones, incluida 2013-08-15. Debe estar habilitada la replicación con redundancia geográfica con acceso de lectura. |
| Métricas por minutos, ubicación secundaria |$MetricsMinuteSecondaryTransactionsBlob  <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue |Todas las versiones, incluida 2013-08-15. Debe estar habilitada la replicación con redundancia geográfica con acceso de lectura. |
| Capacidad (solo Blob service) |$MetricsCapacityBlob |Todas las versiones, incluida 2013-08-15. |

Estas tablas se crean automáticamente cuando se habilita Storage Analytics para una cuenta de almacenamiento. Se tiene acceso a ellas a través del espacio de nombres de la cuenta de almacenamiento, por ejemplo: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>Obtener acceso a los datos de las métricas
Se puede tener acceso a todos los datos de las tablas de las métricas utilizando las API de Table service, incluidas las API de .NET proporcionadas por la biblioteca administrada de Azure. El administrador de la cuenta de almacenamiento puede leer y eliminar entidades de tabla, pero no puede crearlas ni actualizarlas.

## <a name="billing-for-storage-analytics"></a>Facturación para Storage Analytics
Todos los datos de métricas los escriben los servicios de una cuenta de almacenamiento. Como resultado, cada una de las operaciones de escritura realizadas por Storage Analytics es facturable. También lo es la cantidad de almacenamiento utilizado por los datos de métricas.

Son facturables las acciones siguientes realizadas por Storage Analytics:

* Solicitudes para crear blobs para el registro 
* Solicitudes para crear entidades de tabla para las métricas

Si ha configurado una directiva de retención de datos, no se le cobrarán las transacciones de eliminación cuando Storage Analytics elimine los antiguos datos de métricas y de registro. Sin embargo, las transacciones de eliminación desde un cliente sí son facturables. Para obtener más información acerca de las directivas de retención, consulte [Establecer una directiva de retención de datos de Storage Analytics](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Descripción de las solicitudes facturables
Las solicitudes realizadas al servicio de almacenamiento de una cuenta son facturables o no facturables. Storage Analytics registra cada solicitud realizada a un servicio, incluyendo un mensaje de estado que indica cómo se administró la solicitud. De igual forma, Storage Analytics guarda las métricas para un servicio y para las operaciones de la API de dicho servicio, incluidos los porcentajes y el recuento de algunos mensajes de estado. Todas estas características pueden ayudarle a analizar las solicitudes facturables, a llevar a cabo mejoras en la aplicación y a diagnosticar problemas en las solicitudes a los servicios. Para obtener más información sobre la facturación, consulte [Descripción de la facturación de Azure Storage: ancho de banda, transacciones y capacidad](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Al examinar los datos de Storage Analytics, puede usar las tablas del tema [Operaciones y mensajes de estado registrados por Storage Analytics](https://msdn.microsoft.com/library/azure/hh343260.aspx) para determinar qué solicitudes son facturables. De esta manera, podrá comparar los datos de métricas y de registro con los mensajes de estado para ver si se le cobró por una solicitud determinada. También puede usar las tablas del tema anterior para investigar la disponibilidad de un servicio de almacenamiento o de una operación de API determinada.

## <a name="next-steps"></a>Pasos siguientes
### <a name="setting-up-storage-analytics"></a>Configuración de Storage Analytics
* [Supervisión de una cuenta de almacenamiento en Azure Portal](storage-monitor-storage-account.md)
* [Habilitar y configurar Storage Analytics](https://msdn.microsoft.com/library/hh360996.aspx)

### <a name="storage-analytics-logging"></a>Registro de Storage Analytics
* [Acerca del registro de Storage Analytics](https://msdn.microsoft.com/library/hh343262.aspx)
* [Formato del registro de Storage Analytics](https://msdn.microsoft.com/library/hh343259.aspx)
* [Operaciones y mensajes de estado registrados por Storage Analytics](https://msdn.microsoft.com/library/hh343260.aspx)

### <a name="storage-analytics-metrics"></a>Métricas de Storage Analytics
* [Acerca de las métricas de Storage Analytics](https://msdn.microsoft.com/library/hh343258.aspx)
* [Esquema de las tablas de métricas de Storage Analytics](https://msdn.microsoft.com/library/hh343264.aspx)
* [Operaciones y mensajes de estado registrados por Storage Analytics](https://msdn.microsoft.com/library/hh343260.aspx)  

