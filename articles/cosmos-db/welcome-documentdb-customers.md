---
title: 'Clientes de DocumentDB: bienvenidos a Azure Cosmos DB | Microsoft Docs'
description: "Más información sobre el anuncio realizado en //build 2017, en el que se anunciaba que los clientes de DocumentDB son ahora clientes de Azure Cosmos DB."
services: cosmosdb
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmosdb
ms.devlang: n/a
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 05/12/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: f60c3502ed51424647088fa8109f13cabc4e73da
ms.contentlocale: es-es
ms.lasthandoff: 05/17/2017

---

# <a name="welcome-documentdb-customers-to-azure-cosmos-db"></a>Clientes de DocumentDB: bienvenidos a Azure Cosmos DB

Clientes de DocumentDB, nos complace anunciarles que ya forman parte de la familia de Azure Cosmos DB. 

Azure Cosmos DB, [anunciado en la reciente conferencia de Microsoft Build 2017](https://azure.microsoft.com/blog/azure-cosmos-db-microsofts-globally-distributed-multi-model-database-service/), es el primer servicio de bases de datos distribuidas globalmente que le permite escalar elásticamente el rendimiento y el almacenamiento para cualquier número de regiones geográficas al tiempo que garantiza una baja latencia, alta disponibilidad y coherencia respaldadas por los Acuerdos de Nivel de Servicio más completos del sector y compatible con multitud de modelos y API de datos. 

![Azure Cosmos DB es el servicio de base de datos de distribución global de Microsoft con escalado elástico horizontal, baja latencia garantizada, cinco modelos de coherencia y los SLA más completos garantizados.](./media/welcome-documentdb-customers/azure-cosmos-db.png)

Una de las API que admite Azure Cosmos DB es la API de DocumentDB y el modelo de datos de documento. Ya está familiarizado con las API de DocumentDB. Son las API que ya está usando para ejecutar las aplicaciones de DocumentDB actuales. _No_ hay cambios en estas API: el paquete NuGet, los espacios de nombres y todas las dependencias siguen igual. **No es necesario cambiar nada** para continuar ejecutando las aplicaciones compiladas con la API de DocumentDB. Azure Cosmos DB es solo el nombre del servicio del que las aplicaciones forman parte. 

Analicemos algunas preguntas que es posible que le surjan. 

## <a name="why-move-to-azure-cosmos-db"></a>¿Por qué cambiar a Azure Cosmos DB? 

Azure Cosmos DB supone el próximo salto enorme en relación a las bases de datos distribuidas globalmente, a escala y en la nube. Como cliente de DocumentDB, ahora tiene acceso al sistema y a las funcionalidades novedosas y excepcionales ofrecidas por Azure Cosmos DB.

Azure Cosmos DB se lanzó en 2010 como “Proyecto Florencia” para abordar los problemas de desarrollo que aparecen en las aplicaciones a gran escala de Microsoft. Al observar que los desafíos en la compilación de aplicaciones distribuidas globalmente no eran un problema exclusivo de Microsoft, en 2015 creamos la primera generación de esta tecnología disponible para los desarrolladores de Azure. Este servicio se llamó Azure DocumentDB. 

Desde ese momento, hemos agregado nuevas características e incorporado nuevas funcionalidades significativas.  Azure Cosmos DB es el resultado.  Como parte de esta versión de Azure Cosmos DB, los clientes de DocumentDB (con sus datos) se convierten automáticamente en clientes de Azure Cosmos DB. La transición es directa y ya tiene acceso al sistema y a las funcionalidades novedosas y excepcionales ofrecidas por Azure Cosmos DB. Estas funcionalidades pertenecen a las áreas de los motores de bases de datos principales así como a la distribución global, escalabilidad elástica y a unos Acuerdos de Nivel de Servicio completos y líderes del sector. En concreto, hemos desarrollado el motor de base de datos de Azure Cosmos DB para poder asignar eficazmente todos los modelos de datos, sistemas de tipos y API más conocidos al modelo de datos subyacente de Azure Cosmos DB. 

La manifestación actual de desarrollo más conocida de este trabajo es la nueva compatibilidad con [Gremlin](graph-introduction.md) y [Table Storage API](table-introduction.md). Y esto es solo el principio… Se agregarán otras conocidas API y modelos de datos más recientes con el tiempo y se incorporarán nuevos avances hacia el rendimiento y el almacenamiento a escala global. 

Es importante señalar que el [dialecto de SQL](../documentdb/documentdb-sql-query.md) de DocumentDB siempre fue una de las varias API que la instancia subyacente de Cosmos DB podía admitir. Como desarrollador que usa un servicio completamente administrado como Azure Cosmos DB, la única interfaz del servicio son las API expuestas por este. Por lo que respecta al usuario, nada cambia realmente en comparación a cuando era cliente de DocumentDB. Azure Cosmos DB ofrece exactamente la misma API de SQL que DocumentDB. Sin embargo, ahora (y en el futuro) ya puede acceder a otras funcionalidades a las que antes no podía. 

Otra prueba de nuestro trabajo continuo es la base extendida para la escalabilidad global y flexible del rendimiento y almacenamiento. Uno de sus primeros frutos es la característica [RU/m](request-units-per-minute.md) pero tenemos más funcionalidades que se anunciarán próximamente en estas áreas. Estas nuevas funcionalidades ayudan a reducir los costos para nuestros clientes para diversas cargas de trabajo. Hemos realizado varias mejoras fundamentales en el subsistema de distribución global. Una de las múltiples manifestaciones de desarrollo de este trabajo son los modelos de coherencia de prefijos (que constituyen 5 modelos de coherencia bien definidos). Sin embargo, hay muchas más funcionalidades interesantes de las cuales se informará a medida que vayan tomando forma. 

## <a name="what-do-i-need-to-do-to-ensure-my-documentdb-resources-continue-to-run-on-azure-cosmos-db"></a>¿Qué hay que hacer para asegurarse de que los recursos de DocumentDB continúan ejecutándose en Azure Cosmos DB?

Nada. No es necesario realizar ningún cambio. Los recursos de DocumentDB son ahora recursos de Azure Cosmos DB y no se produjo ninguna interrupción del servicio cuando se produjo este cambio.

## <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>¿Qué cambios hay que hacer para que mi aplicación funcione con Azure Cosmos DB?

No es necesario realizar ningún cambio. Las clases, los espacios de nombres y los paquetes NuGet no han cambiado. Como siempre, es recomendable mantener el SDK actualizado para aprovechar las características y mejoras más recientes. 

## <a name="whats-changed-in-the-azure-portal"></a>¿Qué ha cambiado en Azure Portal?

Azure DocumentDB ya no aparece en el portal como servicio de Azure. En su lugar, aparece Azure Cosmos DB con un icono nuevo, tal como se muestra en la siguiente imagen. Todas las colecciones están disponibles tal y como se encontraban antes y aún puede escalar el rendimiento, cambiar la coherencia y supervisar los Acuerdos de Nivel de Servicio. Se han mejorado las funcionalidades del **Explorador de datos (versión preliminar)**. Ya puede ver y editar documentos, crear y ejecutar consultas y trabajar con procedimientos, desencadenadores y UDF almacenados desde una sola página, como se muestra en la siguiente imagen. 

![Visualización y copia de una clave de acceso en Azure Portal, hoja Claves](./media/welcome-documentdb-customers/cosmos-db-data-explorer.png)

## <a name="are-there-changes-to-pricing"></a>¿Hay algún cambio en el precio?

No, el costo de ejecutar la aplicación en Azure Cosmos DB es el mismo que antes. Sin embargo, puede beneficiarse de la nueva **característica de unidad de solicitud por minuto** que ya se había anunciado. Para más información, consulte el artículo [Escalado de rendimiento por minuto](request-units-per-minute.md).

## <a name="are-there-changes-to-the-service-level-agreements-slas"></a>¿Hay algún cambio en los Acuerdos de Nivel de Servicio (SLA)?

No, los Acuerdos de Nivel de Servicio relacionados con la disponibilidad, coherencia, latencia y rendimiento permanecen igual y aún aparecen en el portal. Para más información acerca de estos acuerdos, consulte el [Acuerdo de Nivel de Servicio para Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/).
   
![Aplicación de tareas pendientes con datos de ejemplo](./media/welcome-documentdb-customers/azure-cosmosdb-portal-metrics-slas.png)

## <a name="whats-next-with-azure-cosmos-db"></a>¿Cuál es el futuro de Azure Cosmos DB?

Azure Cosmos DB es un servicio de bases de datos en continua evolución. Todas las nuevas funcionalidades se validan en aplicaciones a gran escala de Microsoft, posteriormente se exponen a clientes externos clave y, por último, se hacen públicas. 

La nueva compatibilidad con Gremlin y Table API es solo el comienzo de los modelos de datos y las API habituales que Azure Cosmos DB admitirá y queremos compartir nuevos modelos de datos a medida que vayan surgiendo. También se realizarán grandes progresos en rendimiento y almacenamiento a escala global. 

Como siempre, le agradecemos que sea cliente de Azure DocumentDB y deseamos compartir Azure Cosmos DB con todos: desarrolladores y clientes de todo el mundo. Nuestro objetivo es convertirnos en el servicio de base de datos más confiable del mundo y permitirle compilar increíbles aplicaciones a escala "cósmica" de una forma más fácil. Pruebe las nuevas funcionalidades de Azure Cosmos DB y envíenos sus comentarios.  Nos interesa mucho conocer qué aplicaciones compiló con Cosmos DB.

Sus amigos de Azure Cosmos DB [@AzureCosmosDB](https://twitter.com/AzureCosmosDB)

