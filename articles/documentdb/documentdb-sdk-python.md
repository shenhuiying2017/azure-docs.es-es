<properties 
	pageTitle="SDK y API para Python de DocumentDB | Microsoft Azure" 
	description="Obtenga toda la información sobre el SDK y la API para Python como, por ejemplo, fechas de lanzamiento, fechas de retirada y cambios de una versión a otra del SDK para Python de DocumentDB." 
	services="documentdb" 
	documentationCenter="python" 
	authors="rnagpal" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="08/09/2016" 
	ms.author="rnagpal"/>

# SDK y API de DocumentDB

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [REST](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## SDK y API para Python de DocumentDB

<table>
<tr><td>**Descarga del SDK**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>
<tr><td>**Documentación de la API**</td><td>[Documentación de referencia de la API para Python] (http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>
<tr><td>**Instrucciones de instalación del SDK**</td><td>[Documentación de referencia del SDK para Python] (http://azure.github.io/azure-documentdb-python/)</td></tr>
<tr><td>**Contribuya al SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>
<tr><td>**Introducción**</td><td>[Introducción al SDK de Python] (documentdb-python-application.md)</td></tr>
<tr><td>**Plataforma admitida actualmente**</td><td>[Python 2.7](https://www.python.org/download/releases/2.7/)</td></tr>
</table></br>

## Notas de la versión

### <a name="1.9.0"/>[1\.9.0](https://pypi.python.org/pypi/pydocumentdb/1.9.0)
- Se ha agregado compatibilidad de la directiva de reintentos con las solicitudes de limitación. (Las solicitudes limitadas reciben una excepción demasiado grande de la tasa de solicitudes, código de error 429). De manera predeterminada, DocumentDB realiza nueve reintentos para cada solicitud cuando aparece el código de error 429, cumpliendo el tiempo de retryAfter en el encabezado de respuesta. Ahora puede establecerse un tiempo del intervalo de reintento fijo como parte de la propiedad RetryOptions del objeto ConnectionPolicy si quiere ignorar el tiempo de retryAfter que ha devuelto el servidor entre los reintentos. DocumentDB espera ahora un máximo de 30 segundos para cada solicitud que se está limitando (independientemente del recuento de reintentos) y devuelve la respuesta con el código de error 429. Este tiempo también puede reemplazarse en la propiedad RetryOptions del objeto ConnectionPolicy.

- DocumentDB ahora devuelve x-ms-throttle-retry-count y x-ms-throttle-retry-wait-time-ms como los encabezados de respuesta de cada solicitud para denotar el recuento de reintentos de limitación y el tiempo acumulativo que ha esperado la solicitud entre los reintentos.

- Se ha quitado la clase RetryPolicy y la propiedad correspondiente (retry\_policy) que estaba expuesta en la clase document\_client y, en su lugar, se ha introducido una clase RetryOptions que expone la propiedad RetryOptions en la clase ConnectionPolicy que puede usarse para reemplazar algunas de las opciones de reintentos predeterminadas.

### <a name="1.8.0"/>[1\.8.0](https://pypi.python.org/pypi/pydocumentdb/1.8.0)
  - Se ha agregado compatibilidad con cuentas de base de datos de varias regiones.

### <a name="1.7.0"/>[1\.7.0](https://pypi.python.org/pypi/pydocumentdb/1.7.0)
- Se ha agregado compatibilidad con la característica de período de vida (TTL) para los documentos.

### <a name="1.6.1"/>[1\.6.1](https://pypi.python.org/pypi/pydocumentdb/1.6.1)
- Correcciones de errores relacionados con la creación de particiones del lado servidor para permitir caracteres especiales en la ruta de acceso de la clave de partición.

### <a name="1.6.0"/>[1\.6.0](https://pypi.python.org/pypi/pydocumentdb/1.6.0)
- Se han implementado [colecciones particionadas](documentdb-partition-data.md) y [niveles de rendimiento definidos por el usuario](documentdb-performance-levels.md).

### <a name="1.5.0"/>[1\.5.0](https://pypi.python.org/pypi/pydocumentdb/1.5.0)
- Se han agregado solucionadores de particiones de hash e intervalo para ayudar con el particionamiento de las aplicaciones entre varias particiones.

### <a name="1.4.2"/>[1\.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
- Implementación de Upsert. Se han agregado nuevos métodos upsertXXX para admitir la característica Upsert.
- Se implementa el enrutamiento por identificador. Sin cambios en la API pública, todos los cambios son internos.

### <a name="1.2.0"/>[1\.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
- Compatible con índice geoespacial.
- Valida la propiedad id para todos los recursos. Los identificadores de recursos no pueden contener los caracteres ?, /, #, \\, ni terminar con un espacio.
- Agrega el nuevo encabezado "progreso de transformación de índices" a ResourceResponse.

### <a name="1.1.0"/>[1\.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
- Implementación de la directiva de indexación V2.

### <a name="1.0.1"/>[1\.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
- Compatibilidad con conexión de proxy.

### <a name="1.0.0"/>[1\.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
- SDK de GA.

## Fechas de lanzamiento y retirada
Microsoft notificará la retirada de un SDK con al menos **12 meses** de antelación para facilitar la transición a una versión compatible o más reciente.

Solo se agregan nuevas características, funcionalidad y optimizaciones al SDK actual, por lo que se recomienda actualizar siempre a la última versión del SDK tan pronto como sea posible.

El servicio rechazará cualquier solicitud realizada en DocumentDB mediante un SDK retirado.

> [AZURE.WARNING]
Todas las versiones del SDK de Azure DocumentDB para Python anteriores a la versión **1.0.0** se retirarán el **29 de febrero de 2016**.

<br/>

| Versión | Fecha de lanzamiento | Fecha de retirada 
| ---	  | ---	         | ---
| [1.9.0](#1.9.0) | 07 de julio de 2016 |---
| [1.8.0](#1.8.0) | 14 de julio de 2016 |---
| [1.7.0](#1.7.0) | 26 de abril de 2016 |---
| [1.6.1](#1.6.1) | 08 de abril de 2016 |---
| [1.6.0](#1.6.0) | 29 de marzo de 2016 |---
| [1\.5.0](#1.5.0) | 03 de enero de 2016 |--- 
| [1\.4.2](#1.4.2) | 06 de octubre de 2015 |--- 
| [1\.4.1](#1.4.1) | 06 de octubre de 2015 |--- 
| [1\.2.0](#1.2.0) | 06 de agosto de 2015 |--- 
| [1\.1.0](#1.1.0) | 09 de julio de 2015 |--- 
| [1\.0.1](#1.0.1) | 25 de mayo de 2015 |--- 
| [1\.0.0](#1.0.0) | 07 de abril de 2015 |--- 
| versión preliminar 0.9.4 | 14 de enero de 2015 | 29 de febrero de 2016 
| versión preliminar 0.9.3 | 09 de diciembre de 2014 | 29 de febrero de 2016 
| versión preliminar 0.9.2 | 25 de noviembre de 2014 | 29 de febrero de 2016 
| versión preliminar 0.9.1 | 23 de septiembre de 2014 | 29 de febrero de 2016 
| versión preliminar 0.9.0 | 21 de agosto de 2014 | 29 de febrero de 2016

## P+F
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Otras referencias

Para más información sobre DocumentDB, consulte la página del servicio [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0810_2016-->
