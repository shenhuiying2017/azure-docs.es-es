---
title: "Configuración de los parámetros del servicio en Azure Database para PostgreSQL | Microsoft Docs"
description: "Describe cómo configurar los parámetros del servicio en Azure Database para PostgreSQL."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7e46e3d588782ec0f2ef89c58001e997b8fc2910
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Personalización de los parámetros de configuración del servidor con la CLI de Azure
Puede enumerar, mostrar y actualizar los parámetros de configuración de un servidor de Azure PostgreSQL con la interfaz de la línea de comandos (CLI de Azure). Sin embargo, en el nivel del servidor, solo se expone y se puede modificar un subconjunto de las opciones de configuración del motor. 

## <a name="prerequisites"></a>Requisitos previos
Para seguir esta guía, necesitará:
- Un servidor y una base de datos [Creación de una instancia de Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md)
- La utilidad de línea de comandos [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) instalada.

## <a name="list-server-configuration-parameters-for-azure-postgresql-server"></a>Lista de los parámetros de configuración del servidor de Azure PostgreSQL
Para obtener una lista de todos los parámetros modificables de un servidor de Azure PostgreSQL y sus valores, ejecute el siguiente comando **az postgres server configuration**, tal y como se muestra aquí:
> az postgres server configuration list --resource-group <resource group name> --server <server name>

Por ejemplo, puede enumerar los parámetros de configuración del servidor de Azure PostgreSQL **mypgserver.postgres.database.azure.com** en el grupo de recursos **myresourcegroup**.
```azurecli
az postgres server configuration list --resource-group myresourcegroup --server mypgserver
```
## <a name="show-server-configuration-parameter-details"></a>Presentación de los detalles de los parámetros de configuración del servidor
Para mostrar los detalles de un parámetro de configuración específico de un servidor de Azure PostgreSQL, ejecute el comando **az postgres server configuration**.
```azurecli
az postgres server configuration show --name <configuration name>
   --resource-group <resource group name> --server <server name>
```
Por ejemplo, puede mostrar los detalles del parámetro de configuración **log\_min\_messages** del servidor de Azure PostgreSQL **mypgserver.postgres.database.azure.com** en el grupo de recursos **myresourcegroup. **
```azurecli
az postgres server configuration show --name log\_min\_messages --resource-group myresourcegroup --server mypgserver
```
## <a name="modify-server-configuration-parameter-value"></a>Modificación del valor de los parámetros de configuración del servidor
Puede modificar el valor de ciertos parámetros de configuración de un servidor de Azure PostgreSQL, y se actualiza el valor de configuración subyacente para el motor del servidor de PostgreSQL. Para actualizar el valor de configuración, ejecute el siguiente comando **az postgres server configuration**. 
```azurecli
az postgres server configuration update --name <configuration name>
   --resource-group <resource group name> --server <server name>[--value]
```
Por ejemplo, puede actualizar el parámetro de configuración **log\_min\_messages** del servidor de Azure PostgreSQL **mypgserver.postgres.database.azure.com** en el grupo de recursos **myresourcegroup. **
```azurecli
az postgres server configuration show --name log\_min\_messages 
   --resource-group myresourcegroup --server mypgserver --value INFO
```
Si desea restablecer el valor de un parámetro de configuración, basta con no incluir el parámetro opcional --value y el servicio aplicará el valor predeterminado. En el ejemplo anterior, sería:
```azurecli
az postgres server configuration show --name log\_min\_messages
   --resource-group myresourcegroup --server mypgserver
```
La configuración de log\_min\_messages se restablece a su valor predeterminado WARNING. Para más información sobre la configuración del servidor y los valores permitidos, consulte la documentación de PostgreSQL en [Server Configuration](https://www.postgresql.org/docs/9.6/static/runtime-config.html) (Configuración del servidor).

## <a name="next-steps"></a>Pasos siguientes
- Para configurar y obtener acceso a los registros del servidor, consulte [Registros del servidor en Azure Database for PostgreSQL](concepts-server-logs.md).
