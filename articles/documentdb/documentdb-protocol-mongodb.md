---
title: Soporte de protocolo de DocumentDB para MongoDB | Microsoft Docs
description: Obtenga información acerca del soporte de protocolo de DocumentDB para MongoDB, ahora disponible para vista previa pública.
keywords: mongodb
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: ''
documentationcenter: ''

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2016
ms.author: anhoh

---
# <a name="documentdb-protocol-support-for-mongodb"></a>Compatibilidad de protocolo de DocumentDB con MongoDB
## <a name="what-is-azure-documentdb?"></a>¿Qué es la Base de datos de documentos de Azure?
Azure DocumentDB es un servicio totalmente administrado de base de datos NoSQL compilado para obtener un rendimiento rápido y predecible, alta disponibilidad, escalado automático y facilidad de desarrollo. Su modelo de datos flexible, bajas latencias continuadas y excelentes funcionalidades de consulta hacen que sea una gran elección para web, móvil, juegos, IoT y para muchas otras aplicaciones que necesitan un escalado impecable. Obtenga más información en [Introducción a DocumentDB: una base de datos de JSON NoSQL](documentdb-introduction.md).

## <a name="what-is-documentdb-protocol-support-for-mongodb?"></a>¿Qué es el soporte de protocolo de DocumentDB para MongoDB?
Las bases de datos de DocumentDB se pueden utilizar ahora como almacenes de datos para aplicaciones escritas para MongoDB. Mediante los [controladores](https://docs.mongodb.org/ecosystem/drivers/) existentes para MongoDB, las aplicaciones se pueden comunicar fácilmente y de forma transparente con DocumentDB, en muchos casos con solo cambiar una cadena de conexión.  Mediante esta funcionalidad de vista previa, los clientes pueden fácilmente compilar y ejecutar aplicaciones en la nube de Azure, aprovechando las bases de datos NoSQL totalmente escalables y administradas de DocumentDB, mientras continúa usando habilidades y herramientas conocidas para MongoDB.

El soporte de protocolo de DocumentDB para MongoDB habilita las funciones principales de la API de MongoDB para realizar acciones de creación, lectura, actualización y eliminación (CRUD) de datos así como de consulta de la base de datos. Las funcionalidades implementadas actualmente se han priorizado según las necesidades de plataformas, marcos y herramientas comunes y según los clientes a gran escala de MongoDB que han evaluado Azure para su plataforma en la nube.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información acerca de cómo [crear](documentdb-create-mongodb-account.md) una cuenta de DocumentDB con soporte de protocolo para MongoDB.
* Obtenga información acerca de cómo [conectar](documentdb-connect-mongodb-account.md) una cuenta de DocumentDB con soporte de protocolo para MongoDB.
* Obtenga información acerca de cómo [usar MongoChef](documentdb-mongodb-mongochef.md) con una cuenta de DocumentDB con compatibilidad de protocolo con MongoDB.
* Explore DocumentDB con soporte de protocolo para buscar [ejemplos](documentdb-mongodb-samples.md)de MongoDB.

<!--HONumber=Oct16_HO2-->


