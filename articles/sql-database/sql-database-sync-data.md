---
title: "Sincronización de datos (versión preliminar) | Microsoft Docs"
description: "Se trata de una introducción a Azure SQL Data Sync (versión preliminar)."
services: sql-database
documentationcenter: 
author: douglaslms
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: load & move data
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: douglasl
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: 95404f9dbffa08edf12ee6c07f671d5f207ce99a
ms.contentlocale: es-es
ms.lasthandoff: 08/05/2017

---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Sincronización de datos entre varias bases de datos locales y de la nube con SQL Data Sync

SQL Data Sync es un servicio basado en Azure SQL Database que permite sincronizar los datos seleccionados de manera bidireccional entre varias bases de datos SQL e instancias de SQL Server.

Data Sync se basa en el concepto de un grupo de sincronización. Un grupo de sincronización es un grupo de bases de datos que desea sincronizar.

Un grupo de sincronización tiene las siguientes propiedades:

-   En el **esquema de sincronización** se describen qué datos se están sincronizando.

-   La **dirección de sincronización** puede ser bidireccional o puede fluir solo en una dirección. Es decir, la dirección de sincronización puede ser de la *base de datos central al cliente* o del *cliente a la base de datos central*, o ambas opciones.

-   El **intervalo de sincronización** se refiere a la frecuencia con que se produce la sincronización.

-   El **directiva de resolución de conflictos** es una directiva de nivel de grupo, que puede ser *Prevalece la base de datos central* o *Prevalece el cliente*.

Data Sync usa una topología de concentrador y radio para sincronizar los datos. Defina una de las bases de datos del grupo como base de datos central. El resto de las bases de datos son bases de datos miembro. La sincronización solo se produce entre la base de datos central y los clientes individuales.
-   La **base de datos central** debe ser una base de datos SQL de Azure.
-   Las **bases de datos miembro** pueden ser bases de datos SQL, bases de datos de SQL Server locales o instancias de SQL Server en Azure Virtual Machines.
-   La **base de datos de sincronización** contiene los metadatos y el registro para Data Sync. La base de datos de sincronización tiene que ser una base de datos SQL de Azure ubicada en la misma región que la base de datos central. La base de datos la crea el propio cliente y es de su propiedad.

> [!NOTE]
> Si usa una base de datos local, debe [configurar un agente local](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-sql-data-sync).

![Sincronización de datos entre bases de datos](media/sql-database-sync-data/sync-data-overview.png)

## <a name="when-to-use-data-sync"></a>Cuándo usar Data Sync

Data Sync es útil en los casos en que es necesario mantener los datos actualizados entre varias bases de datos SQL de Azure o bases de datos de SQL Server. Estos son los casos de uso principales de Data Sync:

-   **Sincronización de datos híbridos:** con la sincronización de datos, puede mantener los datos sincronizados entre bases de datos locales y bases de datos SQL de Azure para habilitar aplicaciones híbridas con su capa de datos de SQL. Esta funcionalidad puede interesar a los clientes que se plantean realizar la migración a la nube y les gustaría colocar algunas de sus aplicaciones en Azure.

-   **Aplicaciones distribuidas:** en muchos casos, es conveniente separar diferentes cargas de trabajo entre diferentes bases de datos. Por ejemplo, si tiene una base de datos de producción de grande, pero también debe ejecutar una carga de trabajo de informes o análisis en estos datos, resulta útil tener una segunda base de datos para esta carga de trabajo adicional. Este enfoque minimiza el impacto de rendimiento en la carga de trabajo de producción. Puede usar Data Sync para mantener estas dos bases de datos sincronizadas.

-   **Aplicaciones globalmente distribuidas:** muchas empresas abarcan varias regiones e incluso varios países. Para minimizar la latencia de red, es preferible disponer de los datos en una región más cercana. Con Data Sync, puede mantener sincronizadas con facilidad las bases de datos de regiones de todo el mundo.

No se recomienda Data Sync en los siguientes escenarios:

-   Recuperación ante desastres

-   Escalado de lectura

-   ETL (OLTP a OLAP)

-   Migración de SQL Server local a Azure SQL Database

## <a name="how-does-data-sync-work"></a>¿Cómo funciona Data Sync? 

-   **Seguimiento de cambios de datos:** Data Sync realiza un seguimiento de cambios mediante los desencadenadores de inserción, actualización y eliminación. Los cambios se registran en una tabla en la base de datos de usuario.

-   **Sincronización de datos:** Data Sync está diseñado en un modelo de concentrador y radio. La base de datos central se sincronizada con cada cliente individualmente. Los cambios de la base de datos central se descargan en el cliente y, después, los cambios del cliente se cargan en la base de datos central.

-   **Resolución de conflictos:** Data Sync proporciona dos opciones para la resolución de conflictos, *Prevalece la base de datos central* o *Prevalece el cliente*.
    -   Si selecciona *Prevalece la base de datos central*, los cambios de la base de datos central siempre sobrescriben los cambios del cliente.
    -   Si selecciona *Prevalece el cliente*, los cambios del cliente sobrescriben los cambios de la base de datos central. Si hay más de un cliente, el valor final depende del cliente que primero se sincronice.

