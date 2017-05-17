---
title: Ejemplos de la CLI de Azure para crear un solos servidor de Azure Database for MySQL y configurar una regla de firewall | Microsoft Docs
description: Este script de la CLI de ejemplo crea un servidor de Azure Database for MySQL (Base de datos de Azure para MySQL) y configura una regla de firewall de nivel de servidor.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.custom: sample
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fa961f123dddc2e6243d1efbd1d72b994179eb79
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Cree un servidor MySQL y configure una regla de firewall mediante la CLI de Azure
Este script de la CLI de ejemplo crea un servidor de Azure Database for MySQL (Base de datos de Azure para MySQL) y configura una regla de firewall de nivel de servidor. Una vez que el script se ejecuta correctamente, todos los servicios de Azure y la dirección IP configurada pueden tener acceso al servidor MySQL.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de ejemplo
```bash
#!/bin/bash

# Create a resource group
az group create \
--name myresource \
--location westus

# Create a MySQL server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mysql server create \
--name mysqlserver4demo \
--resource-group myresource \
--location westus \
--admin-user myadmin \
--admin-password <server_admin_password> \
--performance-tier Basic \
--compute-units 50 \

# Configure a firewall rule for the server
# The ip address range that you want to allow to access your server
az mysql server firewall-rule create \
--resource-group myresource \
--server mysqlserver4demo \
--name AllowIps \
--start-ip-address 0.0.0.0 \
--end-ip-address 255.255.255.255

# Default database ‘postgres’ gets created on the server.
```

## <a name="clean-up-deployment"></a>Limpieza de la implementación
 Después de ejecutarse el script de ejemplo, use el comando siguiente para quitar el grupo de recursos y todos los recursos asociados a él.
```azurecli
az group delete --name myresource
```
## <a name="script-explanation"></a>Explicación del script
Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| **Comando** | **Notas** |
|-------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [az group create](https://docs.microsoft.com/en-us/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| az mysql server create | Crea un servidor MySQL que hospeda las bases de datos. |
| az mysql server firewall create | Crea una regla de firewall para permitir el acceso al servidor y las bases de datos de este desde el intervalo de direcciones IP especificado. |
| [az group delete](https://docs.microsoft.com/en-us/cli/azure/resource#delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes
[Ejemplos de la CLI de Azure para Azure Database for MySQL](../sample-scripts-azure-cli.md)

