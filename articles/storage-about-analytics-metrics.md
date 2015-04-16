<properties 
	pageTitle="Acerca de las métricas del análisis de almacenamiento" 
	description="Aprenda a usar el análisis de almacenamiento, incluidas las métricas de transacciones y las métricas de capacidad, cómo se almacenan las métricas y cómo tener acceso a datos de métricas." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Acerca de las métricas del análisis de almacenamiento

## Información general

El análisis de almacenamiento puede almacenar métricas que incluyen estadísticas acumuladas de las transacciones y datos de capacidad sobre las solicitudes realizadas a un servicio de almacenamiento. Las transacciones se notifican tanto en el nivel de operación de API como en el nivel de servicio de almacenamiento, y la capacidad se notifica en el nivel de servicio de almacenamiento. Los datos de las métricas se pueden utilizar para analizar el uso del servicio de almacenamiento, diagnosticar problemas con las solicitudes realizadas en el mismo y mejorar el rendimiento de las aplicaciones que utilizan un servicio.

Para utilizar el análisis de almacenamiento, debe habilitarlo para cada servicio que desee supervisar. Puede habilitarlo desde el [Portal de administración de Azure](https://manage.windowsazure.com/); para obtener información detallada, consulte [Cómo supervisar una cuenta de almacenamiento](how-to-monitor-a-storage-account.md). También puede habilitar el análisis de almacenamiento mediante programación a través de la API de REST o la biblioteca de cliente. [Use las operaciones Get Blob Service Properties, Get Queue Service Properties](https://msdn.microsoft.com/library/hh452239.aspx) y [Get Table Service Properties para habilitar el Análisis de almacenamiento](https://msdn.microsoft.com/library/hh452238.aspx) para cada servicio.

## Métricas de transacciones

Se registra un conjunto sólido de datos a intervalos de cada hora o de cada minuto para cada servicio de almacenamiento y operación de API que se ha solicitado, incluidos entradas/salidas, disponibilidad, errores y porcentajes de solicitudes por categorías. Puede ver una lista completa de los detalles de transacción en el tema [Esquema de las tablas de métricas del análisis de almacenamiento](https://msdn.microsoft.com/library/hh343264.aspx).

Los datos de las transacciones se registran en dos niveles: el nivel de servicio y el nivel de operación de API. En el nivel de servicio, las estadísticas que resumen todas las operaciones de API solicitadas se escriben en una entidad de la tabla cada hora, aunque no se haya realizado ninguna solicitud al servicio. En el nivel de operación de API, las estadísticas se escriben únicamente en una entidad si la operación se solicitó durante esa hora.

Por ejemplo, si realiza una operación **GetBlob** en el servicio Blob, las métricas de análisis de almacenamiento registrarán la solicitud y la incluirán en los datos agregados tanto para el servicio Blob como para la operación **GetBlob**. Sin embargo, si no se solicita ninguna operación **GetBlob** durante esa hora, no escribirá ninguna entidad en la tabla `$MetricsTransactionsBlob` para dicha operación.

Las métricas de transacciones se registran para las solicitudes del usuario y para las solicitudes realizadas por el análisis de almacenamiento. Por ejemplo, se registran las solicitudes realizadas por el análisis de almacenamiento para escribir registros y entidades de tabla. Para obtener más información sobre cómo se facturan estas solicitudes, consulte [Facturación y análisis de almacenamiento](https://msdn.microsoft.com/library/hh360997.aspx).

## Métricas de capacidad

>[AZURE.NOTE] Actualmente, las métricas de capacidad solo están disponibles para el servicio Blob. Las métricas de capacidad para el servicio Tabla y el servicio Cola estarán disponibles en versiones futuras del análisis de almacenamiento.

Los datos de capacidad se registran diariamente para el servicio Blob de una cuenta de almacenamiento, y se escriben dos entidades de tabla. Una entidad proporciona estadísticas para los datos de usuario y la otra proporciona estadísticas sobre el contenedor de blob `$logs` utilizado por el análisis de almacenamiento. La tabla `$MetricsCapacityBlob` incluye las estadísticas siguientes:

- **Capacity**: La cantidad de almacenamiento utilizado por el servicio Blob de la cuenta de almacenamiento, en bytes.

- **ContainerCount**: El número de contenedores de blobs del servicio Blob de la cuenta de almacenamiento.

- **ObjectCount**: El número de blobs en bloque o en páginas confirmados y sin confirmar del servicio Blob de la cuenta de almacenamiento.

Para obtener más información acerca de las métricas de capacidad, vea el Esquema de las tablas de métricas del análisis de almacenamiento.

## Cómo se almacenan las métricas

Todos los datos de métricas para cada uno de los servicios de almacenamiento se almacenan en tres tablas reservadas para ese servicio: una tabla para la información sobre transacciones, otra tabla para la información sobre las transacciones por minuto y una última tabla para la información sobre capacidad. La información sobre transacciones y transacciones por minuto consta de datos de solicitudes y respuestas, y la información sobre capacidad consta de datos de uso del almacenamiento. Se puede acceder a las métricas por hora y por minuto, y a la capacidad del servicio Blob de una cuenta de almacenamiento, en tablas cuyos nombres se describen en la tabla siguiente.

| Nivel de métricas                      	| Nombres de tabla                                                                                                                 	| Versiones para las que se admiten                                                                                                                       	|
|------------------------------------	|-----------------------------------------------------------------------------------------------------------------------------	|----------------------------------------------------------------------------------------------------------------------------------------------	|
| Métricas por horas, ubicación principal   	|  $MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue                                              	| Versiones anteriores a 2013-08-15 solamente. Si bien estos nombres todavía se admiten, se recomienda que empiece a usar las tablas que se muestran a continuación. 	|
| Métricas por horas, ubicación principal   	| $MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue             	| Todas las versiones, incluida 2013-08-15                                                                                                           	|
| Métricas por minutos, ubicación principal   	| $MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue       	| Todas las versiones, incluida 2013-08-15                                                                                                           	|
| Métricas por horas, ubicación secundaria 	| $MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue       	| Todas las versiones, incluida 2013-08-15. Debe estar habilitada la replicación con redundancia geográfica con acceso de lectura.                                                   	|
| Métricas por minutos, ubicación secundaria 	| $MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue 	| Todas las versiones, incluida 2013-08-15. Debe estar habilitada la replicación con redundancia geográfica con acceso de lectura.                                                   	|
| Capacidad (solo servicio Blob)       	| $MetricsCapacityBlob                                                                                                        	| Todas las versiones, incluida 2013-08-15                                                                                                           	|



Estas tablas se crean automáticamente cuando se habilita el análisis de almacenamiento para una cuenta de almacenamiento. Se tiene acceso a ellas a través del espacio de nombres de la cuenta de almacenamiento, por ejemplo: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`


## Obtener acceso a los datos de las métricas

Se puede tener acceso a todos los datos de las tablas de las métricas utilizando las API del servicio Tabla, incluidas las API de .NET proporcionadas por la biblioteca administrada de Azure. El administrador de la cuenta de almacenamiento puede leer y eliminar entidades de tabla, pero no puede crearlas ni actualizarlas.

## Pasos siguientes
### Conceptos
[Habilitar y configurar el análisis de almacenamiento](https://msdn.microsoft.com/library/hh360996.aspx) 

[Esquema de las tablas de métricas del análisis de almacenamiento](https://msdn.microsoft.com/library/hh343264.aspx) 

[Operaciones y mensajes de estado registrados por el análisis de almacenamiento](https://msdn.microsoft.com/library/hh343260.aspx) 

[Acerca del registro del análisis de almacenamiento](https://msdn.microsoft.com/library/hh343262.aspx) 

### Otros recursos

[Supervisión de una cuenta de almacenamiento](how-to-monitor-a-storage-account.md) 
<!--HONumber=47-->
