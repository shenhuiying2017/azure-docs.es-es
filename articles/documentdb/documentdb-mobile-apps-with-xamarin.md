---
title: "Creación de aplicaciones móviles con Xamarin y Azure DocumentDB | Microsoft Docs"
description: "Tutorial que permite crear una aplicación de Xamarin iOS, Android o Windows Forms mediante Azure DocumentDB. DocumentDB es una base de datos de nube a escala mundial increíblemente rápida para aplicaciones móviles."
services: documentdb
documentationcenter: .net
author: arramac
manager: monicar
editor: 
ms.assetid: ff97881a-b41a-499d-b7ab-4f394df0e153
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 4a783397b038b7545bbbfc45813db3d9973c5e19
ms.lasthandoff: 03/22/2017


---
# <a name="build-mobile-applications-with-xamarin-and-azure-documentdb"></a>Creación de aplicaciones móviles con Xamarin y Azure DocumentDB
La mayoría de las aplicaciones móviles necesitan almacenar los datos en la nube, y Azure DocumentDB es una base de datos de nube para aplicaciones móviles. Tiene todo lo que un desarrollador de aplicaciones móviles puede necesitar. Es una base de datos como servicio NoSQL completamente administrada que se escala a petición, que puede llevarle sus datos allí donde vayan sus usuarios en todo el mundo, de forma transparente para la aplicación. Mediante el [SDK de .NET Core para Azure DocumentDB ](documentdb-sdk-dotnet-core.md), puede permitir que las aplicaciones móviles de Xamarin interactúen directamente con DocumentDB, sin una capa intermedia.

En este artículo, se proporciona un tutorial para crear aplicaciones móviles con Xamarin y DocumentDB. Puede encontrar el código fuente completo del tutorial en [Xamarin y DocumentDB en Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin), además de información sobre cómo administrar usuarios y permisos.

## <a name="documentdb-capabilities-for-mobile-apps"></a>Funcionalidades de DocumentDB para aplicaciones móviles
DocumentDB proporciona las siguientes funcionalidades clave para los desarrolladores de aplicaciones móviles:

![Funcionalidades de DocumentDB para aplicaciones móviles](media/documentdb-mobile-apps-with-xamarin/documentdb-for-mobile.png)

