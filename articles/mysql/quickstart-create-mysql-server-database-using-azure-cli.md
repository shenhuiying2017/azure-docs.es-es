---
title: "Inicio rápido: Creación de una Base de datos de Azure para el servidor MySQL: CLI de Azure | Microsoft Docs"
description: "En esta guía de inicio rápido se describe cómo usar la CLI de Azure para crear una Base de datos de Azure para el servidor MySQL en un grupo de recursos de Azure."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 11/29/2017
ms.custom: mvc
ms.openlocfilehash: aca5d33adda703f3cd50e940ee43bb0624e179a1
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>Creación de una Base de datos de Azure para el servidor MySQL con la CLI de Azure
En esta guía de inicio rápido se describe cómo usar la CLI de Azure para crear una Base de datos de Azure para el servidor MySQL en un grupo de recursos de Azure en unos cinco minutos. La CLI de Azure se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este artículo es preciso que ejecute la versión 2.0 o posterior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

Si tiene varias suscripciones, elija la adecuada donde se encuentre el recurso o para la cual se facture. Seleccione un identificador de suscripción específico en su cuenta mediante el comando [az account set](/cli/azure/account#az_account_set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Cree un [grupo de recursos de Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) con el comando [az group create](/cli/azure/group#az_group_create). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran recursos de Azure como un grupo.

En el ejemplo siguiente, se crea un grupo de recursos denominado `myresourcegroup` en la ubicación `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Creación de un servidor de Azure Database for MySQL
Cree un servidor de Azure Database for MySQL con el comando **[az mysql server create](/cli/azure/mysql/server#az_mysql_server_create)**. Un servidor puede administrar varias bases de datos. Normalmente se usa una base de datos independiente para cada proyecto o para cada usuario.

En el ejemplo siguiente se crea una Base de datos de Azure para el servidor MySQL situado en `westus` en el grupo de recursos `myresourcegroup` con el nombre `myserver4demo`. El servidor tiene un inicio de sesión de administrador con el nombre `myadmin` y la contraseña `Password01!`. El servidor se crea con un nivel de rendimiento **Básico** y **50** unidades de proceso compartidas entre todas las bases de datos en el servidor. Puede escalar o reducir verticalmente el proceso y el almacenamiento según las necesidades de la aplicación.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name myserver4demo --location westus --admin-user myadmin --admin-password Password01! --performance-tier Basic --compute-units 50
```

## <a name="configure-firewall-rule"></a>Configurar de la regla de firewall
Cree una regla de firewall de nivel de servidor de Azure Database for MySQL con el comando **[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create)**. Una regla de firewall de nivel de servidor permite que una aplicación externa, como la herramienta de línea de comandos **mysql.exe** o MySQL Workbench, se conecte al servidor a través del firewall del servicio Azure MySQL. 

En el ejemplo siguiente se crea una regla de firewall para un intervalo de direcciones predefinidas que, en este ejemplo, es todo el intervalo de direcciones IP posible.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server myserver4demo --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
## <a name="configure-ssl-settings"></a>Configuración de SSL
De manera predeterminada, se exigen conexiones SSL entre su servidor y las aplicaciones cliente. Así se garantiza de forma predeterminada la seguridad de los datos "en movimiento" al cifrar el flujo de datos que circula por Internet. Para hacer que esta guía de inicio rápido sea más fácil, se han deshabilitado las conexiones SSL para su servidor. Esto no se recomienda en servidores de producción. Para más información, consulte [Configuración de la conectividad SSL en la aplicación para conectarse de forma segura a Azure Database for MySQL](./howto-configure-ssl.md).

En el ejemplo siguiente se deshabilita la aplicación de SSL en su servidor MySQL.
 
 ```azurecli-interactive
 az mysql server update --resource-group myresourcegroup --name myserver4demo --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Obtención de la información de conexión

Para conectarse al servidor, debe proporcionar las credenciales de acceso y la información del host.

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name myserver4demo
```

El resultado está en formato JSON. Tome nota de los valores de **fullyQualifiedDomainName** y **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "myserver4demo.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/myserver4demo",
  "location": "westus",
  "name": "myserver4demo",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "MYSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>Conexión al servidor mediante la herramienta mysql.exe de la línea de comandos
Conéctese al servidor mediante la herramienta **mysql.exe** de la línea de comandos. Puede descargarlo desde [aquí](https://dev.mysql.com/downloads/) e instalarlo en su equipo. En su lugar, también puede hacer clic en el botón **Pruébelo** de los códigos de ejemplo o en el botón `>_` de la barra de herramientas situada en la parte superior derecha de Azure Portal e iniciar **Azure Cloud Shell**.

Escriba los comandos siguientes: 

1. Conéctese al servidor con la herramienta de la línea de comandos **mysql**:
```azurecli-interactive
 mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
```

2. Ver el estado del servidor:
```sql
 mysql> status
```
Si todo se ha realizado correctamente, la herramienta de la línea de comandos debe mostrar el siguiente texto:

```dos
C:\Users\>mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.26.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Win64 (x86_64)

Connection id:          65512
Current database:
Current user:           myadmin@116.230.243.143
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             myserver4demo.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 days 9 hours 47 min 20 sec

Threads: 4  Questions: 34833  Slow queries: 2  Opens: 84  Flush tables: 4  Open tables: 1  Queries per second avg: 0.167
--------------

mysql>
```

> [!TIP]
> Para otros comandos, consulte el [capítulo 4.5.1 del Manual de referencia de MySQL 5.7](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Conexión al servidor con la herramienta MySQL Workbench de la GUI
1.  Inicie la aplicación MySQL Workbench en el equipo cliente. Puede descargar e instalar MySQL Workbench desde [aquí](https://dev.mysql.com/downloads/workbench/).

2.  En el cuadro de diálogo **Setup New Connection** (Establecer nueva conexión), escriba la siguiente información en la pestaña **Parámetros**:

   ![Configuración de una conexión nueva](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

| **Configuración** | **Valor sugerido** | **Descripción** |
|---|---|---|
|   Nombre de la conexión | Mi conexión | Especifique una etiqueta para esta conexión (puede ser cualquier cosa) |
| Método de conexión | elija Estándar (TCP/IP) | Use el protocolo TCP/IP para conectarse a Azure Database for MySQL> |
| Nombre de host. | myserver4demo.mysql.database.azure.com | Nombre del servidor que anotó anteriormente. |
| Port | 3306 | Se usa el puerto predeterminado para MySQL. |
| Nombre de usuario | myadmin@myserver4demo | El inicio de sesión de administrador del servidor que anotó anteriormente. |
| Password | **** | Use la contraseña de la cuenta de administrador que configuró anteriormente. |

Haga clic en **Probar conexión** para probar si todos los parámetros están configurados correctamente.
Ahora puede hacer clic en la conexión para conectarse correctamente al servidor.

## <a name="clean-up-resources"></a>Limpieza de recursos
Si no necesita estos recursos para otra guía de inicio rápido o tutorial, puede eliminarlos con el siguiente comando: 

```azurecli-interactive
az group delete --name myresourcegroup
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Diseño de una base de datos MySQL con la CLI de Azure](./tutorial-design-database-using-cli.md)
