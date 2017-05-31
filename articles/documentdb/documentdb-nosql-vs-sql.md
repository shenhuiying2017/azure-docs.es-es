---
title: NoSQL comparado con SQL y Azure Cosmos DB | Microsoft Docs
description: "Compare las ventajas de usar las soluciones NoSQL no relacionales frente a soluciones de SQL y Azure Cosmos DB. Obtenga información sobre cómo Azure Cosmos DB proporciona las ventajas de NoSQL y SQL."
keywords: "nosql vs sql, cuándo usar NoSQL, sql frente a nosql"
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 71ef1798-d709-4ccb-9f5c-57948fb96229
ms.service: cosmosdb
ms.custom: overview
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2017
ms.author: mimig
redirect_url: https://aka.ms/cosmosdb
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 9062bbc9b7ae2f3f38bf55d3e37986238a145819
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---
# <a name="nosql-vs-sql-and-azure-cosmos-db"></a>NoSQL comparado con SQL y Azure Cosmos DB
SQL Server y bases de datos relacionales (RDBMS) han sido las bases de datos de referencia durante más de 20 años. Pero la creciente necesidad de procesar más volúmenes, velocidades y tipos de datos con una tasa rápida ha modificado la naturaleza de las necesidades de almacenamiento de datos para los desarrolladores de aplicaciones. Para habilitar este escenario, las bases de datos NoSQL que puedan almacenar datos no estructurados y heterogéneos a escala se han hecho más populares. Para la mayoría de los desarrolladores, las bases de datos relacionales son la opción predeterminada o recomendada, ya que una estructura de tabla es fácil de comprender y resulta conocida, pero hay muchas razones para explorar más allá de las bases de datos relacionales.

NoSQL es una categoría de bases de datos diferente de las bases de datos SQL. NoSQL a menudo se usa para referirse a los sistemas de administración de datos que son "No SQL" o un enfoque de administración de datos que incluye "No solo SQL". Hay una serie de tecnologías de la categoría de NoSQL, incluidas las bases de datos de documentos, los almacenes de clave-valor, los almacenes de familia de columnas y las bases de datos de gráfico, que son populares con juegos, redes sociales y aplicaciones de IoT.

![Diagrama de información general NoSQL frente a SQL que muestra los modelos de datos y escenarios comunes](./media/documentdb-nosql-vs-sql/nosql-vs-sql-overview.png)

El objetivo de este artículo es ayudarle a aprender acerca de las diferencias entre SQL y NoSQ, y proporcionar una introducción a las ofertas de NoSQL y SQL de Microsoft.  

## <a name="when-to-use-nosql"></a>¿Cuándo usar NoSQL?
Imaginemos que está creando un nuevo sitio de participación en redes sociales. Los usuarios pueden crear publicaciones y agregar imágenes, vídeos y música en ellas. Otros usuarios pueden comentar sobre las publicaciones y dar puntos (me gustas) para calificar las publicaciones. La página de aterrizaje tendrá una fuente de entradas que los usuarios pueden compartir y con la que pueden interactuar. 

¿Cómo se almacenan estos datos? Si está familiarizado con SQL, puede comenzar a dibujar algo parecido a esto:

![Diagrama NoSQL frente a SQL que muestra el modelo de datos relacionales para un sitio de participación social](./media/documentdb-nosql-vs-sql/nosql-vs-sql-social.png)

Todo bien hasta ahora, pero ahora piense en la estructura de una sola publicación y cómo mostrarla. Si desea mostrar la publicación y las imágenes asociadas, audio, vídeo, comentarios, puntos e información de usuario en un sitio web o aplicación, tendría que realizar una consulta con ocho combinaciones de tabla para recuperar el contenido. Ahora imagínese un flujo de publicaciones que se carga dinámicamente y aparece en la pantalla, y puede predecir fácilmente que va a requerir miles de consultas y muchas combinaciones para completar la tarea.

