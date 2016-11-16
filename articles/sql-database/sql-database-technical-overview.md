---
title: "¿Qué es Base de datos SQL? Introducción a SQL Database | Microsoft Docs"
description: 'Get an introduction to SQL Database: technical details and capabilities of Microsoft''s relational database management system (RDBMS) in the cloud.'
keywords: "introducción a sql,introducción a sql,qué es base de datos sql"
services: sql-database
documentationcenter: 
author: shontnew
manager: jhubbard
editor: cgronlun
ms.assetid: c561f600-a292-4e3b-b1d4-8ab89b81db48
ms.service: sql-database
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/08/2016
ms.author: shkurhek
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 23d68a523fa9a79d2b5154eef04a5f6e706bdeb2


---
# <a name="what-is-sql-database-introduction-to-sql-database"></a>¿Qué es Base de datos SQL? Introducción a Base de datos SQL
Base de datos SQL es un servicio de bases de datos relacionales en la nube basado en el motor de Microsoft SQL Server líder del mercado, con capacidades críticas. Base de datos SQL ofrece un rendimiento predecible, escalabilidad sin tiempo de inactividad, continuidad empresarial y protección de datos (todo ello casi sin administración). Se puede centrar en el desarrollo rápido de aplicaciones y en reducir el plazo de comercialización, en lugar de administrar las máquinas virtuales y la infraestructura. Al basarse en el motor [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) , Base de datos SQL admite las herramientas, bibliotecas y API de SQL Server existentes, lo que facilita el proceso del paso a la nube.

Este artículo presenta los principales conceptos y características de Base de datos SQL relacionados con el rendimiento, escalabilidad y facilidad de uso, con vínculos para explorar los detalles. Si está listo para dar el salto, puede [crear la primera SQL Database](sql-database-get-started.md) o [crear un grupo de bases de datos elásticas](sql-database-elastic-pool-create-portal.md) en cuestión de minutos. Si desea un análisis más profundo, vea este vídeo de 30 minutos.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON326/player]
> 
> 

## <a name="adjust-performance-and-scale-without-downtime"></a>Ajuste del rendimiento y escalabilidad sin tiempo de inactividad
Base de datos SQL está disponible en los *niveles de servicio*Básico, Estándar y Premium. Cada nivel de servicio ofrece [diferentes niveles de rendimiento y capacidades](sql-database-service-tiers.md) para admitir todos los tipos de cargas de trabajo de bases de datos, de ligeras a pesadas. Puede compilar su primera aplicación en una base de datos pequeña por poco dinero al mes y, a continuación, [cambiar el nivel de servicio](sql-database-scale-up.md) manualmente o mediante programación en cualquier momento cuando la aplicación se haga viral en todo el mundo, sin tiempo de inactividad para la aplicación o sus clientes.

Para muchas empresas y aplicaciones, poder crear bases de datos y aumentar o reducir el rendimiento de una base de datos única a petición es suficiente, especialmente si los patrones de uso son relativamente predecibles. Pero si dichos patrones son impredecibles, pueden dificultar la administración de los costos y del modelo de negocio.

[grupos elásticos](sql-database-elastic-pool.md) de Base de datos SQL solucionan este problema. El concepto es sencillo. Se asigna el rendimiento a un grupo y se paga por el rendimiento colectivo del grupo, en lugar de por el de una base de datos única. No es preciso aumentar o reducir el rendimiento de la base de datos. Las bases de datos del grupo, denominadas *bases de datos elásticas*, se escalan o reducen verticalmente para satisfacer la demanda. Las bases de datos elásticas consumen, pero no superan los límites del grupo, por lo que el costo sigue siendo predecible, aunque su uso no lo sea. Es más, puede [agregar bases de datos al grupo y quitarlas del mismo](sql-database-elastic-pool-manage-portal.md), lo que escala la aplicación de un puñado de bases de datos a miles, y todo sin perder el control del presupuesto. Para más información sobre los patrones de diseño de las aplicaciones SaaS que usan grupos elásticos, consulte [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Diseño de patrones para aplicaciones SaaS multiinquilino con Base de datos SQL de Azure).

