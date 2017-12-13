---
title: "Creación de bases de datos escalables en la nube | Microsoft Docs"
description: "Crear aplicaciones de bases de datos de .NET escalables con la Biblioteca de cliente de Base de datos elástica"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: 1f11c52d-13c1-4994-b9b1-5b1ae2f9255f
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: ddove
ms.openlocfilehash: 0674aba66b48b26b70b3ab32d9283de5c63a267a
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="building-scalable-cloud-databases"></a>Creación de bases de datos escalables en la nube
El escalado horizontal de bases de datos puede realizarse con facilidad mediante herramientas y características escalables de Azure SQL Database. En concreto, puede usar la **Biblioteca de cliente de Elastic Database** para crear y administrar bases de datos de escalado horizontal. Esta característica permite desarrollar fácilmente aplicaciones particionadas mediante cientos (o incluso miles) de bases de datos SQL de Azure. [trabajos elásticos](sql-database-elastic-jobs-powershell.md) para ayudar a facilitar la administración de estas bases de datos.

Para descargar:
* La versión de Java de la biblioteca, consulte el [repositorio central de Maven](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* La versión de .NET de la biblioteca, consulte [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Documentación
1. [Introducción a las herramientas de Elastic Database](sql-database-elastic-scale-get-started.md)
2. [Características de Elastic Database](sql-database-elastic-scale-introduction.md)
3. [Administración de mapas de particiones.](sql-database-elastic-scale-shard-map-management.md)
4. [Migración de bases de datos existentes al escalado horizontal](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md)
6. [Consultas en varias particiones](sql-database-elastic-scale-multishard-querying.md)
7. [Incorporación de una partición con herramientas de Elastic Database](sql-database-elastic-scale-add-a-shard.md)
8. [Aplicaciones de múltiples inquilinos con herramientas de bases de datos elásticas y seguridad de nivel de fila](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Actualización de aplicaciones de la biblioteca de cliente](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Información general de consultas elásticas](sql-database-elastic-query-overview.md)
11. [Glosario de las herramientas de bases de datos elásticas](sql-database-elastic-scale-glossary.md)
12. [Biblioteca de cliente de Elastic Database con Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Biblioteca de cliente de Base de datos elástica con Dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [Herramienta de división y combinación](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Creación de bases de datos escalables en la nube](sql-database-elastic-database-client-library.md) 
16. [Preguntas frecuentes de herramientas de Base de datos elástica](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Capacidades de cliente
El escalado horizontal de aplicaciones mediante *particionamiento* presenta desafíos tanto para el desarrollador como para el administrador. La biblioteca de cliente simplifica las tareas de administración mediante herramientas que permiten a los desarrolladores y administradores administrar bases de datos de escalado horizontal. En un ejemplo típico, hay muchas bases de datos, conocidas como "particiones", que administrar. Los clientes se colocan en la misma base de datos, habiendo una por cliente (un esquema de inquilino único). La biblioteca de cliente incluye estas características:

- **Administración de Shard Map Manager**: se crea una base de datos especial llamada "Shard Map Manager". La administración de mapas de particiones es la capacidad que tiene una aplicación de administrar metadatos sobre sus particiones. Los desarrolladores pueden usar esta funcionalidad para registrar bases de datos como particiones, describir las asignaciones de claves de particionamiento individuales o intervalos de claves para esas bases de datos y mantener estos metadatos a medida que la cantidad y la composición de bases de datos evoluciona para reflejar los cambios en la capacidad. Sin la biblioteca de cliente de bases de datos flexible, necesitará dedicar mucho tiempo a escribir el código de administración al implementar el particionamiento. Para obtener más información, consulte [Administración de mapas de particiones](sql-database-elastic-scale-shard-map-management.md).

- **Enrutamiento dependiente de los datos**: imagine que la aplicación recibe una solicitud. Según el valor de clave de particionamiento de la solicitud, la aplicación necesita determinar la base de datos correcta basada en el valor de clave. A continuación, abre una conexión a la base de datos para procesar la solicitud. El enrutamiento dependiente de los datos proporciona la capacidad de abrir conexiones con una sola llamada simple al mapa de particiones de la aplicación. El enrutamiento dependiente de los datos era otra área del código de infraestructura que ahora está cubierta por la funcionalidad de la biblioteca de cliente de bases de datos elásticas. Para obtener más información, consulte [Enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md).
- **Consultas a través de particiones múltiples (MSQ)**: las consultas a través de particiones múltiples cuando una solicitud implica varias particiones (o todas). Una consulta a través de particiones múltiples ejecuta el mismo código T-SQL en todas las particiones o en un conjunto de particiones. Los resultados de las particiones implicadas se combinan en un conjunto de resultados global usando la semántica UNION ALL. La funcionalidad que se expone a través de la biblioteca cliente controla muchas tareas, entre ellas: administración de conexiones, administración de subprocesos, control de errores y procesamiento de resultados intermedios. MSQ puede consultar hasta cientos de particiones. Para obtener más información, consulte [Consultas a través de particiones múltiples](sql-database-elastic-scale-multishard-querying.md).

En general, los clientes que usan las herramientas de bases de datos elásticas pueden esperar obtener toda la funcionalidad de T-SQL al enviar las operaciones de partición local en lugar de las operaciones entre particiones que tienen su propia semántica.



## <a name="next-steps"></a>Pasos siguientes

- Biblioteca cliente de Elastic Database ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.NET](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)): para **descargar** la biblioteca.

- [Introducción a las herramientas de Elastic Database](sql-database-elastic-scale-get-started.md): para probar la **aplicación de ejemplo** que demuestra las funciones del cliente.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)): para realizar contribuciones al código.
- [Información general sobre las consultas elásticas de Azure SQL Database](sql-database-elastic-query-overview.md): para usar consultas elásticas.

- [Traslado de datos entre bases de datos escaladas horizontalmente en la nube](sql-database-elastic-scale-overview-split-and-merge.md): para obtener instrucciones sobre cómo utilizar la **herramienta de división y combinación**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

