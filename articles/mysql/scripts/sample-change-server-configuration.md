---
title: 'Script de la CLI de Azure: cambio de configuraciones del servidor | Microsoft Docs'
description: Este script de la CLI de ejemplo enumera todas las configuraciones del servidor disponibles y actualiza el valor de innodb_lock_wait_timeout.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 08/29/2017
ms.openlocfilehash: 558e7d4ee96a3424a777f9f57679da25f2306efd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Enumeración y actualización de configuraciones de un servidor de Azure Database for MySQL mediante la CLI de Azure
Este script de la CLI de ejemplo enumera todos los parámetros de configuración disponibles, así como sus valores permitidos para el servidor de Azure Database for MySQL y establece *innodb_lock_wait_timeout* en un valor distinto del predeterminado.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tema es preciso que ejecute la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de ejemplo
En este script de ejemplo, cambie las líneas resaltadas para personalizar el nombre de usuario de administrador y la contraseña.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación
Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos y todos los recursos asociados.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicación del script
Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| **Comando** | **Notas** |
|---|---|
| [az group create](/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az mysql server create](/cli/azure/mysql/server#create) | Crea un servidor MySQL que hospeda las bases de datos. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#list) | Enumeración de las configuraciones de un servidor de Azure Database for MySQL. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#set) | Actualice la configuración de un servidor de Azure Database for MySQL. |
| [az mysql server configuration show](/cli/azure/mysql/server/configuration#show) | Muestre la configuración de un servidor de Azure Database for MySQL. |
| [az group delete](/cli/azure/group#delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes
- Para más información sobre la CLI de Azure: [Documentación de la CLI de Azure](/cli/azure/overview).
- Pruebe scripts adicionales en: [Ejemplos de la CLI de Azure para Azure Database for MySQL](../sample-scripts-azure-cli.md).
- Para obtener más información sobre los parámetros del servidor, consulte [Cómo configurar parámetros del servidor en Azure Database for MySQL](../howto-server-parameters.md).
