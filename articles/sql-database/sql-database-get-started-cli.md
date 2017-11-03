---
title: "CLI de Azure: creación de una base de datos SQL | Microsoft Docs"
description: "Aprenda a crear un servidor lógico de SQL Database, una regla de firewall de nivel de servidor y bases de datos con la CLI de Azure."
keywords: "tutorial de base de datos SQL, creación de una base de datos SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 10/11/2017
ms.author: carlrab
ms.openlocfilehash: 5076a9fcbfb0c46a0aebf858d5ec1dcbee839946
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="create-a-single-azure-sql-database-using-the-azure-cli"></a>Creación de una sola instancia de Azure SQL Database con la CLI de Azure

La CLI de Azure se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts. En esta guía se detalla el uso de la CLI de Azure para implementar una instancia de Azure SQL Database en un [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) de un [servidor lógico de SQL Database](sql-database-features.md).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tema es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="define-variables"></a>Definición de variables

Defina variables para su uso en los scripts con esta guía de inicio rápido.

```azurecli-interactive
# The data center and resource name for your resources
export resourcegroupname = myResourceGroup
export location = westeurope
# The logical server name: Use a random value or replace with your own value (do not capitalize)
export servername = server-$RANDOM
# Set an admin login and password for your database
export adminlogin = ServerAdmin
export password = ChangeYourAdminPassword1
# The ip address range that you want to allow to access your DB
export startip = "0.0.0.0"
export endip = "0.0.0.0"
# The database name
export databasename = mySampleDatabase
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) con el comando [az group create](/cli/azure/group#az_group_create). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran recursos de Azure como un grupo. En el ejemplo siguiente, se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `westeurope`.

```azurecli-interactive
az group create --name $resourcegroupname --location $location
```
## <a name="create-a-logical-server"></a>un servidor lógico

Cree un [servidor lógico de Azure SQL Database](sql-database-features.md) con el comando [az sql server create](/cli/azure/sql/server#az_sql_server_create). Un servidor lógico contiene un conjunto de bases de datos administradas como un grupo. En el ejemplo siguiente se crea un servidor con nombre aleatorio en el grupo de recursos con un inicio de sesión de administrador denominado `ServerAdmin` y una contraseña `ChangeYourAdminPassword1`. Cambie estos valores predefinidos por los que prefiera.

```azurecli-interactive
az sql server create --name $servername --resource-group $resourcegroupname --location $location \
    --admin-user $adminlogin --admin-password $password
```

## <a name="configure-a-server-firewall-rule"></a>Configuración de una regla de firewall del servidor

Cree una [regla de firewall de nivel de servidor de Azure SQL Database](sql-database-firewall-configure.md) con el comando [az sql server firewall create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create). Una regla de firewall de nivel de servidor permite a una aplicación externa, como SQL Server Management Studio o la utilidad SQLCMD conectarse a una instancia de SQL Database a través del firewall del servicio de SQL Database. En el ejemplo siguiente, el firewall está abierto solo para otros recursos de Azure. Para habilitar la conectividad externa, cambie la dirección IP a una dirección apropiada para su entorno. Para abrir todas las direcciones IP, utilice 0.0.0.0 como la dirección IP inicial y 255.255.255.255 como la dirección final.  

```azurecli-interactive
az sql server firewall-rule create --resource-group $resourcegroupname --server $servername \
    -n AllowYourIp --start-ip-address $startip --end-ip-address $endip
```

> [!NOTE]
> SQL Database se comunica a través del puerto 1433. Si intenta conectarse desde dentro de una red corporativa, es posible que el firewall de la red no permita el tráfico de salida a través del puerto 1433. Si es así, no podrá conectarse al servidor de Azure SQL Database, a menos que el departamento de TI abra el puerto 1433.
>

## <a name="create-a-database-in-the-server-with-sample-data"></a>Creación de una base de datos en el servidor con datos de ejemplo

Cree una base de datos con un [nivel de rendimiento S0](sql-database-service-tiers.md) en el servidor con el comando [az sql db create](/cli/azure/sql/db#az_sql_db_create). En el ejemplo siguiente se crea una base de datos llamada `mySampleDatabase` y se cargan en ella los datos del ejemplo AdventureWorksLT. Estos valores predefinidos se pueden reemplazar si se desea (otras guías de inicio rápido de esta colección se basan en los valores de esta).

```azurecli-interactive
az sql db create --resource-group $resourcegroupname --server $servername \
    --name $databasename --sample-name AdventureWorksLT --service-objective S0
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Otras guías de inicio rápido de esta colección se basan en los valores de esta. 

> [!TIP]
> Si tiene previsto seguir trabajando con las siguientes guías de inicio rápido, no elimine los recursos creados en esta guía de inicio rápido. Si no tiene previsto continuar, siga estos pasos para eliminar todos los recursos creados por esta guía de inicio rápido en Azure Portal.
>

```azurecli-interactive
az group delete --name $resourcegroupname
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene una base de datos, puede conectarse y realizar consultas con las herramientas que desee. Para más información, seleccione una de las herramientas siguientes:

- [SQL Server Management Studio](sql-database-connect-query-ssms.md)
- [código de Visual Studio](sql-database-connect-query-vscode.md)
- [.NET](sql-database-connect-query-dotnet.md)
- [PHP](sql-database-connect-query-php.md)
- [Node.js](sql-database-connect-query-nodejs.md)
- [Java](sql-database-connect-query-java.md)
- [Python](sql-database-connect-query-python.md)
- [Ruby](sql-database-connect-query-ruby.md)

