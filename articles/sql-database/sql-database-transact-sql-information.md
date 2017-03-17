---
title: Incompatible en Azure SQL Database T-SQL | Microsoft Docs
description: Instrucciones de Transact-SQL que no son totalmente compatibles con la Base de datos SQL de Azure
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: c05abd9e-28a7-4c97-9bdf-bc60d08fc92e
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/22/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: a08d9f2ef29002f10473b0e041737c9c607f3ca0
ms.openlocfilehash: 7d6de93c99141248ea970ea668fb0b2191267b62
ms.lasthandoff: 03/01/2017


---
# <a name="azure-sql-database-transact-sql-differences"></a>Diferencias de Transact-SQL de Base de datos SQL de Azure   
La mayoría de las características de Transact-SQL de que las aplicaciones dependen se admiten en Microsoft SQL Server y Base de datos SQL de Azure. Por ejemplo, los componentes principales de SQL, como tipos de datos, operadores, funciones de cadena, operadores aritméticos, lógicos y cursor, etc., funcionan sin diferencias con respecto a SQL Server.

# <a name="why-some-transact-sql-is-not-supported"></a>¿Por qué no son compatibles algunos Transact-SQL?
Azure SQL Database se ha diseñado para aislar las características de cualquier dependencia de la base de datos maestra y el sistema operativo. Como consecuencia, muchas actividades de nivel de servidor no son apropiadas para SQL Database. Normalmente las instrucciones de Transact-SQL no están disponibles si configuran opciones de nivel de servidor y componentes del sistema operativo o especifican la configuración del sistema de archivos. Cuando son necesarias funciones que se encuentran fuera de la base de datos de usuario, suele estar disponible una alternativa adecuada en alguna otra manera de SQL Database o cualquier otra función o servicio de Azure. 

Por ejemplo, Always On no se aplica a Azure SQL Database, puesto que la alta disponibilidad se integra en el servicio: todas las bases de datos tienen disponibilidad alta. Por esta razón, las instrucciones de Transact-SQL relacionadas con los grupos de disponibilidad no son compatibles con SQL Database y no se admiten las vistas de administración dinámica relacionadas con Siempre activado.  

Para obtener una lista de las funciones compatibles y no compatibles con SQL Database, consulte las [consideraciones, instrucciones y funciones de Azure SQL Database](sql-database-features.md).


## <a name="transact-sql-syntax-partially-supported-in-sql-database"></a>Sintaxis de Transact-SQL parcialmente admitida en SQL Database
SQL Database es compatible con algunos, pero no con todos, los argumentos que existen en las instrucciones correspondientes de administración de bases de datos e inicios de sesión de Transact-SQL de SQL Server 2016. Por ejemplo, la instrucción `CREATE DATABASE` está disponible en Azure SQL Database, pero no todas las opciones admitidas en SQL Server se admiten en Azure SQL Database, y viceversa. Consulte los temas de sintaxis vinculados para obtener más información sobre las áreas compatibles de cada instrucción.

