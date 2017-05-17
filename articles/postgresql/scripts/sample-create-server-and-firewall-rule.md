---
title: "Script de la CLI de Azure: creación de Azure Database for PostgreSQL (Base de datos de Azure para PostgreSQL) | Microsoft Docs"
description: 'Ejemplo de script de la CLI de Azure: crea un servidor de Azure Database for PostgreSQL (Base de datos de Azure para PostgreSQL) y configura una regla de firewall de nivel de servidor.'
services: postgresql
author: salonisonpal
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
ms.openlocfilehash: 0f61358aa655a44ac7982c80eb1d796667c47bf4
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="create-an-azure-database-for-postgresql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Creación de una base de datos de Azure para el servidor PostgreSQL y configuración de una regla de firewall mediante la CLI de Azure
Este script de la CLI de ejemplo crea un servidor de Azure Database for PostgreSQL (Base de datos de Azure para PostgreSQL) y configura una regla de firewall de nivel de servidor. Después de ejecutar el script correctamente, el servidor PostgreSQL es accesible desde todos los servicios de Azure y la dirección IP configurada.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de ejemplo
```azurecli
#!/bin/bash

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a PostgreSQL server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az postgres server create \
--name mypgserver-20170401 \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--performance-tier Basic \
--compute-units 50 \

# Configure a firewall rule for the server
# The ip address range that you want to allow to access your server
az postgres server firewall-rule create \
--resource-group myresourcegroup \
--server mypgserver-20170401 \
--name AllowIps \
--start-ip-address 0.0.0.0 \
--end-ip-address 255.255.255.255

# Default database ‘postgres’ gets created on the server.
```

## <a name="clean-up-deployment"></a>Limpieza de la implementación
Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos y todos los recursos asociados.
```azurecli
az group delete --name myresourcegroup
```

## <a name="next-steps"></a>Pasos siguientes
- Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).
- Encontrará más ejemplos de scripts de la CLI de Azure Database for PostgreSQL (Base de datos de Azure para PostgreSQL) en la [documentación sobre Azure Database for PostgreSQL (Base de datos de Azure para PostgreSQL)](../sample-scripts-azure-cli.md).

