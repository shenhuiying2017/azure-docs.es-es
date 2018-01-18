---
title: API, SDK y recursos de Python para SQL de Azure Cosmos DB | Microsoft Docs
description: "Obtenga toda la información sobre la API y el SDK de Python para SQL, incluidas la fechas de lanzamiento, fechas de retirada y cambios realizados entre las versiones del SDK de Python para Azure Cosmos DB."
services: cosmos-db
documentationcenter: python
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 3ac344a9-b2fa-4a3f-a4cc-02d287e05469
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 1/4/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6801c5b62be08e4dcb32ad342b15e9ad3f3e20a8
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>SDK de Python para la API de SQL de Azure Cosmos DB: notas de la versión y recursos
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Fuente de cambios de .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [Proveedor de recursos de REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

<table>

<tr><td>**Descargar SDK**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>

<tr><td>**Documentación de la API**</td><td>[Documentación de referencia de la API de Python](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>

<tr><td>**Instrucciones de instalación del SDK**</td><td>[Instrucciones de instalación del SDK de Python](http://azure.github.io/azure-documentdb-python/)</td></tr>

<tr><td>**Contribuya al SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>

<tr><td>**Primeros pasos**</td><td>[Introducción al SDK de Python](sql-api-python-application.md)</td></tr>

<tr><td>**Plataforma admitida actualmente**</td><td>[Python 2.7](https://www.python.org/downloads/) y [Python 3.5](https://www.python.org/downloads/)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de la versión
### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Documentación actualizada para la referencia de Microsoft Azure Cosmos DB en lugar de Azure DocumentDB.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Esta versión del SDK requiere la versión más reciente del emulador de Azure Cosmos DB disponible para su descarga desde https://aka.ms/cosmosdb-emulator.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Corrección de errores para el diccionario de agregados.
* Corrección de errores para recortar las barras diagonales en el vínculo de recursos.
* Se han agregado pruebas para la codificación Unicode.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Se agregó compatibilidad con un nuevo nivel de coherencia denominado ConsistentPrefix.


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Se agregó compatibilidad con consultas de agregación (COUNT, MIN, MAX, SUM y AVG).
* Se agregó una opción para deshabilitar la comprobación de SSL cuando se ejecuta en el emulador de Cosmos DB.
* Se ha quitado la restricción del módulo de solicitudes dependientes para que sea exactamente 2.10.0.
* Reducción del procesamiento mínimo en las colecciones particionadas de 10 100 RU/s a 2500 RU/s.
* Se ha agregado compatibilidad para habilitar el registro de scripts durante la ejecución de procedimientos almacenados.
* La versión de API de REST se incrementó a "2017-01-19" con esta versión.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Se realizan los cambios editoriales de los comentarios de documentación.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Compatibilidad agregada para Python 3.5.
* Compatibilidad agregada para agrupaciones de conexiones con un módulo de solicitudes.
* Compatibilidad agregada para la coherencia de la sesión.
* Compatibilidad agregada para consultas TOP/ORDERBY para colecciones particionadas.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Se ha agregado compatibilidad de la directiva de reintentos con las solicitudes de limitación. (Las solicitudes limitadas reciben una excepción demasiado grande de la tasa de solicitudes, código de error 429). De manera predeterminada, Azure Cosmos DB realiza nueve reintentos para cada solicitud cuando aparece el código de error 429, respetando el tiempo de retryAfter en el encabezado de respuesta. Ahora puede establecerse un tiempo del intervalo de reintento fijo como parte de la propiedad RetryOptions del objeto ConnectionPolicy si quiere ignorar el tiempo de retryAfter que ha devuelto el servidor entre los reintentos. Azure Cosmos DB espera ahora un máximo de 30 segundos para cada solicitud que se está limitando (independientemente del recuento de reintentos) y devuelve la respuesta con el código de error 429. Este tiempo también puede reemplazarse en la propiedad RetryOptions del objeto ConnectionPolicy.
* Cosmos DB ahora devuelve x-ms-throttle-retry-count y x-ms-throttle-retry-wait-time-ms como los encabezados de respuesta de cada solicitud para denotar el recuento de reintentos de limitación y el tiempo acumulativo que ha esperado la solicitud entre los reintentos.
* Se ha quitado la clase RetryPolicy y la propiedad correspondiente (retry_policy) que estaba expuesta en la clase document_client y, en su lugar, se ha introducido una clase RetryOptions que expone la propiedad RetryOptions en la clase ConnectionPolicy que puede usarse para reemplazar algunas de las opciones de reintentos predeterminadas.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Se ha agregado compatibilidad con cuentas de base de datos de varias regiones.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Se ha agregado compatibilidad con la característica de período de vida (TTL) para los documentos.

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* Correcciones de errores relacionados con la creación de particiones del lado servidor para permitir caracteres especiales en la ruta de acceso de la clave de partición.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Se han implementado [colecciones particionadas](partition-data.md) y [niveles de rendimiento definidos por el usuario](performance-levels.md). 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Se han agregado solucionadores de particiones de hash e intervalo para ayudar con el particionamiento de las aplicaciones entre varias particiones.

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* Implementación de Upsert. Se han agregado nuevos métodos upsertXXX para admitir la característica Upsert.
* Se implementa el enrutamiento por identificador. Sin cambios en la API pública, todos los cambios son internos.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Compatible con índice geoespacial.
* Valida la propiedad id para todos los recursos. Los identificadores de recursos no pueden contener los caracteres ?, /, #, \, ni terminar con un espacio.
* Agrega el nuevo encabezado "progreso de transformación de índices" a ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementación de la directiva de indexación V2.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Compatibilidad con conexión de proxy.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK de GA.

## <a name="release--retirement-dates"></a>Fechas de lanzamiento y retirada
Microsoft notifica la retirada de un SDK con al menos **12 meses** de antelación para facilitar la transición a una versión compatible o más reciente.

Solo se agregan nuevas características, funcionalidad y optimizaciones al SDK actual, por lo que se recomienda actualizar siempre a la última versión del SDK tan pronto como sea posible. 

El servicio rechazará cualquier solicitud realizada a Cosmos DB mediante un SDK retirado.

> [!WARNING]
> Todas las versiones del SDK de Azure SQL para Python anteriores a la versión **1.0.0** se retiraron el **29 de febrero de 2016**. 
> 
> 

<br/>

| Versión | Fecha de lanzamiento | Fecha de retirada |
| --- | --- | --- |
| [2.3.1](#2.3.1) |21 de diciembre de 2017 |--- |
| [2.3.0](#2.3.0) |10 de noviembre de 2017 |--- |
| [2.2.1](#2.2.1) |29 de septiembre de 2017 |--- |
| [2.2.0](#2.2.0) |10 de mayo de 2017 |--- |
| [2.1.0](#2.1.0) |1 de mayo de 2017 |--- |
| [2.0.1](#2.0.1) |30 de octubre de 2016 |--- |
| [2.0.0](#2.0.0) |29 de septiembre de 2016 |--- |
| [1.9.0](#1.9.0) |7 de julio de 2016 |--- |
| [1.8.0](#1.8.0) |14 de junio de 2016 |--- |
| [1.7.0](#1.7.0) |26 de abril de 2016 |--- |
| [1.6.1](#1.6.1) |08 de abril de 2016 |--- |
| [1.6.0](#1.6.0) |29 de marzo de 2016 |--- |
| [1.5.0](#1.5.0) |3 de enero de 2016 |--- |
| [1.4.2](#1.4.2) |6 de octubre de 2015 |--- |
| [1.4.1](#1.4.1) |6 de octubre de 2015 |--- |
| [1.2.0](#1.2.0) |6 de agosto de 2015 |--- |
| [1.1.0](#1.1.0) |09 de julio de 2015 |--- |
| [1.0.1](#1.0.1) |25 de mayo de 2015 |--- |
| [1.0.0](#1.0.0) |07 de abril de 2015 |--- |
| 0.9.4-prelease |14 de enero de 2015 |29 de febrero de 2016 |
| 0.9.3-prelease |9 de diciembre de 2014 |29 de febrero de 2016 |
| 0.9.2-prelease |25 de noviembre de 2014 |29 de febrero de 2016 |
| 0.9.1-prelease |23 de septiembre de 2014 |29 de febrero de 2016 |
| 0.9.0-prelease |21 de agosto de 2014 |29 de febrero de 2016 |

## <a name="faq"></a>Preguntas más frecuentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Otras referencias
Para más información sobre Cosmos DB, consulte la página del servicio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

