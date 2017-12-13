---
title: "Administración de credenciales en la biblioteca de cliente de bases de datos elásticas | Microsoft Docs"
description: "Cómo configurar el nivel correcto de las credenciales (de administrador a solo lectura) de las aplicaciones de bases de datos elásticas."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: 72e0edaf-795e-4856-84a5-6594f735fb7e
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: ddove
ms.openlocfilehash: 0f266f3be8bf8c1699b3b19bea96c83d32f1bd69
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Credenciales usadas para acceder a la biblioteca de cliente de Elastic Database
La [biblioteca de cliente de Elastic Database](sql-database-elastic-database-client-library.md) usa tres variantes de credenciales para acceder al [administrador de mapas de particiones](sql-database-elastic-scale-shard-map-management.md). Según lo que necesite, use la credencial con el menor nivel de acceso posible.

* **Credenciales de administración**: se usan para crear o manipular un administrador de mapas de particiones. (Consulte el [glosario](sql-database-elastic-scale-glossary.md)). 
* **Credenciales de acceso**: se usan para obtener acceso a un administrador de mapas de particiones ya existente, para así obtener información acerca de las particiones.
* **Credenciales de conexión**: se usan para conectarse a las particiones. 

Consulte, asimismo, [Administrar bases de datos e inicios de sesión en Azure SQL Database](sql-database-manage-logins.md). 

## <a name="about-management-credentials"></a>Acerca de las credenciales de administración
Las credenciales de administración se usan para crear un objeto **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)) para aquellas aplicaciones que manipulan mapas de particiones. (Por ejemplo, vea [Incorporación de una partición con herramientas de Elastic Database](sql-database-elastic-scale-add-a-shard.md) y [Enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md)). El usuario de la biblioteca de cliente de escalado elástico crea los usuarios e inicios de sesión de SQL necesarios; asimismo, debe asegurarse de que estos tienen permisos de lectura y escritura para poder usar la base de datos de mapa de particiones global y todas las bases de datos de particiones. Estas credenciales se usan para mantener el mapa de particiones global y los mapas de particiones locales cuando se realizan cambios en el mapa de particiones. Por ejemplo, use las credenciales de administración para crear un objeto de Shard Map Manager (mediante **GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.getsqlshardmapmanager), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)): 

```
// Obtain a shard map manager. 
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy); 
```

La variable **smmAdminConnectionString** es una cadena de conexión que contiene las credenciales de administración. El identificador de usuario y la contraseña proporcionan acceso de lectura y escritura a la base de datos de mapa de particiones y a particiones individuales. La cadena de conexión de administración también incluye el nombre del servidor y el nombre de la base de datos para identificar la base de datos de mapa de particiones global. Esta es una cadena de conexión típica para ese fin:

```
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 
```

No use valores con el formato "username@server"; use simplemente el valor con formato "nombreUsuario".  Le recomendamos esto, ya que las credenciales deben funcionar tanto con la base de datos del administrador de mapas de particiones, como con las particiones individuales, las cuales pueden estar en distintos servidores.

## <a name="access-credentials"></a>Credenciales de acceso
Si desea crear un administrador de mapas de particiones en una aplicación que no va a administrar mapas de particiones, use credenciales que tengan permisos de solo lectura en el mapa de particiones global. La información recuperada del mapa de particiones global que tenga estas credenciales se usa para el [enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md) y para rellenar la caché del mapa de particiones del cliente. Las credenciales se proporcionan a través del mismo patrón de llamada al elemento **GetSqlShardMapManager**: 

```
// Obtain shard map manager. 
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Recuerde que puede usar el elemento **smmReadOnlyConnectionString** para reflejar la utilización de diferentes credenciales de este acceso en nombre de usuarios **que no sean administradores**: estas credenciales no deben proporcionar permiso de escritura para el mapa de particiones global. 

## <a name="connection-credentials"></a>Credenciales de conexión
Cuando se usa el método **OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._list_shard_mapper.openconnectionforkey), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)) para acceder a una partición asociada a una clave de particionamiento, se necesitan credenciales adicionales. Estas credenciales deben proporcionar los permisos de acceso de solo lectura a las tablas de mapa de particiones local que residen en la partición. Esto es necesario para realizar la validación de la conexión para el enrutamiento dependiente de los datos en la partición. Este fragmento de código le permitirá obtener acceso a los datos, según el contexto de enrutamiento dependiente de los datos: 

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 
```

En este ejemplo, el elemento **smmUserConnectionString** contiene la cadena de conexión para las credenciales de usuario. En el caso de Base de datos SQL de Azure, la siguiente es una cadena de conexión típica para las credenciales de usuario: 

```
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Al igual que con las credenciales de administración, no use valores que tengan el formato "username@server". En su lugar, use aquellos que tengan el formato "username".  Tenga en cuenta también que la cadena de conexión no contiene un nombre de servidor y de base de datos. Esto se debe a que la llamada **OpenConnectionForKey** dirige automáticamente la conexión a la partición correcta según la clave. Por lo tanto, no se proporcionan ni el nombre de la base de datos ni el del servidor. 

## <a name="see-also"></a>Consulte, asimismo, 
[Administrar bases de datos e inicios de sesión en Azure SQL Database](sql-database-manage-logins.md)

[Protección de SQL Database](sql-database-security-overview.md)

[Introducción a trabajos de Elastic Database](sql-database-elastic-jobs-getting-started.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

