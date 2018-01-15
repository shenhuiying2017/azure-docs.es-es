---
title: "Resolución de diferencias de T-SQL de migración a Azure SQL Database | Microsoft Docs"
description: Instrucciones de Transact-SQL que no son totalmente compatibles con Azure SQL Database
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: c05abd9e-28a7-4c97-9bdf-bc60d08fc92e
ms.service: sql-database
ms.custom: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 10/23/2017
ms.author: carlrab
ms.openlocfilehash: f311c0d139d5ec35cbd85a34bd5a5e991bccba3a
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Resolución de diferencias de Transact-SQL durante la migración a SQL Database   
Cuando se [migra la base de datos](sql-database-cloud-migrate.md) de SQL Server a Azure SQL Server, es posible que descubra que la base de datos requiere cierta reingeniería antes de que se pueda migrar el servidor SQL Server. En este artículo se proporcionan instrucciones para ayudarle a realizar esta reingeniería y entender las razones subyacentes por las que es necesaria. Para detectar incompatibilidades, utilice el [Asistente para migración de datos (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Información general
La mayoría de las características de Transact-SQL que usan las aplicaciones se admiten en Microsoft SQL Server y Azure SQL Database. Por ejemplo, los componentes principales de SQL, como tipos de datos, operadores, funciones de cadena, aritméticas, lógicas y de cursor, funcionan de la misma forma en SQL Server y SQL Database. Pero hay algunas diferencias de T-SQL en los elementos DDL (lenguaje de definición de datos) y DML (lenguaje de manipulación de datos) que generan instrucciones y consultas de T-SQL que solo se admiten parcialmente (como se describe más adelante en este artículo).

Además, hay algunas características y sintaxis que no se admiten en absoluto porque Azure SQL Database se diseñó para aislar las características de cualquier dependencia de la base de datos maestra y el sistema operativo. Por eso, muchas actividades de nivel de servidor no son apropiadas para SQL Database. Las instrucciones y opciones de T-SQL no están disponibles si configuran opciones de nivel de servidor y componentes del sistema operativo, o especifican la configuración del sistema de archivos. Cuando se necesitan estas capacidades, suele estar disponible una alternativa adecuada en alguna otra manera de SQL Database o de otra función o servicio de Azure. 

Por ejemplo, la alta disponibilidad se integra en Azure, por lo que configurar Siempre disponible no es necesario (aunque puede interesarle configurar la replicación geográfica activa para una recuperación más rápida en caso de desastre). Por esto, las instrucciones de T-SQL relacionadas con los grupos de disponibilidad no son compatibles con SQL Database y tampoco se admiten las vistas de administración dinámica relacionadas con Siempre disponible.

Para obtener una lista de las funciones compatibles y no compatibles con SQL Database, vea [Características de Azure SQL Database](sql-database-features.md). La lista de esta página complementa ese artículo de instrucciones y características, y se centra en las instrucciones de Transact-SQL.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Instrucciones de sintaxis de Transact-SQL con diferencias parciales
Las instrucciones DDL (lenguaje de definición de datos) principales están disponibles, pero algunas instrucciones DDL tienen extensiones relacionadas con la ubicación de disco y las características no admitidas. 

- Las instrucciones CREATE y ALTER DATABASE tienen más de 30 opciones. Las instrucciones incluyen la ubicación de archivos, FILESTREAM y opciones de agente de servicio que solo se aplican a SQL Server. Puede que esto no sea importante si se crean las bases de datos antes de la migración, pero si se va a migrar código de T-SQL que crea bases de datos, se debe comparar [CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) con la sintaxis de SQL Server en [CREATE DATABASE (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) para asegurarse de que se admiten todas las opciones que se usan. CREATE DATABASE para Azure SQL Database también tiene opciones de objetivo de servicio y escalado flexible que se aplican únicamente a SQL Database.
- Las instrucciones CREATE y ALTER TABLE tienen opciones FileTable que no se pueden usar en SQL Database porque no se admite FILESTREAM.
- Se admiten las instrucciones de inicio de sesión CREATE y ALTER, pero SQL Database no ofrece todas las opciones. Para que la base de datos sea más portable, SQL Database promueve la utilización de usuarios de base de datos independiente en lugar de inicios de sesión siempre que sea posible. Para más información, vea [CREATE/ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx) y [Control y concesión de acceso a bases de datos](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Sintaxis de Transact-SQL no admitida en Azure SQL Database   
Además de las instrucciones de Transact-SQL relacionadas con las funciones no admitidas descritas en [Características de Azure SQL Database](sql-database-features.md), no se admiten las instrucciones y grupos de instrucciones siguientes. Por tanto, si la base de datos que se va a migrar usa alguna de las siguientes características, vuelva a diseñar el código de T-SQL para eliminar estas características e instrucciones de T-SQL.

- Intercalación de objetos del sistema
- Conexión relacionada: instrucciones de punto de conexión. SQL Database no admite la autenticación de Windows, pero admite la autenticación de Azure Active Directory similar. Algunos tipos de autenticación requieren la versión más reciente de SSMS. Para obtener más información, vea [Conexión a SQL Database o a SQL Data Warehouse mediante autenticación de Azure Active Directory](sql-database-aad-authentication.md).
- Consultas entre bases de datos con tres o cuatro nombres de partes. (Las consultas entre bases de datos de solo lectura se admiten mediante el uso de [consultas de base de datos elástica](sql-database-elastic-query-overview.md)).
- Encadenamiento de propiedad entre bases de datos, configuración `TRUSTWORTHY`
- `EXECUTE AS LOGIN` Use "EXECUTE AS USER" en su lugar.
- Se admite el cifrado excepto para la administración extensible de claves.
- Eventos: eventos, notificaciones de eventos, notificaciones de consulta
- Ubicación de archivos: sintaxis relacionada con la ubicación de archivos de base de datos, el tamaño y los archivos de base de datos que Microsoft Azure administra automáticamente.
- Alta disponibilidad: sintaxis relacionada con la alta disponibilidad que se administra mediante la cuenta de Microsoft Azure. Esto incluye la sintaxis de las copias de seguridad, restauración, Siempre activado, creación de reflejos de las bases de datos, trasvase de registros, modos de recuperación.
- Registro del LOG: sintaxis que se basa en el registro del LOG, que no está disponible en SQL Database: replicación de inserción y captura de datos modificados. SQL Database puede estar suscrito a un artículo de replicación de inserción.
- Funciones: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardware: sintaxis relacionada con la configuración del servidor relativa al hardware: memoria, subprocesos de trabajo, afinidad de CPU, marcas de seguimiento. Usar en su lugar niveles de servicio.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE` y nombres de cuatro partes
- .NET Framework: integración de CLR con SQL Server
- Búsqueda semántica
- Credenciales de servidor: use [credenciales con ámbito de base de datos](https://msdn.microsoft.com/library/mt270260.aspx) en su lugar.
- Elementos de nivel de servidor: roles de servidor, `sys.login_token`. `GRANT`, `REVOKE` y `DENY` de los permisos de nivel de servidor no están disponibles, aunque algunos se sustituyen por permisos en el nivel de base de datos. Algunos DMV útiles de nivel de servidor cuentan con DMV equivalentes de nivel de base de datos.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Opciones de `sp_configure` y `RECONFIGURE`. Algunas opciones están disponibles mediante [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- Agente SQL Server: sintaxis que se basa en el Agente SQL Server o la base de datos MSDB: alertas, operadores, servidores de administración central. Use el scripting como Azure PowerShell en su lugar.
- Auditoría de SQL Server: usar auditoría de SQL Database en su lugar.
- Seguimiento de SQL Server
- Marcas de seguimiento: algunos elementos de la marca de seguimiento se trasladaron a los modos de compatibilidad.
- Depuración de Transact-SQL
- Desencadenadores: desencadenadores LOGON o con ámbito de servidor
- Instrucción `USE`: para cambiar el contexto de base de datos a una base de datos distinta, debe crear una nueva conexión a la base de datos nueva.

## <a name="full-transact-sql-reference"></a>Referencia completa de Transact-SQL
Para obtener más información sobre la gramática de Transact-SQL, su uso y ejemplos, consulte [Referencia de Transact-SQL (motor de base de datos)](https://msdn.microsoft.com/library/bb510741.aspx) en Libros en pantalla de SQL Server. 

### <a name="about-the-applies-to-tags"></a>Acerca de las etiquetas "Se aplica a"
La referencia de Transact-SQL incluye artículos relacionados con las versiones de SQL Server que abarcan desde la de 2008 hasta la actual. Debajo del título del artículo hay una barra de iconos donde se muestran las cuatro plataformas de SQL Server y se indica la aplicabilidad. Por ejemplo, los grupos de disponibilidad se introdujeron en SQL Server 2012. En el artículo [CREATE AVAILABILTY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) se indica que la instrucción se aplica a **SQL Server (a partir de 2012)**. La instrucción no se aplica a SQL Server 2008, SQL Server 2008 R2, Azure SQL Database, Azure SQL Data Warehouse o Almacenamiento de datos paralelos.

En algunos casos, la línea general de un artículo se puede usar en un producto, pero existen diferencias poco significativas entre los productos. Las diferencias se indican en los puntos medios del artículo según corresponda. En algunos casos, la línea general de un artículo se puede usar en un producto, pero existen diferencias poco significativas entre los productos. Las diferencias se indican en los puntos medios del artículo según corresponda. Por ejemplo, el artículo CREATE TRIGGER está disponible en SQL Database. No obstante, la opción **ALL SERVER** para desencadenadores de nivel de servidor indica que no se pueden usar desencadenadores de nivel de servidor en SQL Database. Use desencadenadores de nivel de base de datos en su lugar.

## <a name="next-steps"></a>pasos siguientes

Para obtener una lista de las funciones compatibles y no compatibles con SQL Database, vea [Características de Azure SQL Database](sql-database-features.md). La lista de esta página complementa ese artículo de instrucciones y características, y se centra en las instrucciones de Transact-SQL.