Ahora puede usar una solución relacional como SQL Server para almacenar los datos y consultarlos con combinaciones (ya que SQL es compatible con datos dinámicos [con formato de JSON](https://msdn.microsoft.com/library/dn921897.aspx)), o bien puede usar una opción de NoSQL que simplifica el enfoque para este escenario específico. Al usar un solo documento como el siguiente y almacenarlo en Azure Cosmos DB (un servicio de base de datos de documentos NoSQL de Azure), puede aumentar el rendimiento y recuperar la publicación completa con una consulta y sin combinaciones. Es más sencillo, más directo y, por lo tanto, se ofrece un mejor rendimiento.

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

Además, estos datos pueden dividirse por Id. de publicación para permitir que los datos se escalen horizontalmente de forma natural y aprovechen las características de escala de NoSQL. Además, los sistemas NoSQL permiten a los desarrolladores reducir la coherencia y ofrecer aplicaciones de alta disponibilidad con una latencia baja.  Finalmente, esta solución no requiere que los desarrolladores definan, administren y mantengan el esquema en el nivel de datos que permite la iteración rápida.

A continuación, puede crear esta solución con otros servicios de Azure:

* [Búsqueda de Azure](https://azure.microsoft.com/services/search/) puede utilizarse a través de la aplicación web para permitir a los usuarios buscar publicaciones.
* [Servicios de aplicaciones de Azure](https://azure.microsoft.com/services/app-service/) puede usarse para hospedar aplicaciones y procesos en segundo plano.
* [Almacenamiento de blobs de Azure](https://azure.microsoft.com/services/storage/) puede utilizarse para almacenar perfiles de usuario completos, incluidas las imágenes.
* [Base de datos SQL de Azure](https://azure.microsoft.com/services/sql-database/) puede utilizarse para almacenar grandes cantidades de datos como la información de inicio de sesión y los datos de análisis de uso.
* El [Aprendizaje automático de Azure](https://azure.microsoft.com/services/machine-learning/) se puede usar para generar conocimientos e inteligencia que ofrezcan información sobre el proceso y para entregar el contenido correcto a los usuarios adecuados.

Este sitio de participación social es solo uno un escenario en el que una base de datos NoSQL es el modelo de datos adecuado para el trabajo. Si está interesado en leer más sobre este escenario y cómo modelar los datos de Azure Cosmos DB en aplicaciones de redes sociales, consulte [Redes sociales y Azure Cosmos DB](documentdb-social-media-apps.md). 

## <a name="nosql-vs-sql-comparison"></a>Comparación de NoSQL frente a SQL
La siguiente tabla compara las diferencias principales entre NoSQL y SQL. 

![Diagrama de NoSQL frente a SQL que muestra cuándo usar NoSQL y cuándo usar SQL. Comparación de SQL frente a NoSQL](./media/documentdb-nosql-vs-sql/nosql-vs-sql-comparison.png)

Si una base de datos NoSQL se adapta mejor a sus requisitos, continúe con la siguiente sección para obtener más información acerca de los servicios de NoSQL disponibles de Azure. De lo contrario, si una base de datos SQL se adapta mejor a sus necesidades, vaya a [¿Cuáles son las ofertas de Microsoft SQL?](#what-are-the-microsoft-sql-offerings)

## <a name="what-are-the-microsoft-azure-nosql-offerings"></a>¿Cuáles son las ofertas de Microsoft Azure NoSQL?
Azure ofrece cuatro servicios NoSQL totalmente administrados: 

* [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/)
* [Almacenamiento de tablas de Azure](https://azure.microsoft.com/services/storage/)
* [HBase de Azure como parte de HDInsight](https://azure.microsoft.com/services/hdinsight/)
* [Caché en Redis de Azure](https://azure.microsoft.com/services/cache/)

El siguiente gráfico de comparación se asigna a los diferenciadores clave para cada servicio. ¿Que describe con más exactitud las necesidades de su aplicación? 

![Diagrama de NoSQL frente a SQL que muestra cuándo usar ofertas NoSQL de Microsoft Azure, incluidos Azure Cosmos DB, Table Storage, HBase como parte de HDInsight y Redis Cache](./media/documentdb-nosql-vs-sql/nosql-vs-sql-documentdb-storage-hbase-hdinsight-redis-cache.png)

Si uno o varios de estos servicios pueden satisfacer las necesidades de la aplicación, aprenda más con los siguientes recursos: 

* [Casos de uso de Azure Cosmos DB](documentdb-use-cases.md)
* [Introducción al Almacenamiento de tablas de Azure](../storage/storage-dotnet-how-to-use-tables.md)
* [¿Qué es HBase en HDInsight?](../hdinsight/hdinsight-hbase-overview.md)
* [Ruta de aprendizaje de Caché en Redis](https://azure.microsoft.com/documentation/learning-paths/redis-cache/)

A continuación, vaya a [Pasos siguientes](#next-steps) para obtener información de evaluación gratuita.

## <a name="what-are-the-microsoft-sql-offerings"></a>¿Cuáles son las ofertas de Microsoft SQL?
Microsoft tiene cinco ofertas de SQL: 

* [Base de datos SQL de Azure](https://azure.microsoft.com/services/sql-database/)
* [SQL Server en Máquinas virtuales de Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/)
* [SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-2016/)
* [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/)
* [Analytics Platform System (dispositivo local)](https://www.microsoft.com/en-us/server-cloud/products/analytics-platform-system/)

Si está interesado en SQL Server en una Máquina virtual o la Base de datos SQL, lea [Selección de una opción SQL Server en la nube: Base de datos (PaaS) SQL de Azure o SQL Server en máquinas virtuales de Azure (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md) para obtener más información sobre las diferencias entre los dos.

Si SQL suena como la mejor opción, vaya a [SQL Server](https://www.microsoft.com/server-cloud/products/) para obtener más información acerca de lo que nuestros productos de Microsoft SQL y servicios tienen que ofrecer.

A continuación, vaya a [Pasos siguientes](#next-steps) para obtener vínculos de evaluación y evaluación gratuita.

## <a name="next-steps"></a>Pasos siguientes
Le invitamos a obtener más información acerca de nuestros productos SQL y NoSQL probándolos de forma gratuita. 

* Para todos los servicios de Azure, puede registrarse para una [prueba de un mes gratis](https://azure.microsoft.com/pricing/free-trial/) y recibir 200 USD para utilizarlos en cualquiera de los servicios de Azure.
  
  * [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/)
  * [HBase de Azure como parte de HDInsight](https://azure.microsoft.com/services/hdinsight/)
  * [Caché en Redis de Azure](https://azure.microsoft.com/services/cache/)
  * [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/)
  * [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
  * [Almacenamiento de tablas de Azure](https://azure.microsoft.com/services/storage/)
* Puede poner en marcha una [versión de evaluación de SQL Server 2016 en una máquina virtual](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016ctp33evaluationwindowsserver2012r2/) o descargar una [versión de evaluación de SQL Server](https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016).
  
  * [SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-2016/)
  * [SQL Server en Máquinas virtuales de Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/)


