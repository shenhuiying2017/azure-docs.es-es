---
title: "¿Qué es SQL Database? Introducción a SQL Database | Microsoft Docs"
description: 'Get an introduction to SQL Database: technical details and capabilities of Microsoft''s relational database management system (RDBMS) in the cloud.'
keywords: "introducción a sql,introducción a sql,qué es base de datos sql"
services: sql-database
documentationcenter: 
author: shontnew
manager: jhubbard
editor: cgronlun
ms.assetid: c561f600-a292-4e3b-b1d4-8ab89b81db48
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 12/20/2016
ms.author: shkurhek;carlrab
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: 262583a2a11c41cf7acb55599041692137165e8f

---
# <a name="what-is-sql-database-introduction-to-sql-database"></a>¿Qué es SQL Database? Introducción a SQL Database
SQL Database es un servicio de bases de datos relacionales de Microsoft Cloud que usa el motor de Microsoft SQL Server líder del mercado, lo que le permite controlar cargas de trabajo críticas. SQL Database ofrece un rendimiento predecible en varios niveles de servicio, escalabilidad dinámica sin tiempo de inactividad, continuidad empresarial integrada y protección de datos (todo ello casi sin administración). Estas funcionalidades permiten centrarse en el desarrollo rápido de aplicaciones y en reducir el plazo de acceso al mercado, en lugar de tener que dedicar tiempo y recursos a la administración tanto de máquinas virtuales como de la infraestructura. Al basarse en el motor [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), SQL Database admite las herramientas, bibliotecas y API de SQL Server existentes. Como consecuencia, facilita el desarrollo de nuevas soluciones, el movimiento de soluciones de SQL Server existentes y la ampliación de las soluciones de SQL Server existentes a Microsoft Cloud sin necesidad de adquirir nuevos conocimientos.

Este artículo presenta los principales conceptos y características de Base de datos SQL relacionados con el rendimiento, escalabilidad y facilidad de uso, con vínculos para explorar los detalles. Si está listo para empezar con los tutoriales prácticos, diríjase a [Tutorial de SQL Database: creación de una base de datos SQL en cuestión de minutos con datos de ejemplo y Azure Portal](sql-database-get-started.md) o [Creación de un nuevo grupo de bases de datos elásticas con Azure Portal](sql-database-elastic-pool-create-portal.md). En este vídeo encontrará una breve demostración.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Azure-SQL-Database-create-DBs-in-seconds/player]
> 
> 

## <a name="adjust-performance-and-scale-without-downtime"></a>Ajuste del rendimiento y escalabilidad sin tiempo de inactividad
El servicio SQL Database ofrece tres niveles de servicio: Básico, Estándar y Premium. Cada nivel de servicio ofrece [diferentes niveles de rendimiento y capacidades](sql-database-service-tiers.md) para admitir todos los tipos de cargas de trabajo de bases de datos, de ligeras a pesadas. Puede compilar su primera aplicación en una base de datos pequeña por poco dinero al mes y, después, [cambiar el nivel de servicio](sql-database-scale-up.md) manualmente o mediante programación en cualquier momento para adecuarla a las necesidades de su solución. Y todo esto se puede hacer que la aplicación o los clientes sufran ningún tipo de inactividad. La escalabilidad dinámica permite que una base de datos responda transparentemente a los requisitos de recursos, que cambian con rapidez, y le permite pagar solo por los recursos que necesite cuando los necesite.

## <a name="elastic-pools-to-maximize-resource-utilization"></a>Grupos elásticos para maximizar la utilización de los recursos
Para muchas empresas y aplicaciones, poder crear bases de datos únicas y aumentar o reducir el rendimiento a petición es suficiente, especialmente si los patrones de uso son relativamente predecibles. Pero si dichos patrones son impredecibles, pueden dificultar la administración de los costos y del modelo de negocio. Los [grupos elásticos](sql-database-elastic-pool.md) están diseñadas para solucionar este problema. El concepto es sencillo. Se asignan los recursos de rendimiento a un grupo, en lugar a una base de datos individual y se paga por los recursos de rendimiento colectivos del grupo, no por el rendimiento de la base de datos única. Con los grupos elásticos no es preciso centrarse en marcar el ascenso y la bajada de rendimiento de la base de datos a medida que varía la demanda de recursos. Las bases de datos en grupos consumen los recursos de rendimiento del grupo elástico a medida que se necesiten. Las bases de datos en grupos consumen los recursos, pero nunca superan los límites del grupo, por lo que el costo es en todo momento predecible, aunque el uso de las bases de datos individuales no lo sea. Es más, puede [agregar bases de datos al grupo y quitarlas del mismo](sql-database-elastic-pool-manage-portal.md), lo que escala la aplicación de un puñado de bases de datos a miles, y todo sin perder el control del presupuesto. Por último, también puede controlar el número mínimo y máximo de recursos disponibles para las bases de datos del grupo, con el fin de asegurarse de que ninguna de las bases de datos del grupo utiliza todos los recursos del grupo y que todas las bases de datos del grupo tienen un número mínimo garantizado de recursos. Para más información sobre los patrones de diseño de las aplicaciones SaaS que usan grupos elásticos, consulte [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Diseño de patrones para aplicaciones SaaS multiempresa con Base de datos SQL de Azure).

