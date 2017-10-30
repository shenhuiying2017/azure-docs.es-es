---
title: "¿Qué es Almacenamiento de datos SQL de Azure? | Microsoft Docs"
description: "Base de datos distribuida de clase empresarial, capaz de procesar volúmenes de petabytes de datos relacionales y no relacionales. Es el primer almacenamiento de datos en la nube de la industria que aumenta, reduce y hace una pausa en segundos."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: bjhubbard
editor: 
ms.assetid: 4006c201-ec71-4982-b8ba-24bba879d7bb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 10/23/2017
ms.author: jrj;barbkess
ms.openlocfilehash: d5ad5b566bd8d40ab6d7a9151af54890fd47cc88
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="what-is-azure-sql-data-warehouse"></a>¿Qué es Almacenamiento de datos SQL de Azure?

SQL Data Warehouse es un almacenamiento de datos empresarial (EDW) en la nube empresarial que aprovecha el procesamiento paralelo masivo (MPP) para ejecutar rápidamente consultas complejas en petabytes de datos. SQL Data Warehouse se usa como un componente clave de una solución de macrodatos. Importe macrodatos en SQL Data Warehouse con consultas T-SQL de PolyBase simples y, después, use la potencia de MPP para realizar análisis de alto rendimiento. Al realizar la integración y el análisis, el almacenamiento de datos pasará a ser la versión única de certeza con la que puede contar su empresa para obtener información.  


## <a name="key-component-of-big-data-solution"></a>Componente clave de la solución de macrodatos
SQL Data Warehouse es un componente clave de una solución de macrodatos de un extremo a otro en la nube.

![Solución de almacenamiento de datos](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

En una solución de datos en la nube, los datos provenientes de varios orígenes se ingieren en almacenes de macrodatos. Una vez que están en un almacén de macrodatos, Hadoop, y Spark y los algoritmos de aprendizaje automático preparan y entrenan los datos. Cuando los datos están listos para la realización de un análisis complejo, SQL Data Warehouse usa PolyBase para consultar los almacenes de macrodatos. PolyBase usa las consultas T-SQL estándar para llevar los datos a SQL Data Warehouse.
 
SQL Data Warehouse almacena los datos en tablas relacionales con almacenamiento en columnas. Este formato reduce considerablemente los costos de almacenamiento de datos y mejora el rendimiento de las consultas. Una vez que los datos están almacenados en SQL Data Warehouse, se pueden realizar análisis a gran escala. En comparación con los sistemas de bases de datos tradicionales, las consultas de análisis finalizan en segundos, en lugar de minutos, o en horas, en lugar de días. 

Los resultados del análisis pueden ir a aplicaciones o bases de datos de informes mundiales. En ellas, los analistas empresariales pueden obtener información útil para tomar decisiones empresariales bien informadas.

## <a name="optimization-choices"></a>Opciones de optimización

SQL Data Warehouse ofrece [niveles de rendimiento](performance-tiers.md) diseñados para proporcionar la flexibilidad necesaria para satisfacer sus necesidades de datos, independientemente de que sean grandes o pequeñas. Puede elegir un almacén de datos que esté optimizado para proporcionar mayor elasticidad o para el proceso. 

- El **nivel de rendimiento optimizado para elasticidad** separa las capas de proceso y almacenamiento en la arquitectura. Esta opción destaca en que las cargas de trabajo pueden aprovechar al máximo la separación entre proceso y almacenamiento mediante el escalado frecuente para proporcionar soporte técnico a periodos cortos de actividad máxima. Este nivel de proceso tiene el punto de precio básico más bajo y escala para admitir la mayoría de las cargas de trabajo del cliente.

- El **nivel de rendimiento optimizado para proceso** utiliza el hardware de Azure más reciente para introducir una nueva memoria caché de disco de estado sólido NVMe que mantiene los datos a los que se accede con mayor frecuencia cerca de la CPU, que es exactamente donde desea que estén. Al organizar automáticamente el almacenamiento en niveles, destacan las consultas complejas de este nivel de rendimiento, ya que todas las E/S se mantienen de forma local en la capa de proceso. Además, el almacén de columnas se ha mejorado para almacenar una cantidad ilimitada de datos en SQL Data Warehouse. El nivel de rendimiento optimizado para proceso proporciona un mayor nivel de escalabilidad, lo que permite escalar hasta 30 000 unidades de almacenamiento de datos (cDWU) de proceso. Elija este nivel para las cargas de trabajo que requieran un rendimiento continuo e increíblemente rápido.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ya conoce un poco SQL Data Warehouse, aprenda a [crear una instancia de SQL Data Warehouse][create a SQL Data Warehouse] y [cargar datos de ejemplo de manera rápida][load sample data]. Si no está familiarizado con Azure, el [Glosario de Azure][Azure glossary] le puede resultar útil para consultar la nueva terminología que se encuentre. O bien, examine algunos de estos otros recursos de SQL Data Warehouse.  

* [Casos de éxito de clientes]
* [Blogs]
* [Solicitud de función]
* [Vídeos]
* [Blogs de Customer Advisory Team]
* [Creación de una incidencia de soporte técnico]
* [Foro de MSDN]
* [Foro Stack Overflow]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Creación de una incidencia de soporte técnico]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Casos de éxito de clientes]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs de Customer Advisory Team]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Solicitud de función]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Foro de MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Foro Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/en-us/support/legal/sla/
