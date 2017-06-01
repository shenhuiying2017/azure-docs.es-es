---
title: "Inicio rápido: Creación de una Base de datos de Azure para el servidor MySQL: CLI de Azure | Microsoft Docs"
description: "En esta guía de inicio rápido se describe cómo usar la CLI de Azure para crear una Base de datos de Azure para el servidor MySQL en un grupo de recursos de Azure."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: portal
ms.workload: 
ms.date: 05/24/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 9f78163e4ff1166a2abd94150d686256ee338286
ms.contentlocale: es-es
ms.lasthandoff: 05/25/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>Creación de una Base de datos de Azure para el servidor MySQL con la CLI de Azure
En esta guía de inicio rápido se describe cómo usar la CLI de Azure para crear una Base de datos de Azure para el servidor MySQL en un grupo de recursos de Azure en unos cinco minutos. La CLI de Azure se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts.

Para completar esta guía de inicio rápido, asegúrese de que ha instalado la versión más reciente de [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.
Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla.

```azurecli
az login
```
Siga las instrucciones del símbolo del sistema para abrir https://aka.ms/devicelog en el explorador y, después, escriba el código generado en el **símbolo del sistema**.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Cree un [grupo de recursos de Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) con el comando [az group create](https://docs.microsoft.com/cli/azure/group#create). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran recursos de Azure como un grupo.

En el ejemplo siguiente, se crea un grupo de recursos denominado `mycliresource` en la ubicación `westus`.

```azurecli
az group create --name mycliresource --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Creación de una Base de datos de Azure para el servidor MySQL
Cree una Base de datos de Azure para el servidor MySQL con el comando **az mysql server create**. Un servidor puede administrar varias bases de datos. Normalmente se usa una base de datos independiente para cada proyecto o para cada usuario.

En el ejemplo siguiente se crea una Base de datos de Azure para el servidor MySQL situado en `westus` en el grupo de recursos `mycliresource` con el nombre `mycliserver`. El servidor tiene un inicio de sesión de administrador con el nombre `myadmin` y la contraseña `Password01!`. El servidor se crea con un nivel de rendimiento **Básico** y **50** unidades de proceso compartidas entre todas las bases de datos en el servidor. Puede escalar o reducir verticalmente el proceso y el almacenamiento según las necesidades de la aplicación.

```azurecli
az mysql server create --resource-group mycliresource --name mycliserver --location westus --admin-user myadmin --admin-password Password01! --performance-tier Basic --compute-units 50
```

![Creación de una Base de datos de Azure para el servidor MySQL con la CLI de Azure](./media/quickstart-create-mysql-server-database-using-azure-cli/3_az-mysq-server-create.png)

## <a name="configure-firewall-rule"></a>Configurar la regla de firewall
Cree una regla de firewall de nivel de servidor de Base de datos de Azure para MySQL con el comando **az mysql server firewall-rule create**. Una regla de firewall de nivel de servidor permite que una aplicación externa, como la herramienta de línea de comandos **mysql.exe** o MySQL Workbench, se conecte al servidor a través del firewall del servicio Azure MySQL. 

En el ejemplo siguiente se crea una regla de firewall para un intervalo de direcciones predefinidas que, en este ejemplo, es todo el intervalo de direcciones IP posible.

```azurecli
az mysql server firewall-rule create --resource-group mycliresource --server mycliserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
## <a name="configure-ssl-settings"></a>Configuración de SSL
De manera predeterminada, se exigen conexiones SSL entre su servidor y las aplicaciones cliente.  Esto garantiza la seguridad de los datos "en movimiento" cifrando el flujo de datos que circula por Internet.  Para facilitar este inicio rápido, deshabilitaremos las conexiones SSL para su servidor.  Esto no se recomienda en servidores de producción.  Para obtener más información, vea [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Configuración de la conectividad SSL en la aplicación para la conexión segura a la Base de datos de Azure para MySQL).

En el ejemplo siguiente se deshabilita la aplicación de SSL en su servidor MySQL.
 
 ```azurecli
 az mysql server update --resource-group mycliresource --name mycliserver -g -n --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Obtención de la información de conexión

Para conectarse al servidor, debe proporcionar las credenciales de acceso y la información del host.

```azurecli
az mysql server show --resource-group mycliresource --name mycliserver
```

El resultado está en formato JSON. Tome nota de los valores de **fullyQualifiedDomainName** y **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mycliserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mycliresource/providers/Microsoft.DBforMySQL/servers/mycliserver",
  "location": "westus",
  "name": "mycliserver",
  "resourceGroup": "mycliresource",
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
Para conectarse a su servidor con la herramienta de la línea de comandos **mysql.exe**, asegúrese de que tiene la instalación de MySQL en su equipo.  Puede descargar MySQL de [aquí](https://dev.mysql.com/downloads/).

Abra el símbolo del sistema y escriba lo siguiente: 

1. Conéctese al servidor con la herramienta de la línea de comandos **mysql**:
```dos
 mysql -h mycliserver.mysql.database.azure.com -u myadmin@mycliserver -p
```

2. Ver el estado del servidor:
```dos
 mysql> status
```
Si todo se ha realizado correctamente, la herramienta de la línea de comandos debe mostrar lo siguiente:

```dos
C:\Users\v-chenyh>mysql -h mycliserver.mysql.database.azure.com -u myadmin@mycliserver -p
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
Connection:             mycliserver.mysql.database.azure.com via TCP/IP
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
> Para otros comandos, vea el [capítulo 4.5.1 del Manual de referencia de MySQL 5.6](https://dev.mysql.com/doc/refman/5.6/en/mysql.html).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Conexión al servidor con la herramienta MySQL Workbench de la GUI
1.    Inicie la aplicación MySQL Workbench en el equipo cliente. Puede descargar e instalar MySQL Workbench desde [aquí](https://dev.mysql.com/downloads/workbench/).

2.    En el cuadro de diálogo **Setup New Connection** (Establecer nueva conexión), escriba la siguiente información en la pestaña **Parámetros**:

| **Parámetros** | **Descripción** |
|----------------|-----------------|
|    *Nombre de la conexión* | especifique un nombre para esta conexión (puede ser cualquier cosa) |
| *Método de conexión* | elija Estándar (TCP/IP) |
| *Nombre de host* | mycliserver.mysql.database.azure.com (nombre del servidor que anotó anteriormente) |
| *Puerto* | 3306 |
| *Nombre de usuario* | myadmin@mycliserver (inicio de sesión de administrador del servidor que anotó anteriormente) |
| *Password* | Guarde la contraseña de la cuenta de administrador |

   ![Configuración de una conexión nueva](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

Haga clic en **Probar conexión** para probar si todos los parámetros están configurados correctamente.
Ahora puede hacer clic en la conexión que acaba de crear para conectarse correctamente al servidor.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no necesita estos recursos para otra guía de inicio rápido o tutorial, puede eliminarlos de la siguiente manera: 

```azurecli
az group delete --name mycliresource
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Diseño de una base de datos MySQL con la CLI de Azure](./tutorial-design-database-using-cli.md)

