---
title: "Introducción a la API Cassandra de Azure Cosmos DB | Microsoft Docs"
description: "Obtenga información sobre cómo puede usar Azure Cosmos DB en aplicaciones existentes del tipo migrar mediante lift-and-shift y crear nuevas aplicaciones con la API Cassandra mediante los controladores de Cassandra y CQL con los que ya está familiarizado."
services: cosmos-db
author: govindk
manager: jhubbard
documentationcenter: 
ms.assetid: 73839abf-5af5-4ae0-a852-0f4159bc00a0
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: govindk
ms.openlocfilehash: fe911b69278021262f1d90f2157bb78ca48db0db
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2017
---
# <a name="introduction-to-apache-cassandra-api-for-azure-cosmos-db"></a>Introducción a la API Apache Cassandra para Azure Cosmos DB

Azure Cosmos DB proporciona la API Cassandra (versión preliminar) para aplicaciones escritas para Apache Cassandra que necesitan funcionalidades premium como:

* [Tamaño de almacenamiento y rendimiento escalables](partition-data.md).
* [Distribución global llave en mano](distribute-data-globally.md)
* Latencias en milisegundos de un solo dígito en el percentil 99.
* [Cinco niveles de coherencia bien definidos](consistency-levels.md)
* [Indexación automática de los datos](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sin que haya que ocuparse de la administración de esquemas ni de índices. 
* Alta disponibilidad garantizada, respaldada de forma íntegra por [Acuerdos de Nivel de Servicio líderes del sector](https://azure.microsoft.com/support/legal/sla/cosmos-db/)

## <a name="what-is-the-azure-cosmos-db-apache-cassandra-api"></a>¿Qué es la API Apache Cassandra de Azure Cosmos DB?

Azure Cosmos DB se puede usar como almacén de datos para aplicaciones escritas para [Apache Cassandra](https://cassandra.apache.org/), mediante la API Apache Cassandra. Esto significa que, gracias al uso de los [controladores con licencia Apache compatibles con CQLv4](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) existentes, su aplicación escrita para Cassandra ahora se puede comunicar con la API Cassandra de Azure Cosmos DB. En muchos casos, puede pasar de usar Apache Cassandra a la API Apache Cassandra de Azure Cosmos DB, cambiando únicamente una cadena de conexión. Mediante esta funcionalidad, se pueden crear y ejecutar fácilmente aplicaciones de base de datos de API Cassandra en la nube de Azure con la distribución global de Azure Cosmos DB y los [completos Acuerdos de Nivel de Servicio líderes del sector](https://azure.microsoft.com/support/legal/sla/cosmos-db), al tiempo que se siguen usando conocimientos y herramientas conocidos para la API Cassandra.

![API Cassandra de Azure Cosmos DB](./media/cassandra-introduction/cosmosdb-cassandra.png)

La API Cassandra le permite interactuar con los datos almacenados en Azure Cosmos DB mediante las herramientas basadas en lenguaje de consulta de Cassandra (p. ej., CQLSH) y los controladores cliente de Cassandra con los que ya está familiarizado. Obtenga más información en este vídeo de Microsoft Mechanics que presenta el administrador principal del departamento de ingeniería Kirill Gavrylyuk.

> [!VIDEO https://www.youtube.com/embed/1Sf4McGN1AQ]
>

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>¿Cuál es la ventaja de usar la API Apache Cassandra para Azure Cosmos DB?

**Ausencia de administración de operaciones**: como auténtico servicio completamente administrado, Azure Cosmos DB garantiza a los administradores de la API Cassandra no tener que preocuparse de la administración y supervisión de innumerables configuraciones en el sistema operativo, JVM y su interacción. Azure Cosmos DB proporciona la supervisión del rendimiento, la latencia, el almacenamiento y la disponibilidad, así como alertas configurables. 

**Administración del rendimiento**: Azure Cosmos DB proporciona lecturas y escrituras de baja latencia respaldadas por el Acuerdo de Nivel de Servicio para el percentil 99. Los usuarios no deben preocuparse de que la sobrecarga operativa sea muy elevada para proporcionar Acuerdos de Nivel de Servicio de lectura y escritura óptimos. En ellos suelen incluirse la programación de la compactación, la administración de marcadores de exclusión, la configuración de filtros de bloom y retrasos de réplica. Azure Cosmos DB hace desaparecer la preocupación de administrar estos problemas y le permite centrarse en los resultados de la aplicación.

**Indexación automática**: Azure Cosmos DB indexa automáticamente todas las columnas de la tabla en la base de datos de API Cassandra. Azure Cosmos DB no requiere la creación de índices secundarios para acelerar las consultas. Proporciona una experiencia de lectura y escritura de baja latencia al mismo tiempo que se realiza una indexación automática coherente. 

**Capacidad para usar código y herramientas existentes**: Azure Cosmos DB proporciona compatibilidad de nivel de protocolo de conexión con SDK y herramientas existentes. Esta compatibilidad garantiza que pueda usar su código base existente con la API Cassandra de Azure Cosmos DB con cambios triviales.

**Elasticidad de almacenamiento y rendimiento**: la plataforma Azure Cosmos proporciona elasticidad de rendimiento garantizado en las regiones a través de operaciones de la CLI, de PowerShell o del portal simples. Según vaya creciendo su aplicación, puede escalar tablas de Azure Cosmos DB de manera flexible con un rendimiento predecible impecable. Azure Cosmos DB admite tablas de API Cassandra que pueden escalarse a tamaños de almacenamiento prácticamente ilimitado. 

**Disponibilidad y distribución global**: Azure Cosmos DB permite distribuir datos en todas las regiones de Azure para ofrecer a los usuarios una experiencia de baja latencia al mismo tiempo que se garantiza su disponibilidad. Azure Cosmos DB proporciona una disponibilidad del 99,99 % dentro de una región y una disponibilidad de lectura del 99,999 % en las regiones sin sobrecarga de operaciones. Azure Cosmos DB está disponible en más de 30 [regiones de Azure](https://azure.microsoft.com/regions/services/). Obtenga más información en [Distribución de datos global con DocumentDB](distribute-data-globally.md). 

**Opción de coherencia**: Azure Cosmos DB proporciona la opción de cinco niveles de coherencia bien definidos para lograr un equilibrio óptimo entre la coherencia y el rendimiento. Estos niveles de coherencia son Alta, Obsolescencia limitada, Sesión, Prefijo coherente y Posible. Estos niveles de coherencia bien definidos y pormenorizados permiten al desarrollador realizar equilibrios profundos entre la coherencia, la disponibilidad y la latencia. Más información en el artículo sobre el [Uso de los niveles de coherencia para maximizar la disponibilidad y el rendimiento](consistency-levels.md). 

**Clase empresarial**: seguro y compatible de forma predeterminada, Azure Cosmos DB proporciona [certificaciones de cumplimiento](https://www.microsoft.com/trustcenter) para garantizar que los usuarios puedan utilizar la plataforma sin preocuparse sobre problemas de cumplimiento. Azure Cosmos DB también proporciona cifrado en reposo y en movimiento, firewall de IP y registros de auditoría para actividades del plano de control.  

<a id="sign-up-now"></a>
## <a name="sign-up-now"></a>Regístrese ahora 

Si ya tiene una suscripción de Azure, puede registrarse para formar parte del programa Cassandra API (versión preliminar) en [Azure Portal](https://aka.ms/cosmosdb-cassandra-signup).  Si es nuevo en Azure, regístrese para conseguir una [evaluación gratuita](https://azure.microsoft.com/free) con la que disfrutará de 12 meses de acceso gratuito a Azure Cosmos DB. Complete los pasos siguientes para solicitar acceso al programa Cassandra API (versión preliminar).

1. En [Azure Portal](https://portal.azure.com), haga clic en **Nuevo** > **Bases de datos** > **Azure Cosmos DB**. 

2. En la página Cuenta nueva, seleccione **Cassandra** en el cuadro de API. 

3. En el cuadro **Suscripción**, seleccione la suscripción de Azure que quiera utilizar para la cuenta.

4. Haga clic en **Regístrese hoy para obtener la versión preliminar**.

    ![API Cassandra de Azure Cosmos DB](./media/cassandra-introduction/cassandra-sign-up.png)

3. En el panel "Regístrese hoy para obtener la versión preliminar", haga clic en **Aceptar**. 

    Una vez enviada la solicitud, el estado cambia a **Pendiente de aprobación** en el panel de la cuenta nueva. 

Después de enviar la solicitud, espere a recibir un correo electrónico donde se indique que se ha aprobado. Debido al elevado volumen de solicitudes, es posible que reciba la notificación en el plazo de una semana. No es necesario crear una incidencia de soporte técnico para completar la solicitud. Las solicitudes se revisarán según el orden de llegada. 

## <a name="how-to-get-started"></a>Primeros pasos
Una vez se haya unido al programa de versión preliminar, siga los pasos de inicio rápido de Cassandra API para usarla para crear una aplicación.

* [Guía de inicio rápido: compilación de una aplicación web Cassandra con Node.js y Azure Cosmos DB](create-cassandra-nodejs.md)
* [Guía de inicio rápido: compilación de una aplicación web Cassandra con Java y Azure Cosmos DB](create-cassandra-java.md)
* [Guía de inicio rápido: compilación de una aplicación web Cassandra con .NET y Azure Cosmos DB](create-cassandra-dotnet.md)
* [Guía de inicio rápido: compilación de una aplicación web Cassandra con Python y Azure Cosmos DB](create-cassandra-python.md)

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo la API Cassandra de Azure Cosmos DB se integra en la documentación de Azure Cosmos DB global, pero hay algunos indicadores para empezar:

* Siga las [guías de inicio rápido](create-cassandra-nodejs.md) para crear una cuenta y una nueva aplicación mediante un ejemplo de Git.
* Siga el [tutorial](tutorial-develop-cassandra-java.md) para crear una nueva aplicación mediante programación.
* Siga el [tutorial de importación de datos de Cassandra](cassandra-import-data.md) para importar sus datos existentes en Azure Cosmos DB.
* Lea las [preguntas más frecuentes](faq.md#cassandra).
