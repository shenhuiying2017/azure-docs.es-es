---
title: "Configuración de los parámetros del servicio en Azure Database for MySQL"
description: "En este artículo se describe cómo configurar los parámetros de servicio de Azure Database for MySQL mediante la utilidad de línea de comandos de la CLI de Azure."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 9caf6b164f2433ab3c1b701554f562211cc2de80
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Personalización de los parámetros de configuración del servidor con la CLI de Azure
Puede enumerar, mostrar y actualizar los parámetros de configuración de un servidor de Azure Database for MySQL con la CLI de Azure, la utilidad de línea de comandos de Azure. En el nivel del servidor, se expone y se puede modificar un subconjunto de las opciones de configuración del motor. 

## <a name="prerequisites"></a>requisitos previos
Para seguir esta guía, necesitará:
- [Un servidor de Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- Utilidad de línea de comandos [CLI de Azure 2.0](/cli/azure/install-azure-cli) o use Azure Cloud Shell en el explorador.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Lista de los parámetros de configuración del servidor de Azure Database for MySQL
Para obtener una lista de todos los parámetros modificables en un servidor y sus valores, ejecute el comando [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list).

Puede enumerar los parámetros de configuración del servidor **mydemoserver.mysql.database.azure.com** en el grupo de recursos **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Para ver la definición de cada uno de los parámetros enumerados, consulte la sección de referencia de MySQL en [Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html) (Variables del sistema del servidor).

## <a name="show-server-configuration-parameter-details"></a>Presentación de los detalles de los parámetros de configuración del servidor
Para mostrar los detalles de un parámetro de configuración específico de un servidor, ejecute el comando [az mysql server configuration show](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show).

En este ejemplo se muestran detalles del parámetro de configuración **slow\_query\_log** del servidor **mydemoserver.mysql.database.azure.com** en el grupo de recursos **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Modificación de un valor de los parámetros de configuración del servidor
También puede modificar el valor de un determinado parámetro de configuración del servidor; esta acción actualizará el valor de configuración subyacente del motor del servidor de MySQL. Para actualizar la configuración, use el comando [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set). 

Para actualizar el parámetro de configuración **slow\_query\_log** del servidor **mydemoserver.mysql.database.azure.com** en el grupo de recursos **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Si desea restablecer el valor de un parámetro de configuración, omita el parámetro opcional `--value` y el servicio aplicará el valor predeterminado. Para el ejemplo anterior, sería:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Este código restablece la configuración **slow\_query\_log** en el valor predeterminado **Apagado**. 

## <a name="next-steps"></a>Pasos siguientes
- Cómo configurar [parámetros del servidor en Azure Portal](howto-server-parameters.md)