- Bases de datos: [CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER DATABASE](https://msdn.microsoft.com/library/mt574871.aspx)   
- Inicios de sesión: [CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)   

## <a name="transact-sql-syntax-not-supported-in-sql-database"></a>Sintaxis de Transact-SQL no admitida en SQL Database   
Además de las instrucciones de Transact-SQL relacionadas con las funciones no admitidas descritas en las consideraciones, instrucciones y funciones de [Azure SQL Database](sql-database-features.md), no son compatibles las instrucciones y grupos de instrucciones siguientes.
- Intercalación de objetos del sistema
- Conexión relacionada: instrucciones de punto de conexión, `ORIGINAL_DB_NAME`. Base de datos SQL no admite la autenticación de Windows, pero admite la autenticación de Azure Active Directory similar. Algunos tipos de autenticación requieren la versión más reciente de SSMS. Para obtener más información, vea [Conexión a Base de datos SQL o a Almacenamiento de datos SQL mediante autenticación de Azure Active Directory](sql-database-aad-authentication.md).
- Consultas entre bases de datos con tres o cuatro nombres de partes. (Las consultas entre bases de datos de solo lectura se admiten mediante el uso de [consultas de base de datos elástica](sql-database-elastic-query-overview.md)).
- Encadenamiento de propiedad entre bases de datos, configuración `TRUSTWORTHY`
- `DATABASEPROPERTY` Se usa `DATABASEPROPERTYEX` en su lugar.
- `EXECUTE AS LOGIN` Use "EXECUTE AS USER" en su lugar.
- Se admite el cifrado excepto para la administración extensible de claves.
- Eventos: eventos, las notificaciones de eventos, notificaciones de consulta
- Sintaxis relacionada con la ubicación de los archivos de las bases de datos, el tamaño y los archivos de las bases de datos que Microsoft Azure administra automáticamente.
- Sintaxis relacionada con la alta disponibilidad que se administra mediante su cuenta de Microsoft Azure. Esto incluye la sintaxis de las copias de seguridad, restauración, Siempre activado, creación de reflejos de las bases de datos, trasvase de registros, modos de recuperación.
- Sintaxis que se basa en el registro del LOG que no está disponible en SQL Database: replicación de inserción y captura de datos modificados. SQL Database puede estar suscrito a un artículo de replicación de inserción.
- Sintaxis que se basa en el Agente SQL Server o la base de datos MSDB: alertas, operadores, servidores de administración central. Use el scripting como Azure PowerShell en su lugar.
- Funciones: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Tablas temporales globales
- Sintaxis relacionada con la configuración del servidor relativa al hardware: memoria, subprocesos de trabajo, afinidad de CPU, marcas de seguimiento, etc. Usar en su lugar niveles de servicio.
- `HAS_DBACCESS`
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE` y nombres de cuatro partes
- [Integración de CLR de .NET Framework con SQL Server](http://msdn.microsoft.com/library/ms254963.aspx)
- Búsqueda semántica
- Credenciales de servidor Use credenciales con ámbito de base de datos en su lugar.
- Elementos de nivel de servidor: roles de servidor, `IS_SRVROLEMEMBER`, `sys.login_token`. `GRANT`, `REVOKE` y `DENY` de los permisos de nivel de servidor no están disponibles, aunque algunos se sustituyen por permisos en el nivel de base de datos. Algunos DMV útiles de nivel de servidor cuentan con DMV equivalentes de nivel de base de datos.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Opciones de `sp_configure` y `RECONFIGURE`. Algunas opciones están disponibles mediante [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- Auditoría de SQL Server. Use la auditoría de Base de datos SQL en su lugar.
- Seguimiento de SQL Server
- Marcas de seguimiento. Algunos elementos de la marca de seguimiento se han trasladado a los modos de compatibilidad.
- Depuración de Transact-SQL
- Desencadenadores: desencadenadores LOGON o con ámbito de servidor
- Instrucción `USE`: para cambiar el contexto de base de datos a una base de datos distinta, debe crear una nueva conexión a la base de datos nueva.

## <a name="full-transact-sql-reference"></a>Referencia completa de Transact-SQL
Para obtener más información sobre la gramática de Transact-SQL, su uso y ejemplos, consulte [Referencia de Transact-SQL (motor de base de datos)](https://msdn.microsoft.com/library/bb510741.aspx) en Libros en pantalla de SQL Server. 

### <a name="about-the-applies-to-tags"></a>Acerca de las etiquetas "Se aplica a"
La referencia de Transact-SQL incluye temas relacionados con las versiones de SQL Server que abarcan desde la de 2008 hasta la actual. Debajo del título del tema hay un icono de barras que enumera las cuatro plataformas de SQL Server e indica la aplicabilidad. Por ejemplo, los grupos de disponibilidad se introdujeron en SQL Server 2012. El tema [CREATE AVAILABILTY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) indica que la instrucción se aplica a **SQL Server (desde 2012)**. La instrucción no se aplica a SQL Server 2008, SQL Server 2008 R2, Base de datos SQL de Azure, Almacenamiento de datos SQL de Azure o Almacenamiento de datos paralelos.

En algunos casos, la línea general de un tema se puede utilizar en un producto, pero existen diferencias poco significativas entre los productos. Las diferencias se indican en los puntos medios del tema según corresponda.