* Consultas completas sobre datos sin esquema. DocumentDB almacena los datos como documentos JSON sin esquema en colecciones heterogéneas, y ofrece [consultas completas y rápidas](documentdb-sql-query.md) sin necesidad de preocuparse por esquemas o índices.
* Rápida. Garantizada. Solo se tardan unas milésimas de segundos en leer y escribir documentos con DocumentDB. Los desarrolladores pueden especificar el rendimiento que necesitan y DocumentDB lo respeta con Acuerdos de Nivel de Servicio (SLA) del 99,99 %.
* Escala ilimitada. Las colecciones de DocumentDB [crecen a medida que la aplicación crece](documentdb-partition-data.md). Puede comenzar con un tamaño pequeño de datos y 100s solicitudes por segundo y crecer hasta un rendimiento arbitrariamente grande de 10s y 100s millones de solicitudes por segundo y petabytes de datos.
* Distribución global. Los usuarios de aplicaciones móviles están moviéndose de un lado para otro, con frecuencia por todo el mundo. DocumentDB es una [base de datos distribuida globalmente](documentdb-distribute-data-globally.md), y con un solo clic en un mapa llevará los datos allí donde estén los usuarios.
* Autorización completa integrada. Con DocumentDB, puede implementar fácilmente patrones conocidos como [datos por usuario](https://aka.ms/documentdb-xamarin-todouser) o datos compartidos por varios usuarios sin código de autorización personalizado complejo.
* Consultas geoespaciales. Muchas aplicaciones móviles ofrecen en la actualidad experiencias en contexto geográfico. Gracias a la compatibilidad de primera clase con [tipos geoespaciales](documentdb-geospatial.md), DocumentDB permite que estas experiencias sean fáciles de conseguir.
* Datos adjuntos binarios. Los datos de aplicaciones a menudo incluyen blobs binarios. La compatibilidad nativa con datos adjuntos facilita el uso de DocumentDB como tienda integral para los datos de las aplicaciones.

## <a name="documentdb-and-xamarin-tutorial"></a>Tutorial de DocumentDB y Xamarin
En el siguiente tutorial se muestra cómo crear una aplicación móvil con Xamarin y DocumentDB en cinco sencillos pasos. Puede encontrar el código fuente completo del tutorial en [Xamarin y DocumentDB en Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).

### <a name="get-started"></a>Primeros pasos
Con DocumentDB es fácil comenzar a trabajar. Simplemente vaya al portal de Azure, cree una nueva cuenta de DocumentDB, vaya a la pestaña Inicio rápido y descargue un ejemplo de "lista de tareas pendientes" de Xamarin Forms, ya conectado a su cuenta de DocumentDB. 

![Inicio rápido de DocumentDB para aplicaciones móviles](media/documentdb-mobile-apps-with-xamarin/documentdb-quickstart.png)

O bien, si ya tiene una aplicación de Xamarin, puede agregarla a este [paquete NuGet de DocumentDB](documentdb-sdk-dotnet-core.md). DocumentDB admite bibliotecas compartidas de Xamarin.IOS, Xamarin.Android y Xamarin Forms.

### <a name="work-with-data"></a>Trabajar con datos
Los registros de datos se almacenan en DocumentDB como documentos JSON sin esquema en colecciones heterogéneas. Puede almacenar documentos con diferentes estructuras en la misma colección.

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

En sus proyectos de Xamarin puede usar Language-Integrated Queries sobre datos sin esquema:

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>Agregar usuarios
Al igual que muchos otros ejemplos de introducción, el ejemplo de DocumentDB que ha descargado se autentica en el servicio mediante claves maestras codificadas en el código de la aplicación. Esto no es una buena idea para una aplicación que pretende ejecutar en cualquier parte, excepto en su emulador local. Si un atacante consiguiera la clave maestra, todos los datos de su cuenta de DocumentDB se verían comprometidos. Por eso queremos que nuestra aplicación solo tenga acceso a los registros del usuario que ha iniciado sesión. DocumentDB permite a los desarrolladores conceder acceso de lectura o de lectura y escritura a la aplicación a una colección, un conjunto de documentos agrupados por una clave de partición o un documento específico. 

Aquí, por ejemplo, mostramos cómo convertir nuestra aplicación de lista de tareas pendientes en una aplicación de "lista de tareas pendientes" para varios usuarios: 

* Agregue el inicio de sesión a la aplicación, mediante Facebook, Active Directory o cualquier otro proveedor.
* Cree una colección UserItems de DocumentDB con /userId como clave de partición. Al especificar la clave de partición para su colección, DocumentDB puede escalar hasta el infinito a medida que crece el número de usuarios de nuestra aplicación, al tiempo que ofrece consultas rápidas.
* Agregue Resource Token Broker de DocumentDB, una API web sencilla que autentica a los usuarios y emite tokens de corta duración a los usuarios conectados con acceso únicamente a los documentos dentro de la partición del usuario. En este ejemplo, hospedamos Resource Token Broker en App Service.
* Modifique la aplicación para autenticarse en Resource Token Broker con Facebook y solicitar los tokens de recursos para el usuario que ha iniciado sesión en Facebook, y luego acceda a los datos de los usuarios de la colección UserItems.  

Puede encontrar un ejemplo de código completo de este patrón en [Resource Token Broker en Github](http://aka.ms/documentdb-xamarin-todouser). Este diagrama ilustra la solución:

![Agente de permisos y usuarios de DocumentDB](media/documentdb-mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

Ahora, si queremos que dos usuarios obtengan acceso a la misma "lista de tareas pendientes", agregamos permisos adicionales al token de acceso en Resource Token Broker.

### <a name="scale-on-demand"></a>Escalado a petición
DocumentDB es una base de datos como servicio administrada. A medida que crece su base de usuarios, no es necesario preocuparse de aprovisionar máquinas virtuales o de aumentar los núcleos. Todo lo que tiene que hacer es indicar a DocumentDB cuántas aplicaciones por segundo (rendimiento) necesita su aplicación. Puede especificar el rendimiento mediante la pestaña Escala del portal usando una medida del rendimiento llamada Unidades de solicitud (RU) por segundo. Por ejemplo, una operación de lectura en un documento de 1 KB requiere 1 RU. Puede agregar también alertas para la métrica "Rendimiento" con el fin de supervisar el crecimiento del tráfico y cambiar mediante programación el rendimiento cuando se disparen las alertas.

![Escala del rendimiento de DocumentDB a petición](media/documentdb-mobile-apps-with-xamarin/documentdb-scale.png)

### <a name="go-planet-scale"></a>Escala a nivel mundial
A medida que su aplicación gane popularidad, puede que consiga usuarios de todo el mundo. O puede que no quiera que le pillen con la guardia baja si un meteorito se estrella contra los centros de datos de Azure donde creó su colección de DocumentDB. Vaya al portal de Azure, a su cuenta de DocumentDB, y con un clic en un mapa, sus datos se replicarán continuamente en cualquier número de regiones en todo el mundo. Esto garantiza que los datos estén disponibles siempre que lo estén los usuarios. Además, puede agregar directivas de conmutación por error para estar preparado para ese día.

![Escala de DocumentDB entre regiones geográficas](media/documentdb-mobile-apps-with-xamarin/documentdb-replicate-globally.png)

¡Enhorabuena! Ha finalizado la solución y tiene una aplicación móvil con Xamarin y DocumentDB. Se puede usar un patrón similar en aplicaciones de Cordova mediante el SDK de JavaScript para DocumentDB, y en aplicaciones nativas de iOS/Android con las API de REST de DocumentDB.

## <a name="next-steps"></a>Pasos siguientes
* Vea el código fuente de [Xamarin y DocumentDB en Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).
* Descargue el [SDK de .NET Core para DocumentDB](documentdb-sdk-dotnet-core.md).
* Encuentre más ejemplos de código para [aplicaciones .NET](documentdb-dotnet-samples.md).
* Aprenda sobre las [completas funcionalidades de consulta de DocumentDB](documentdb-sql-query.md).
* Aprenda sobre la [compatibilidad geoespacial en DocumentDB](documentdb-geospatial.md).




