---
title: "Configuración de los parámetros del servicio en Azure Database para PostgreSQL | Microsoft Docs"
description: "En este artículo se describe cómo configurar los parámetros de servicio de Azure Database for PostgreSQL mediante la línea de comandos de la CLI de Azure."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: a2a3395fad35599591e093385305f28bbb161014
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Personalización de los parámetros de configuración del servidor con la CLI de Azure
Puede enumerar, mostrar y actualizar los parámetros de configuración de un servidor Azure PostgreSQL con la interfaz de la línea de comandos (CLI de Azure). Sin embargo, en el nivel del servidor, solo se expone y se puede modificar un subconjunto de las opciones de configuración del motor. 

## <a name="prerequisites"></a>Requisitos previos
Para seguir esta guía, necesitará:
- Crear una base de datos y un servidor Azure Database for PostgreSQL siguiendo las instrucciones de [Creación de una instancia de Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Instalar la interfaz de la línea de comandos [CLI de Azure 2.0](/cli/azure/install-azure-cli) en su máquina o usar [Azure Cloud Shell](../cloud-shell/overview.md) en Azure Portal mediante el explorador.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Lista de los parámetros de configuración del servidor de Azure Database for PostgreSQL
Para obtener una lista de todos los parámetros modificables en un servidor y sus valores, ejecute el comando [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list).

Puede enumerar los parámetros de configuración del servidor **mypgserver-20170401.postgres.database.azure.com** en el grupo de recursos **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="show-server-configuration-parameter-details"></a>Presentación de los detalles de los parámetros de configuración del servidor
Para mostrar los detalles de un parámetro de configuración específico de un servidor, ejecute el comando [az postgres server configuration show](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_show).

En este ejemplo se muestran detalles del parámetro de configuración **log\_min\_messages** del servidor **mypgserver-20170401.postgres.database.azure.com** en el grupo de recursos **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="modify-server-configuration-parameter-value"></a>Modificación del valor de los parámetros de configuración del servidor
También puede modificar el valor de un determinado parámetro de configuración del servidor; esta acción actualizará el valor de configuración subyacente del motor del servidor PostgreSQL. Para actualizar la configuración, use el comando [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set). 

Para actualizar el parámetro de configuración **log\_min\_messages** del servidor **mypgserver-20170401.postgres.database.azure.com** en el grupo de recursos **myresourcegroup.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401 --value INFO
```
Si desea restablecer el valor de un parámetro de configuración, basta con no incluir el parámetro opcional `--value` y el servicio aplicará el valor predeterminado. En el ejemplo anterior, sería:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401
```
La configuración de **log\_min\_messages** se restablecerá al valor predeterminado **WARNING**. Para obtener más información sobre la configuración del servidor y los valores permitidos, consulte la documentación de PostgreSQL en [Server Configuration](https://www.postgresql.org/docs/9.6/static/runtime-config.html) (Configuración del servidor).

## <a name="next-steps"></a>Pasos siguientes
- Para configurar y obtener acceso a los registros del servidor, consulte [Registros del servidor en Azure Database for PostgreSQL](concepts-server-logs.md).
