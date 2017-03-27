---
title: "¿Qué es DocumentDB: API para MongoDB? | Microsoft Docs"
description: "Obtenga información sobre DocumentDB: API de MongoDB y cómo se pueden ejecutar fácilmente aplicaciones existentes de MongoDB en la nube de Azure"
keywords: "Qué es MongoDB"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: f173fa709f2a7a21042752ba4b5ac936d01fe300
ms.lasthandoff: 03/15/2017


---
# <a name="what-is-documentdb-api-for-mongodb"></a>¿Qué es DocumentDB: API para MongoDB?

Las bases de datos de DocumentDB se pueden utilizar ahora como almacenes de datos para aplicaciones escritas para MongoDB. Esto significa que mediante [controladores](https://docs.mongodb.org/ecosystem/drivers/) existentes para bases de datos de MongoDB, la aplicación escrita para MongoDB se puede comunicar ahora con DocumentDB y usar bases de datos de DocumentDB en lugar de bases de datos de MongoDB. En muchos casos, puede cambiar del uso de MongoDB a DocumentDB con solo cambiar una cadena de conexión. Mediante esta funcionalidad, los clientes pueden crear y ejecutar fácilmente aplicaciones de base de datos de MongoDB en la nube de Azure, y aprovechar las bases de datos NoSQL totalmente escalables y administradas de DocumentDB, al tiempo que se siguen usando conocimientos y herramientas conocidos para MongoDB.

## <a name="what-is-the-benefit-of-using-documentdb-api-for-mongodb"></a>¿Cuál es la ventaja de usar DocumentDB: API para MongoDB?
**Sin administración del servidor**: DocumentDB es un servicio completamente administrado, lo que significa que no tiene que administrar ninguna infraestructura o máquina virtual por su cuenta. DocumentDB está disponible en más de 30 [regiones de Azure](https://azure.microsoft.com/regions/services/).

**Escala sin límites**: puede escalar la capacidad de proceso y el almacenamiento de manera independiente y elástica. Puede agregar capacidad para atender millones de solicitudes por segundo con facilidad.

**Nivel empresarial**: DocumentDB admite varias réplicas locales para ofrecer protección de datos y disponibilidad del 99,99 % frente a errores locales y regionales. DocumentDB presenta [certificaciones de conformidad](https://www.microsoft.com/trustcenter) y características de seguridad de nivel empresarial. 

**Compatibilidad con MongoDB**: DocumentDB: API para MongoDB está diseñada para la compatibilidad con MongoDB. Puede usar su código, sus aplicaciones, sus controladores y sus herramientas existentes para trabajar con DocumentDB. 

Aprenda más en este vídeo de Azure Friday con Scott Hanselman y Kirill Gavrylyuk, Administrador de ingeniería principal de Azure DocumentDB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/DocumentDB-Database-as-a-Service-for-MongoDB-Developers/player]
> 


## <a name="how-to-get-started"></a>Primeros pasos
Cree una cuenta de DocumentDB: API para MongoDB en [Azure Portal](https://portal.azure.com) y cambie la conexión a la nueva cuenta. 

*Y ya está.*

Para obtener instrucciones más detalladas, siga los pasos para [crear una cuenta](documentdb-create-mongodb-account.md) y [conectar con su cuenta](documentdb-connect-mongodb-account.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre DocumentDB: la API de MongoDB se integra en la documentación de DocumentDB global, pero hay algunos indicadores para empezar:
* Para saber cómo obtener la información de la cadena de conexión de la cuenta, siga los pasos del tutorial [Conexión a una cuenta de MongoDB](documentdb-connect-mongodb-account.md).
* Para obtener información sobre cómo crear una conexión entre la base de datos de DocumentDB y la aplicación de MongoDB en MongoChef, siga los pasos del tutorial [Uso de MongoChef con DocumentDB](documentdb-mongodb-mongochef.md).
* Siga el tutorial [Migración de datos a una cuenta de DocumentDB compatible con los protocolos de MongoDB](documentdb-mongodb-migrate.md) para importar los datos a una base de datos de la API de MongoDB.
* Compile su primera aplicación de API de MongoDB con [Node.js](documentdb-mongodb-samples.md).
* Compile su primera aplicación web de API de MongoDB con .[NET](documentdb-mongodb-application.md).
* Para conectarse a una cuenta de la API de MongoDB con [Robomongo](documentdb-mongodb-robomongo.md).
* Obtenga información sobre cuántas RU utilizan las operaciones con el [comando GetLastRequestStatistics y las métricas de Azure Portal](documentdb-request-units.md#GetLastRequestStatistics).
* Obtenga información sobre cómo [configurar las preferencias de lectura para las aplicaciones distribuidas globalmente](documentdb-distribute-data-globally.md#ReadPreferencesAPIforMongoDB).