## <a name="limitations-and-considerations"></a>Limitaciones y consideraciones

### <a name="performance-impact"></a>Impacto en el rendimiento
Data Sync usa desencadenadores de inserción, actualización y eliminación para realizar un seguimiento de los cambios. Crea tablas laterales en la base de datos de usuario para hacer un seguimiento de los cambios. Estas actividades de seguimiento de cambios afectan a la carga de trabajo de la base de datos. Evalúe el nivel de servicio y realice la actualización si fuera necesario.

### <a name="eventual-consistency"></a>Coherencia final
Como Data Sync se basa en desencadenadores, la coherencia transaccional no está garantizada. Microsoft garantiza que todos los cambios se realicen finalmente y que Data Sync no ocasione pérdida de datos.

### <a name="unsupported-data-types"></a>Tipos de datos no admitidos

-   Secuencia de archivos

-   UDT SQL/CLR

-   XMLSchemaCollection (XML admitido)

-   Cursor, Timestamp, Hierarchyid

### <a name="requirements"></a>Requisitos

-   Cada tabla debe tener una clave principal.

-   Una tabla no puede tener una columna de identidad que no sea la clave principal.

-   Los nombres de objetos (bases de datos, tablas y columnas) no pueden contener los caracteres imprimibles punto (.), corchete de apertura ([) o corchete de cierre (]).

### <a name="limitations-on-service-and-database-dimensions"></a>Limitaciones de las dimensiones de la base de datos y del servicio

|                                                                 |                        |                             |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| **Dimensiones**                                                      | **Límite**              | **Solución alternativa**              |
| Número máximo de grupos de sincronización a los que una base de datos puede pertenecer       | 5                      |                             |
| Número máximo de puntos de conexión en un único grupo de sincronización              | 30                     | Crear varios grupos de sincronización |
| Número máximo de puntos de conexión locales en un único grupo de sincronización | 5                      | Crear varios grupos de sincronización |
| Nombres de base de datos, tabla, esquema y columna                       | 50 caracteres por nombre |                             |
| Tablas de un grupo de sincronización                                          | 500                    | Crear varios grupos de sincronización |
| Columnas de una tabla de un grupo de sincronización                              | 1000                   |                             |
| Tamaño de la fila de datos en una tabla                                        | 24 Mb                  |                             |
| Intervalo de sincronización mínimo                                           | 5 minutos              |                             |

## <a name="common-questions"></a>Preguntas frecuentes

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>¿Con qué frecuencia puede sincronizar mis datos 
Data Sync? 
La frecuencia mínima es cada cinco minutos.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>¿Puedo usar Data Sync para realizar la sincronización solo bases de datos locales de SQL Server? 
No directamente. Sin embargo, es posible realizar una sincronización indirecta entre bases de datos locales de SQL Server, mediante la creación de una base de datos central en Azure y la posterior incorporación de bases de datos locales al grupo de sincronización.
   
### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-keep-them-synchronized"></a>¿Puedo usar Data Sync para propagar datos de mi base de datos de producción a una base de datos vacía y, después, mantenerlos sincronizados? 
Sí. Cree el esquema manualmente en la base de datos nueva mediante la generación de scripts del original. Después de crear el esquema, agregue las tablas a un grupo de sincronización para copiar los datos y mantenerlos sincronizados.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>¿Por qué veo tablas que no he creado?  
Data Sync crea tablas laterales en su base de datos para hacer un seguimiento de los cambios. No las elimine, ya que Data Sync dejaría de funcionar.
   
### <a name="i-got-an-error-message-that-said-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-the-error"></a>He recibido el mensaje de error "No se puede insertar el valor NULL en la columna \<columna\>. La columna no admite valores NULL." ¿Qué significa esto y cómo puedo corregir el error? 
Este mensaje de error indica que uno de los dos problemas siguientes:
1.  Puede haber una tabla sin una clave principal. Para corregir este problema, agregue una clave principal a todas las tablas que va a sincronizar.
2.  Puede haber una cláusula WHERE en la instrucción CREATE INDEX. La sincronización no controla esta condición. Para solucionar este problema, quite la cláusula WHERE o realice los cambios manualmente en todas las bases de datos. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>¿Cómo trata Data Sync las referencias circulares? En otras palabras, ¿cuándo se sincronizan los mismos datos en varios grupos de sincronización y sigue cambiando como resultado?
Data Sync no controla las referencias circulares, así que asegúrese de no usarlas. 

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre SQL Data Sync, consulte:

-   [Introducción a SQL Data Sync](sql-database-get-started-sql-data-sync.md)

-   Para obtener ejemplos completos de PowerShell que muestren cómo configurar SQL Data Sync:
    -   [Uso de PowerShell para sincronizar entre varias bases de datos SQL de Azure.](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Uso de PowerShell para realizar la sincronización entre una base de datos SQL de Azure y una base de datos de SQL Server local](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Descarga de la documentación técnica completa de SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_full_documentation.pdf?raw=true)

-   [Descarga de la documentación de la API de REST de SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Para más información sobre SQL Database, consulte:

-   [Información general de SQL Database](sql-database-technical-overview.md)

-   [Administración del ciclo de vida de las aplicaciones](https://msdn.microsoft.com/library/jj907294.aspx)