Independientemente de lo que elija (única o elásticas), no se quedará bloqueado. Puede fusionar bases de datos únicas con grupos de bases de datos elásticas y cambiar los niveles de servicio tanto de las bases de datos únicas como de los grupos para crear diseños innovadores. Además, con la potencia y el alcance de Azure, puede combinar y hacer coincidir los servicios de Azure con Base de datos SQL para satisfacer sus necesidades únicas de diseño de aplicaciones modernas, impulsar las eficiencias de costos y recursos, y desbloquear nuevas oportunidades de negocio.

Pero, ¿cómo se puede comparar el rendimiento relativo de las bases de datos y los grupos de bases de datos? ¿Cómo se sabe el momento correcto en el que debe hacer clic para detenerse al subir y bajar por el dial? La respuesta es la unidad de transacción de base de datos (DTU) para bases de datos únicas y la DTU elástica (eDTU) para bases de datos elásticas y grupos de bases de datos. Para más detalles, consulte [Opciones y rendimiento de Base de datos SQL: comprender lo que está disponible en cada nivel de servicio](sql-database-service-tiers.md) .

## <a name="keep-your-app-and-business-running"></a>Mantenimiento de la aplicación y el negocio en funcionamiento
El contrato de nivel de servicio [(SLA)](http://azure.microsoft.com/support/legal/sla/)de Azure de disponibilidad del 99,99 %, líder del sector, con la tecnología de una red global de centros de datos administrados por Microsoft, ayuda a mantener las aplicaciones en funcionamiento de forma ininterrumpida. Con cada base de datos SQL, aprovecha las ventajas de protección de datos integrada, tolerancia a errores y protección de datos, algo que de lo contrario tendría que diseñar, adquirir, compilar y administrar. Aun así, en función de las exigencias de su negocio, puede solicitar capas de protección adicionales para asegurarse de que tanto la aplicación como el negocio se pueden recuperar rápidamente en caso de desastre, error o cualquier otro contratiempo grave. Con Base de datos SQL, cada nivel de servicio ofrece un menú de características diferente que se puede usar para ponerse en funcionamiento y permanecer así. Puede usar la restauración en un momento dado para devolver una base de datos a un estado anterior, con un plazo máximo de 35 días. Además, si el centro de datos que hospeda las bases de datos sufre una interrupción, es posible conmutar a las réplicas de base de datos de otra región. O bien, se pueden usar réplicas para las actualizaciones o la reubicación en otras regiones.

![Replicación geográfica de Base de datos SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)

Para obtener más información acerca de las diferentes características de continuidad empresarial disponibles para los distintos niveles de servicio, consulte [Continuidad empresarial](sql-database-business-continuity.md) .

## <a name="secure-your-data"></a>Protección de los datos
SQL Server tradicionalmente ha proporcionado una sólida seguridad  de datos que SQL Database respeta con sus características que limitan el acceso, protegen los datos y ayudan a supervisar la actividad. Para ver un resumen rápido de las opciones de seguridad que tiene que tiene Base de datos SQL, consulte [Protección de bases de datos SQL](sql-database-security.md) . Para obtener una vista más completa de las características de seguridad, consulte [Centro de seguridad para el motor de base de datos SQL Server y Base de datos SQL](https://msdn.microsoft.com/library/bb510589) . Y para obtener información sobre la seguridad de plataforma de Azure, vaya al [Centro de confianza de Azure](https://azure.microsoft.com/support/trust-center/security/) .

## <a name="next-steps"></a>Pasos siguientes
Ahora que leyó una introducción a la Base de datos SQL y contestó a la pregunta "¿Qué es la Base de datos SQL?", está listo para:

* En la página [Precios de Base de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/) , encontrará comparativas y calculadoras de los costos tanto de las bases de datos únicas como de las bases de datos elásticas.
* Para más información, consulte [¿Qué es un grupo de bases de datos elásticas de Azure?](sql-database-elastic-pool.md).
* Comience por [crear su primera base de datos](sql-database-get-started.md).
* [Conectarse y realizar consultas con SSMS](sql-database-connect-query-ssms.md)
* Para crear su primera aplicación en C#, Java, Node.js, PHP, Python o Ruby, consulte [Bibliotecas de conexiones para Base de datos SQL y SQL Server](sql-database-libraries.md)
* Para ver un índice de los títulos y las descripciones, consulte [Todos los temas del servicio Base de datos SQL de Azure](sql-database-index-all-articles.md).




<!--HONumber=Nov16_HO2-->


