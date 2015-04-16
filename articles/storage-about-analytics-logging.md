<properties 
	pageTitle="Acerca del registro del análisis de almacenamiento" 
	description="Aprenda a usar el registro de análisis de almacenamiento y las solicitudes de autenticación, y cómo se almacenan los registros " 
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

# Acerca del registro del análisis de almacenamiento

## Información general
El análisis de almacenamiento registra información detallada sobre las solicitudes correctas y erróneas realizadas a un servicio de almacenamiento. Esta información se puede utilizar para supervisar solicitudes concretas y para diagnosticar problemas con un servicio de almacenamiento. Las solicitudes se registran en función de la mejor opción.

Para utilizar el análisis de almacenamiento, debe habilitarlo para cada servicio que desee supervisar. Puede habilitarlo desde el [Portal de administración de Azure](https://manage.windowsazure.com/); para obtener información detallada, consulte [Cómo supervisar una cuenta de almacenamiento](how-to-monitor-a-storage-account.md). También puede habilitar el análisis de almacenamiento mediante programación a través de la API de REST o la biblioteca de cliente. Use las operaciones Get Blob Service Properties, Get Queue Service Properties y Get Table Service Properties para habilitar el Análisis de almacenamiento para cada servicio.

Solo se crean entradas del registro si hay actividad del servicio de almacenamiento. Por ejemplo, si una cuenta del almacenamiento tiene actividad en el servicio Blob pero no en los servicios Tabla o Cola, solo se crearán los registros que pertenezcan al servicio Blob.

## Registrar solicitudes de autenticación
Se registran los siguientes tipos de solicitudes autenticadas:

- Solicitudes correctas

- Solicitudes erróneas, incluyendo errores de tiempo de espera, de limitación, de red, de autorización, etc

- Solicitudes que utilizan una firma de acceso compartido (SAS), incluyendo las solicitudes correctas y las erróneas

- Solicitudes de datos de análisis

Las solicitudes realizadas por el propio análisis de almacenamiento, como la creación o eliminación del registro, no se registran. Puede encontrar una lista completa de los datos registrados en los temas [Operaciones y mensajes de estado registrados por el análisis de almacenamiento](https://msdn.microsoft.com/library/hh343260.aspx) y [Formato del registro del análisis de almacenamiento](https://msdn.microsoft.com/library/hh343259.aspx).

## Registrar solicitudes anónimas
Se registran los siguientes tipos de solicitudes anónimas:

- Solicitudes correctas

- Errores del servidor

- Errores de tiempo de espera para el cliente y el servidor

- Solicitudes GET erróneas con el código de error 304 (Sin modificar)

El resto de solicitudes anónimas erróneas no se registran. Puede encontrar una lista completa de los datos registrados en los temas [Operaciones y mensajes de estado registrados por el análisis de almacenamiento](https://msdn.microsoft.com/library/hh343260.aspx) y [Formato del registro del análisis de almacenamiento](](https://msdn.microsoft.com/library/hh343259.aspx)).

## Cómo se almacenan los registros
Todos los registros se almacenan en blobs en bloques en un contenedor denominado $logs, que se crea automáticamente cuando se habilita el análisis de almacenamiento para una cuenta de almacenamiento. El contenedor $logs se encuentra en el espacio de nombres del blob de la cuenta de almacenamiento, por ejemplo: `http://<accountname>.blob.core.windows.net/$logs`. Este contenedor no se puede eliminar una vez habilitado el análisis de almacenamiento, aunque su contenido sí se puede eliminar.

>[Azure.NOTE] El contenedor $logs no se muestra cuando se realiza una operación de lista de contenedores, como el método [ListContainers](https://msdn.microsoft.com/library/ee758348.aspx). Se debe tener acceso a él directamente. Por ejemplo, puede utilizar el método [ListBlobs](https://msdn.microsoft.com/library/ee772878.aspx) para acceder a los blobs en el contenedor `$logs`.
A medida que se registran las solicitudes, el análisis de almacenamiento cargará los resultados intermedios como bloques. Periódicamente, el análisis de almacenamiento confirmará estos bloques para que estén disponibles como blobs.

Es posible que haya entradas duplicadas en los registros creados durante la misma hora. Puede determinar si un registro es un duplicado comprobando el número **RequestId** y **Operation**.

## Convenciones de nomenclatura de los registros
Cada registro se escribirá en el formato siguiente:

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log 

En la tabla siguiente se describe cada atributo del nombre del registro:

| Atributo      	| Descripción                                                                                                                                                                                	|
|----------------	|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| <service-name> 	| El nombre del servicio de almacenamiento. Por ejemplo: blob, tabla o cola                                                                                                                        	|
| AAAA           	| El año de cuatro dígitos para el registro. Por ejemplo: 2011                                                                                                                                         	|
| MM             	| El mes de dos dígitos para el registro. Por ejemplo: 07                                                                                                                                           	|
| DD             	| El mes de dos dígitos para el registro. Por ejemplo: 07                                                                                                                                           	|
| hh             	| La hora de dos dígitos que indica la hora inicial para los registros, en formato UTC de 24 horas. Por ejemplo: 18                                                                                   	|
| mm             	| El número de dos dígitos que indica el minuto inicial para los registros. >[AZURE.NOTE]Este valor no se admite en la versión actual del análisis de almacenamiento, y su valor será siempre 00. 	|
| <counter>      	| Un contador basado en cero con seis dígitos que indica el número de blobs del registro generados para el servicio de almacenamiento en un período de tiempo de una hora. Este contador se inicia en 000000. Por ejemplo: 000001   	|

A continuación se muestra un nombre completo de registro de ejemplo que combina los ejemplos anteriores:

    blob/2011/07/31/1800/000001.log

A continuación se muestra un URI de ejemplo que se puede utilizar para obtener acceso al registro anterior:

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log 

Cuando se registra una solicitud de almacenamiento, el nombre del registro resultante depende de la hora en la que se completó la operación solicitada. Por ejemplo, si una solicitud GetBlob se completó a las 6:30 p.m. el 31/07/2011, el registro se escribirá con el prefijo siguiente: `blob/2011/07/31/1800/`

## Metadatos del registro
Todos los blobs del registro se almacenan con metadatos que se pueden utilizar para identificar los datos de registro que contiene el blob. La tabla siguiente describe cada atributo de metadatos:

| Atributo  	| Descripción                                                                                                                                                                                                                                               	|
|------------	|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| LogType    	| Describe si el registro contiene información relacionada con las operaciones de lectura, escritura o eliminación. Este valor puede incluir un tipo o una combinación de los tres, separados por comas.   Ejemplo 1: escritura; Ejemplo 2: lectura, escritura; Ejemplo 3: lectura, escritura, eliminación 	|
| StartTime  	| La primera hora de una entrada en el registro, en el formato YYYY-MM-DDThh:mm:ssZ. Por ejemplo: 2011-07-31T18:21:46Z                                                                                                                                          	|
| EndTime    	| La hora más reciente de una entrada en el registro, en el formato YYYY-MM-DDThh:mm:ssZ. Por ejemplo: 2011-07-31T18:22:09Z                                                                                                                                            	|
| LogVersion 	| La versión del formato del registro. Actualmente, el único valor admitido es: 1,0                                                                                                                                                                                 	|

La lista siguiente muestra metadatos completos de ejemplo utilizando los ejemplos anteriores:

- LogType=escritura 

- StartTime=2011-07-31T18:21:46Z 

- EndTime=2011-07-31T18:22:09Z 

- LogVersion=1.0 

## Obtener acceso a los datos de registro

Se puede tener acceso a todos los datos del contenedor `$logs` utilizando las API del servicio Blob, incluidas las API de .NET proporcionadas por la biblioteca administrada de Azure. El administrador de la cuenta de almacenamiento puede leer y eliminar registros, pero no puede crearlos ni actualizarlos. Los metadatos y el nombre del registro se pueden utilizar al consultar un registro. Es posible que los registros de una hora determinada aparezcan desordenados, pero los metadatos siempre especifican el intervalo de tiempo de las entradas de registro en un registro. Por consiguiente, se puede utilizar una combinación de nombres y metadatos de registro al buscar un registro concreto.

## Pasos siguientes

[Formato del registro del análisis de almacenamiento](https://msdn.microsoft.com/library/hh343259.aspx) 

[Operaciones y mensajes de estado registrados por el análisis de almacenamiento](https://msdn.microsoft.com/library/hh343260.aspx) 

[Acerca de las métricas del análisis de almacenamiento](https://msdn.microsoft.com/library/hh343258.aspx) 
<!--HONumber=47-->