## <a name="blend-single-databases-with-pooled-databases"></a>Fusión de bases de datos únicas con bases de datos en grupos
Independientemente de lo que use (bases de datos únicas o grupos elásticos) no se encontrará bloqueado. Puede fusionar bases de datos únicas con grupos elásticos y cambiar los niveles de servicio tanto de las primeras como de los segundos de forma rápida y sencilla para adaptarlos a su situación. Además, con la potencia y el alcance de Azure, puede combinar otros servicios de Azure con SQL Database para satisfacer sus necesidades únicas de diseño de aplicaciones, impulsar las eficiencias de costos y recursos, y acceder a nuevas oportunidades de negocio.

## <a name="monitoring-and-alerting"></a>Supervisión y alertas
Pero, ¿cómo se puede comparar el rendimiento relativo de las bases de datos únicas con el de los grupos elásticos? ¿Cómo se sabe el momento correcto en el que debe hacer clic para detenerse al subir y bajar por el dial? Use las herramientas de [supervisión de rendimiento integrado](sql-database-performance.md) y de [alerta](sql-database-insights-alerts-portal.md) herramientas, en combinación con la evaluación del rendimiento basada en [unidades de transacción de bases de datos (DTU) para bases de datos únicas y DTU elásticas (eDTU) para grupos elásticos](sql-database-what-is-a-dtu.md). Uso de estas herramientas, puede evaluar rápidamente el impacto de escalar verticalmente en función de su suscripción actual o se proyecta necesidades de rendimiento. Para más detalles, consulte [Opciones y rendimiento de Base de datos SQL: comprender lo que está disponible en cada nivel de servicio](sql-database-service-tiers.md) .

## <a name="keep-your-app-and-business-running"></a>Mantenimiento de la aplicación y el negocio en funcionamiento
El contrato de nivel de servicio [(SLA)](http://azure.microsoft.com/support/legal/sla/)de Azure de disponibilidad del 99,99 %, líder del sector, con la tecnología de una red global de centros de datos administrados por Microsoft, ayuda a mantener las aplicaciones en funcionamiento de forma ininterrumpida. Con cada base de datos SQL, se saca provecho de las ventajas de la seguridad integrada, la tolerancia a errores y la [protección de datos](sql-database-automated-backups.md), algo que de lo contrario tendría que adquirir o diseñar, compilar y administrar. Con SQL Database, cada nivel de servicio ofrece un conjunto completo de opciones y características de continuidad empresarial que puede usar para empezar a trabajar y seguir haciéndolo. Puede usar la [restauración a un momento dado](sql-database-recovery-using-backups.md) para devolver una base de datos a un estado anterior, con un plazo máximo de 35 días. Puede configurar la [retención de copia de seguridad a largo plazo](sql-database-long-term-retention.md) para guardar las copias de seguridad en un almacén seguro hasta diez años. Además, si el centro de datos que hospeda las bases de datos sufre una interrupción, puede restaurar las bases de datos a partir de [copias con redundancia geográfica de las copias de seguridad recientes]((sql-database-recovery-using-backups.md). Si es necesario, también puede configurar [réplicas con redundancia geográfica legibles](sql-database-geo-replication-overview.md) en una o varias regiones para que se produzca una conmutación por error rápida en caso de interrupción en el centro de datos. Estas réplicas también se pueden usar para agilizar el rendimiento de lectura en distintas regiones geográficas o para [actualizaciones de las aplicaciones sin tiempo de inactividad](sql-database-manage-application-rolling-upgrade.md). 

![Replicación geográfica de Base de datos SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)

Para obtener más información acerca de las diferentes características de continuidad empresarial disponibles para los distintos niveles de servicio, consulte [Continuidad empresarial](sql-database-business-continuity.md) .

## <a name="secure-your-data"></a>Protección de los datos
SQL Server tiene una tradición de seguridad de datos que SQL Database mantiene con características que limitan el acceso, protegen los datos y le ayudan a supervisar la actividad. Para ver un resumen rápido de las opciones de seguridad que tiene que tiene Base de datos SQL, consulte [Protección de bases de datos SQL](sql-database-security-overview.md) . Para obtener una vista más completa de las características de seguridad, consulte [Centro de seguridad para el motor de base de datos SQL Server y Base de datos SQL](https://msdn.microsoft.com/library/bb510589) . Y para obtener información sobre la seguridad de plataforma de Azure, vaya al [Centro de confianza de Azure](https://azure.microsoft.com/support/trust-center/security/) .

## <a name="next-steps"></a>Pasos siguientes
Ahora que leyó una introducción a la Base de datos SQL y contestó a la pregunta "¿Qué es la Base de datos SQL?", está listo para:

* Consulte la [página de precios](https://azure.microsoft.com/pricing/details/sql-database/), para ver comparativas y calculadoras de los costos tanto de las bases de datos únicas como de los grupos elásticos.
* Para más información, consulte [¿Qué es un grupo de bases de datos elásticas de Azure?](sql-database-elastic-pool.md).
* Comience por [crear su primera base de datos](sql-database-get-started.md).
* Para crear su primera aplicación en C#, Java, Node.js, PHP, Python o Ruby, consulte [Bibliotecas de conexiones para Base de datos SQL y SQL Server](sql-database-libraries.md)



<!--HONumber=Dec16_HO4-->


