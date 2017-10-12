---
title: "Creación y administración de reglas de firewall de Azure Database for PostgreSQL mediante la CLI de Azure | Microsoft Docs"
description: "En este artículo se describe cómo crear y administrar reglas de firewall de Azure Database for PostgreSQL mediante la línea de comandos de Azure."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.openlocfilehash: 6f081416dd7d78f0153b3fda21a340a8c1a70c5f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Creación y administración de reglas de firewall de Azure Database for PostgreSQL mediante la CLI de Azure
Las reglas de firewall de nivel de servidor permiten a los administradores administrar el acceso a un servidor de Azure Database for PostgreSQL desde una dirección IP o desde un intervalo de direcciones IP especificado. Con los comandos de la CLI de Azure adecuados, puede crear, actualizar, eliminar, enumerar y mostrar reglas de firewall para administrar el servidor. Para obtener información general sobre los firewalls de Azure Database for PostgreSQL, consulte [Reglas de firewall de un servidor de Azure Database for PostgreSQL](concepts-firewall-rules.md)

## <a name="prerequisites"></a>Requisitos previos
Para seguir esta guía, necesitará:
- Un [servidor y una base de datos de Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md).
- Instalar la utilidad de línea de comandos [Azure CLI 2.0](/cli/azure/install-azure-cli) o usar Azure Cloud Shell en el explorador.

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Configuración de reglas de firewall para Azure Database for PostgreSQL
Los comandos [az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) se usan para configurar reglas de firewall.

## <a name="list-firewall-rules"></a>Enumerar reglas de firewall 
Para enumerar las reglas de firewall de servidor existentes en el servidor, ejecute el comando [az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule#list).
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401
```
La salida enumera las reglas existentes, si existen, en formato JSON de forma predeterminada. Puede usar el modificador `--output table` para obtener una salida en formato de tabla más legible.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401 --output table
```
## <a name="create-firewall-rule"></a>Crear reglas de firewall
Para crear una regla de firewall en el servidor, ejecute el comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#create). 

En este ejemplo se permite a un rango de todas las direcciones IP tener acceso al servidor **mypgserver-20170401.postgres.database.azure.com**
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup  --server mypgserver-20170401 --name "AllowIpRange" --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
Para permitir el acceso a una única dirección IP, proporcione la misma dirección como dirección IP inicial y final, como en el ejemplo.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup  
--server mypgserver-20170401 --name "AllowSingleIpAddress" --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Si se realiza correctamente, la salida del comando muestra los detalles de la regla de firewall que ha creado, de forma predeterminada en formato JSON. Si se produce un error, la salida muestra el texto del mensaje de error.

## <a name="update-firewall-rule"></a>Actualizar reglas de firewall 
Se puede actualizar una regla de firewall existente en el servidor con el comando [az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule#update). Proporcione como entrada el nombre de una regla de firewall existente y los atributos de dirección IP de inicio y final que se van a actualizar.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.255
```
Cuando se realiza correctamente, la salida del comando muestra los detalles de la regla de firewall que ha actualizado, de forma predeterminada en formato JSON. Si se produce un error, la salida muestra el texto del mensaje de error.
> [!NOTE]
> Si la regla de firewall no existe, el comando update la crea.

## <a name="show-firewall-rule-details"></a>Mostrar los detalles de la regla de firewall
También puede mostrar los detalles de la regla de firewall existente para un servidor mediante la ejecución del comando [az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule#show).
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange"
```
Cuando se realiza correctamente, la salida del comando muestra los detalles de la regla de firewall que ha especificado, de forma predeterminada en formato JSON. Si se produce un error, la salida muestra el texto del mensaje de error.

## <a name="delete-firewall-rule"></a>Eliminar reglas de firewall
Para revocar el acceso de un intervalo IP desde el servidor, elimine una regla de firewall existente mediante la ejecución del comando [az postgres server firewall-rule delete](/cli/azure/postgres/server/firewall-rule#delete). Proporcione el nombre de una regla de firewall existente.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange"
```
Cuando se realiza correctamente, no hay ninguna salida. En caso de error, se devuelve el texto del mensaje de error.

## <a name="next-steps"></a>Pasos siguientes
- De forma similar, puede utilizar un explorador web para [crear y administrar reglas de firewall de Azure Database for PostgreSQL mediante Azure Portal](howto-manage-firewall-using-portal.md).
- Para más información, consulte [Reglas de firewall de servidor de Azure Database for PostgreSQL](concepts-firewall-rules.md).
- Para obtener ayuda para la conexión a un servidor de Azure Database for PostgreSQL, consulte [Bibliotecas de conexión para Azure Database for PostgreSQL](concepts-connection-libraries.md).
