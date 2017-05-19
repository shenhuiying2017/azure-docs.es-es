---
title: "Configuración y acceso a los registros del servidor para PostgreSQL con la CLI de Azure | Microsoft Docs"
description: "Describe cómo configurar y acceder a los registros del servidor en Azure Database for PostgreSQL."
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
ms.openlocfilehash: dc047e6d5194035586931b3cacc4d5cd07f6cfc2
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Configuración y acceso a los registros del servidor con la CLI de Azure
Puede enumerar y descargar los registros de error del servidor de Azure PostgreSQL mediante la interfaz de la línea de comandos (CLI de Azure). Sin embargo, no se admite el acceso a los registros de transacciones. 

## <a name="prerequisites"></a>Requisitos previos
Para seguir esta guía, necesitará:
- Un [servidor de Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md).
- La utilidad de línea de comandos [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) instalada.

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Configuración del registro para Azure Database for PostgreSQL
Puede configurar el servidor para tener acceso a los registros de consulta y los registros de error. Los registros de error pueden contener información de vaciado automático, conexión y puntos de comprobación.
1. Active el registro.
2. Actualice los valores de log\_statement y log\_min\_duration\_statement para habilitar el registro de consultas.
3. Actualice el período de retención.

Consulte cómo [personalizar los parámetros de configuración de servidor](howto-configure-server-parameters-using-cli.md) para más información.

## <a name="list-logs-for-azure-postgresql-server"></a>Lista de los registros del servidor de Azure PostgreSQL
Para obtener una lista de los archivos de registro disponibles para el servidor, ejecute el comando **az postgres server-logs**, tal y como se muestra en el ejemplo siguiente:
```azurecli
az postgres server-logs list --resource-group <resource group name> --server <server name> [ --file-last-written --filename-contains --max-file-size ]
```
Por ejemplo, puede enumerar los archivos de registro para el servidor de Azure PostgreSQL **mypgserver.postgres.database.azure.com** en el Grupo de recursos **myresourcegroup**, y dirigirlo a un archivo de texto denominado **log\_files\_list.txt. **
```azurecli
az postgres server-logs list --resource-group **myresourcegroup** --server **mypgserver** > log\_files\_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Descarga de registros del servidor en el sistema local
También puede descargar archivos de registro individuales del servidor de Azure PostgreSQL. 
```azurecli
az postgres server-logs download –name <log file name> --resource-group <resource group name> --server <server name>
```
En este ejemplo se descarga el archivo de registro específico para el servidor de Azure PostgreSQL **mypgserver.postgres.database.azure.com** en el Grupo de recursos **myresourcegroup** a su entorno local.
```azurecli
az postgres server-logs download --name 20170414-mypgserver-postgresql.log --resource-group **myresourcegroup** --server **mypgserver**
```
## <a name="next-steps"></a>Pasos siguientes
- Para más información acerca de los registros del servidor, consulte [Registros del servidor en Azure Database for PostgreSQL](concepts-server-logs.md).
- Para más información sobre los parámetros del servidor, consulte cómo [personalizar los parámetros de configuración del servidor mediante la CLI de Azure](howto-configure-server-parameters-using-cli.md).
