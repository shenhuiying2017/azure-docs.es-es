---
title: "Configuración y acceso a los registros del servidor para PostgreSQL con la CLI de Azure | Microsoft Docs"
description: "En este artículo se describe cómo configurar los registros de servidor de Azure Database for PostgreSQL, y obtener acceso a ellos, mediante la línea de comandos de la CLI de Azure."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.openlocfilehash: 26f8e12c493904f722cad5191ee053feff20f7fc
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Configuración y acceso a los registros del servidor con la CLI de Azure
Puede descargar los registros de error del servidor de PostgreSQL mediante la interfaz de la línea de comandos (CLI de Azure). Sin embargo, no se admite el acceso a los registros de transacciones. 

## <a name="prerequisites"></a>Requisitos previos
Para seguir esta guía, necesitará:
- Un [servidor de Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md).
- Instale la utilidad de línea de comandos [CLI de Azure 2.0](/cli/azure/install-azure-cli) o use Azure Cloud Shell en el explorador.

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Configuración del registro para Azure Database for PostgreSQL
Puede configurar el servidor para tener acceso a los registros de consulta y los registros de error. Los registros de error pueden contener información de vaciado automático, conexión y puntos de comprobación.
1. Active el registro.
2. Actualice los valores de log\_statement y log\_min\_duration\_statement para habilitar el registro de consultas.
3. Actualice el período de retención.

Consulte cómo [personalizar los parámetros de configuración del servidor](howto-configure-server-parameters-using-cli.md) para más información.

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Enumeración de registros del servidor de Azure Database for PostgreSQL
Para mostrar la lista de archivos de registro disponibles para el servidor, ejecute el comando [az postgres server-logs list](/cli/azure/postgres/server-logs#list).

Puede enumerar los archivos de registro del servidor **mypgserver.postgres.database.azure.com** en el grupo de recursos **myresourcegroup**, y dirigirlo a un archivo de texto denominado **log\_files\_list.txt.**
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mypgserver-20170401 > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Descarga de registros del servidor en el sistema local
El comando [az postgres server-logs download](/cli/azure/postgres/server-logs#download) le permite descargar archivos de registro individuales para su servidor. 

En este ejemplo se descarga el archivo de registro específico para el servidor **mypgserver-20170401.postgres.database.azure.com** en el grupo de recursos **myresourcegroup** a su entorno local.
```azurecli-interactive
az postgres server-logs download --name 20170414-mypgserver-20170401-postgresql.log --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="next-steps"></a>Pasos siguientes
- Para más información acerca de los registros del servidor, consulte [Registros del servidor en Azure Database for PostgreSQL](concepts-server-logs.md).
- Para más información sobre los parámetros del servidor, consulte cómo [personalizar los parámetros de configuración del servidor mediante la CLI de Azure](howto-configure-server-parameters-using-cli.md).
