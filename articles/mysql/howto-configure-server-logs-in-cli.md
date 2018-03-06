---
title: Acceso a registros de servidor en Azure Database for MySQL mediante la CLI de Azure
description: "En este artículo se describe cómo acceder a los registros de servidor de Azure Database for MySQL mediante la utilidad de línea de comandos de la CLI de Azure."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 8cd83722569eef503030b7e7438a73209cb812d6
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Configuración y acceso a los registros del servidor con la CLI de Azure
Puede descargar los registros de servidor de Azure Database for MySQL mediante la CLI de Azure, la utilidad de línea de comandos de Azure.

## <a name="prerequisites"></a>requisitos previos
Para seguir esta guía, necesitará:
- [Servidor de Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- La [CLI de Azure 2.0](/cli/azure/install-azure-cli) o usar Azure Cloud Shell en el explorador.

## <a name="configure-logging-for-azure-database-for-mysql"></a>Configuración del registro para Azure Database for MySQL
Puede configurar el servidor para acceder al registro de consultas lentas de MySQL.
1. Active el registro estableciendo el parámetro **slow\_query\_log** en ON.
2. Ajuste otros parámetros, como **long\_query\_time** y **log\_slow\_admin\_statements**.

Consulte [Cómo configurar parámetros del servidor](howto-configure-server-parameters-using-cli.md) para aprender a establecer el valor de estos parámetros mediante la CLI de Azure.

Por ejemplo, el siguiente comando de la CLI activará el registro de consultas lentas, establecerá el tiempo de consultas largas en 10 segundos y desactivará el registro de la instrucción de administración lenta. Por último, se muestran las opciones de configuración para su revisión.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Enumeración de registros del servidor de Azure Database for MySQL
Para mostrar la lista de archivos de registro disponibles para el servidor, ejecute el comando [az mysql server-logs list](/cli/azure/mysql/server-logs#az_mysql_server_logs_list).

Puede enumerar los archivos de registro del servidor **mydemoserver.mysql.database.azure.com** en el grupo de recursos **myresourcegroup**, y dirigirlo a un archivo de texto denominado **log\_files\_list.txt.**
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Descarga de registros del servidor
El comando [az mysql server-logs download](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) le permite descargar archivos de registro individuales para su servidor. 

En este ejemplo se descarga el archivo de registro específico para el servidor **mydemoserver.mysql.database.azure.com** en el grupo de recursos **myresourcegroup** a su entorno local.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Pasos siguientes
- Más información sobre [los registros del servidor de Azure Database for MySQL](concepts-server-logs.md)
