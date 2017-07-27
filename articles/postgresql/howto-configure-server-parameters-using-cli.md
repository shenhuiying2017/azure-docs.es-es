---
title: "Configuración de los parámetros del servicio en Azure Database para PostgreSQL | Microsoft Docs"
description: "En este artículo se describe cómo configurar los parámetros de servicio de Azure Database for PostgreSQL mediante la línea de comandos de la CLI de Azure."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 9a575148a05843bef7524eff61407b377292ca3b
ms.contentlocale: es-es
ms.lasthandoff: 06/16/2017

---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Personalización de los parámetros de configuración del servidor con la CLI de Azure
Puede enumerar, mostrar y actualizar los parámetros de configuración de un servidor de Azure PostgreSQL con la interfaz de la línea de comandos (CLI de Azure). Sin embargo, en el nivel del servidor, solo se expone y se puede modificar un subconjunto de las opciones de configuración del motor. 

## <a name="prerequisites"></a>Requisitos previos
Para seguir esta guía, necesitará:
- Un servidor y una base de datos [Creación de una instancia de Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Instale la utilidad de línea de comandos [CLI de Azure 2.0](/cli/azure/install-azure-cli) o use Azure Cloud Shell en el explorador.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Lista de los parámetros de configuración del servidor de Azure Database for PostgreSQL
Para obtener una lista de todos los parámetros modificables en un servidor y sus valores, ejecute el comando [az postgres server configuration list](/cli/azure/postgres/server/configuration#list).

Puede enumerar los parámetros de configuración del servidor **mypgserver-20170401.postgres.database.azure.com** en el grupo de recursos **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="show-server-configuration-parameter-details"></a>Presentación de los detalles de los parámetros de configuración del servidor
Para mostrar los detalles de un parámetro de configuración específico de un servidor, ejecute el comando [az postgres server configuration show](/cli/azure/postgres/server/configuration#show).

En este ejemplo se muestran detalles del parámetro de configuración **log\_min\_messages** del servidor **mypgserver-20170401.postgres.database.azure.com** en el grupo de recursos **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="modify-server-configuration-parameter-value"></a>Modificación del valor de los parámetros de configuración del servidor
También puede modificar el valor de un determinado parámetro de configuración del servidor; esta acción actualizará el valor de configuración subyacente del motor del servidor de PostgreSQL. Para actualizar la configuración, use el comando [az postgres server configuration set](/cli/azure/postgres/server/configuration#set). 

Para actualizar el parámetro de configuración **log\_min\_messages** del servidor **mypgserver-20170401.postgres.database.azure.com** en el grupo de recursos **myresourcegroup.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401 --value INFO
```
Si desea restablecer el valor de un parámetro de configuración, basta con no incluir el parámetro opcional `--value` y el servicio aplicará el valor predeterminado. En el ejemplo anterior, sería:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401
```
La configuración de **log\_min\_messages** se restablecerá al valor predeterminado **WARNING**. Para más información sobre la configuración del servidor y los valores permitidos, consulte la documentación de PostgreSQL en [Server Configuration](https://www.postgresql.org/docs/9.6/static/runtime-config.html) (Configuración del servidor).

## <a name="next-steps"></a>Pasos siguientes
- Para configurar y obtener acceso a los registros del servidor, consulte [Registros del servidor en Azure Database for PostgreSQL](concepts-server-logs.md).

