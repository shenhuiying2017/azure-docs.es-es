---
title: 'API de Node.js y recursos del SDK: Azure DocumentDB | Microsoft Docs'
description: "Obtenga toda la información sobre el SDK y la API para Node.js como, por ejemplo, fechas de lanzamiento, fechas de retirada y cambios de una versión a otra del SDK para Node.js de DocumentDB."
services: documentdb
documentationcenter: nodejs
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 9d5621fa-0e11-4619-a28b-a19d872bcf37
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/22/2016
ms.author: rnagpal
translationtype: Human Translation
ms.sourcegitcommit: a6aadaae2a9400dc62ab277d89d9a9657833b1b7
ms.openlocfilehash: 43d658a67eb55a2d2e35f79080d63c3effb6387e


---
# <a name="documentdb-apis-and-sdks"></a>SDK y API de DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/en-us/rest/api/documentdb/)
> * [Proveedor de recursos de REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

## <a name="documentdb-nodejs-api-and-sdk"></a>SDK para Node.js de DocumentDB
<table>

<tr><td>**Descargar SDK**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>

<tr><td>**Documentación de la API**</td><td>[Documentación de referencia de la API de Node.js](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>

<tr><td>**Instrucciones de instalación del SDK**</td><td>[Instrucciones de instalación](http://azure.github.io/azure-documentdb-node/)</td></tr>

<tr><td>**Contribuya al SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>

<tr><td>**Ejemplos**</td><td>[Ejemplos de código Node.js](documentdb-nodejs-samples.md)</td></tr>

<tr><td>**Tutorial introductorio**</td><td>[Introducción al SDK de Node.js](documentdb-nodejs-get-started.md)</td></tr>

<tr><td>**Tutorial de la aplicación web**</td><td>[Compilación de una aplicación web Node.js mediante DocumentDB](documentdb-nodejs-application.md)</td></tr>

<tr><td>**Plataforma admitida actualmente**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de la versión

### <a name="a-name11011101a"></a><a name="1.10.1"/>1.10.1</a>
* Se ha deshabilitado la comprobación de SSL cuando se usa el SDK para alcanzar el emulador (nombre de host = host local).
* Se ha agregado compatibilidad para habilitar el registro de scripts durante la ejecución de procedimientos almacenados.

### <a name="a-name11001100a"></a><a name="1.10.0"/>1.10.0</a>
* Compatibilidad agregada con las consultas paralelas entre particiones.
* Se ha agregado compatibilidad con las consultas TOP y ORDER BY en las colecciones particionadas.

### <a name="a-name190190a"></a><a name="1.9.0"/>1.9.0</a>
* Se ha agregado compatibilidad de la directiva de reintentos con las solicitudes de limitación. (Las solicitudes limitadas reciben una excepción demasiado grande de la tasa de solicitudes, código de error 429). De manera predeterminada, DocumentDB realiza nueve reintentos para cada solicitud cuando aparece el código de error 429, cumpliendo el tiempo de retryAfter en el encabezado de respuesta. Ahora puede establecerse un tiempo del intervalo de reintento fijo como parte de la propiedad RetryOptions del objeto ConnectionPolicy si quiere ignorar el tiempo de retryAfter que ha devuelto el servidor entre los reintentos. DocumentDB espera ahora un máximo de 30 segundos para cada solicitud que se está limitando (independientemente del recuento de reintentos) y devuelve la respuesta con el código de error 429. Este tiempo también puede reemplazarse en la propiedad RetryOptions del objeto ConnectionPolicy.
* DocumentDB ahora devuelve x-ms-throttle-retry-count y x-ms-throttle-retry-wait-time-ms como los encabezados de respuesta de cada solicitud para denotar el recuento de reintentos de limitación y el tiempo acumulativo que ha esperado la solicitud entre los reintentos.
* Se ha agregado la clase RetryOptions que expone la propiedad RetryOptions en la clase ConnectionPolicy que puede usarse para reemplazar algunas de las opciones de reintentos predeterminadas.

### <a name="a-name180180a"></a><a name="1.8.0"/>1.8.0</a>
* Se ha agregado compatibilidad con cuentas de base de datos de varias regiones.

### <a name="a-name170170a"></a><a name="1.7.0"/>1.7.0</a>
* Se ha agregado compatibilidad con la característica de período de vida (TTL) para los documentos.

### <a name="a-name160160a"></a><a name="1.6.0"/>1.6.0</a>
* Se han implementado [colecciones particionadas](documentdb-partition-data.md) y [niveles de rendimiento definidos por el usuario](documentdb-performance-levels.md).

### <a name="a-name156156a"></a><a name="1.5.6"/>1.5.6</a>
* Solucionado el error RangePartitionResolver.resolveForRead por el que no se devolvían vínculos debido a una mala concatenación de resultados.

### <a name="a-name155155a"></a><a name="1.5.5"/>1.5.5</a>
* Valor hashParitionResolver resolveForRead() fijo: cuando ninguna clave de partición proporcionada lanzó una excepción, en lugar de devolver una lista de todos los vínculos registrados.

### <a name="a-name154154a"></a><a name="1.5.4"/>1.5.4</a>
* Corrige el problema [100](https://github.com/Azure/azure-documentdb-node/issues/100) : agente específico de HTTPS (evitar la modificación del agente global para fines de DocumentDB). Use un agente dedicado para todas las solicitudes de lib.

### <a name="a-name153153a"></a><a name="1.5.3"/>1.5.3</a>
* Corrige el problema [81](https://github.com/Azure/azure-documentdb-node/issues/81) : controla correctamente guiones en identificadores de medios.

### <a name="a-name152152a"></a><a name="1.5.2"/>1.5.2</a>
* Corrige el problema [95](https://github.com/Azure/azure-documentdb-node/issues/95) : advertencia de pérdida de escucha de EventEmitter.

### <a name="a-name151151a"></a><a name="1.5.1"/>1.5.1</a>
* Corrige el problema [92](https://github.com/Azure/azure-documentdb-node/issues/90) : cambio de nombre de la carpeta Hash a hash para sistemas que distinguen mayúsculas de minúsculas.

### <a name="a-name150150a"></a><a name="1.5.0"/>1.5.0</a>
* Se implementa la compatibilidad con el particionamiento, para lo que se agregan resolvedores de hash y de particiones de intervalo.

### <a name="a-name140140a"></a><a name="1.4.0"/>1.4.0</a>
* Implementación de Upsert. Se han agregado nuevos métodos upsertXXX en documentClient.

### <a name="a-name130130a"></a><a name="1.3.0"/>1.3.0</a>
* Omitida para alinear el número de versión con otros SDK.

### <a name="a-name122122a"></a><a name="1.2.2"/>1.2.2</a>
* Divide el contenedor de objetos Q promise en nuevo repositorio.
* Actualización del archivo de paquete para registro npm.

### <a name="a-name121121a"></a><a name="1.2.1"/>1.2.1</a>
* Se implementa el enrutamiento por identificador.
* Corrige el problema [49](https://github.com/Azure/azure-documentdb-node/issues/49) : la propiedad current entra en conflicto con el método current().

### <a name="a-name120120a"></a><a name="1.2.0"/>1.2.0</a>
* Se agregó compatibilidad con índice geoespacial.
* Valida la propiedad id para todos los recursos. Los identificadores de recursos no pueden contener los caracteres ?, /, #, &#47;&#47;, ni terminar con un espacio.
* Agrega el nuevo encabezado "progreso de transformación de índices" a ResourceResponse.

### <a name="a-name110110a"></a><a name="1.1.0"/>1.1.0</a>
* Implementación de la directiva de indexación V2.

### <a name="a-name103103a"></a><a name="1.0.3"/>1.0.3</a>
* Problema [40](https://github.com/Azure/azure-documentdb-node/issues/40) : se implementaron configuraciones eslint y grunt en el núcleo y el SDK de promise.

### <a name="a-name102102a"></a><a name="1.0.2"/>1.0.2</a>
* Problema [#45](https://github.com/Azure/azure-documentdb-node/issues/45) : el contenedor de objetos promise no incluye el encabezado con el error.

### <a name="a-name101101a"></a><a name="1.0.1"/>1.0.1</a>
* Implementación de capacidad para consultar conflictos agregando readConflicts, readConflictAsync y queryConflicts.
* Documentación de la API actualizada.
* Problema [41](https://github.com/Azure/azure-documentdb-node/issues/41) : error client.createDocumentAsync.

### <a name="a-name100100a"></a><a name="1.0.0"/>1.0.0</a>
* SDK de GA.

## <a name="release--retirement-dates"></a>Fechas de lanzamiento y de retirada
Microsoft notificará la retirada de un SDK con al menos **12 meses** de antelación para facilitar la transición a una versión compatible o más reciente.

Solo se agregan nuevas características, funcionalidad y optimizaciones al SDK actual, por lo que se recomienda actualizar siempre a la última versión del SDK tan pronto como sea posible.

El servicio rechazará cualquier solicitud realizada en DocumentDB mediante un SDK retirado.

<br/>

| Versión | Fecha de lanzamiento | Fecha de retirada |
| --- | --- | --- |
| [1.10.1](#1.10.1) |22 de diciembre de 2016 |--- |
| [1.10.0](#1.10.0) |03 de octubre de 2016 |--- |
| [1.9.0](#1.9.0) |7 de julio de 2016 |--- |
| [1.8.0](#1.8.0) |14 de junio de 2016 |--- |
| [1.7.0](#1.7.0) |26 de abril de 2016 |--- |
| [1.6.0](#1.6.0) |29 de marzo de 2016 |--- |
| [1.5.6](#1.5.6) |8 de marzo de 2016 |--- |
| [1.5.5](#1.5.5) |2 de febrero de 2016 |--- |
| [1.5.4](#1.5.4) |1 de febrero de 2016 |--- |
| [1.5.2](#1.5.2) |26 de enero de 2016 |--- |
| [1.5.2](#1.5.2) |22 de enero de 2016 |--- |
| [1.5.1](#1.5.1) |4 de enero de 2016 |--- |
| [1.5.0](#1.5.0) |31 de diciembre de 2015 |--- |
| [1.4.0](#1.4.0) |6 de octubre de 2015 |--- |
| [1.3.0](#1.3.0) |6 de octubre de 2015 |--- |
| [1.2.2](#1.2.2) |10 de septiembre de 2015 |--- |
| [1.2.1](#1.2.1) |15 de agosto de 2015 |--- |
| [1.2.0](#1.2.0) |05 de agosto de 2015 |--- |
| [1.1.0](#1.1.0) |09 de julio de 2015 |--- |
| [1.0.3](#1.0.3) |4 de junio de 2015 |--- |
| [1.0.2](#1.0.2) |23 de mayo de 2015 |--- |
| [1.0.1](#1.0.1) |15 de mayo de 2015 |--- |
| [1.0.0](#1.0.0) |08 de abril de 2015 |--- |

## <a name="faq"></a>Preguntas más frecuentes
[!INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Otras referencias
Para más información sobre DocumentDB, consulte la página del servicio [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) .




<!--HONumber=Jan17_HO4-->


