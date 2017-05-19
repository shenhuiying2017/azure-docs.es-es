---
title: "Creación y administración de reglas de firewall de Azure Database for PostgreSQL mediante la CLI de Azure | Microsoft Docs"
description: "Describe cómo crear y administrar reglas de firewall de Azure Database for PostgreSQL mediante la CLI de Azure."
services: postgresql
author: jasonwhowell
ms.author: jasonh
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
ms.openlocfilehash: 5cb9a3e1fe6e5477e399fd7148fc5366f76cea38
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Creación y administración de reglas de firewall de Azure Database for PostgreSQL mediante la CLI de Azure
Las reglas de firewall de nivel de servidor permiten a los administradores administrar el acceso a un servidor de Azure Database for PostgreSQL desde una dirección IP o desde un intervalo de direcciones IP especificado. Con los comandos de la CLI de Azure adecuados, puede crear, actualizar, eliminar, enumerar y mostrar reglas de firewall para administrar el servidor. Para obtener información general sobre los firewalls de Azure Database for PostgreSQL, consulte [Reglas de firewall de un servidor de Azure Database for PostgreSQL](concepts-firewall-rules.md)

## <a name="prerequisites"></a>Requisitos previos
Para seguir esta guía, necesitará:
- Un [servidor y una base de datos de Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md).
- La utilidad de línea de comandos [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) instalada.

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Configuración de reglas de firewall para Azure Database for PostgreSQL
El comando **az postgres server firewall-rule** se utiliza desde la CLI de Azure para crear, eliminar, enumerar, mostrar y actualizar reglas de firewall. Este 

## <a name="login-to-azure-and-list-servers"></a>Inicio de sesión en Azure y lista de servidores
Conecte de forma segura con la CLI de Azure con su cuenta de Azure. Use el comando **az login** para hacerlo.
1. Ejecute el siguiente comando desde la línea de comando.
```azurecli
az login
```
Este comando generará un código que se usará en el paso siguiente.

2. Use un explorador web para abrir la página [https://aka.ms/devicelogin](https://aka.ms/devicelogin) y escriba el código.

3. En el mensaje, inicie sesión con sus credenciales de Azure.

4. Una vez que se autoriza su inicio de sesión, se imprimirá una lista de suscripciones en la consola.
Copie el identificador de la suscripción deseada para establecer que la suscripción actual se usará a partir de ahora.
```azurecli
az account set --subscription {your subscription id}
```
5. Si no está seguro de los nombres, muestre los servidores de Azure Database for PostgreSQL para su suscripción y grupo de recursos.

```azurecli
az postgres server list --resource-group myresourcegroup
```
Anote el valor del atributo name de la lista, que se usará para especificar con qué servidor PostgreSQL desea trabajar. Si es necesario, confirme los detalles de dicho servidor con el atributo name para confirmar que el nombre es correcto:

```azurecli
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

## <a name="list-firewall-rules"></a>Lista de reglas de firewall 
Utilizando el nombre del servidor y el nombre del grupo de recursos, enumere las reglas de firewall existentes en el servidor. Tenga en cuenta que el atributo de nombre de servidor se especifica en el modificador **--server** y no en el modificador **--name**.
```azurecli
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401
```
La salida mostrará las reglas existentes, de forma predeterminada en formato JSON. Puede usar la opción **--output table** para obtener una salida en formato de tabla más legible.
```azurecli
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401 --output table
```
## <a name="create-firewall-rule"></a>Creación de reglas de firewall
Con el nombre del servidor de Azure Database for PostgreSQL y el nombre del grupo de recursos, cree una nueva regla de firewall en el servidor. Dé un nombre a la regla y las direcciones IP inicial y final para designar un intervalo de direcciones IP al que se permitirá el acceso.
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup  --server mypgserver-20170401 --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
Para permitir el acceso a una única dirección IP, proporcione la misma dirección como dirección IP de inicio y de final, como en el ejemplo.
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup  
--server mypgserver-20170401 --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
Cuando se realiza correctamente, la salida del comando mostrará los detalles de la regla de firewall que ha creado, de forma predeterminada en formato JSON. Si se produce un error, la salida mostrará el texto del mensaje de error.

## <a name="update-firewall-rule"></a>Actualización de reglas de firewall 
Con el nombre del servidor de Azure Database for PostgreSQL y el nombre del grupo de recursos, actualice una regla de firewall existente en el servidor. Proporcione como entrada el nombre de una regla de firewall existente y los atributos de dirección IP de inicio y final que se van a actualizar.
```azurecli
az postgres server firewall-rule update --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Cuando se realiza correctamente, la salida del comando mostrará los detalles de la regla de firewall que ha actualizado, de forma predeterminada en formato JSON. Si se produce un error, la salida mostrará el texto del mensaje de error.
> [!NOTE]
> Si la regla de firewall no existe, el comando update la creará.

## <a name="show-firewall-rule-details"></a>Presentación de los detalles de la regla de firewall
Con el nombre del servidor de Azure Database for PostgreSQL y el nombre del grupo de recursos, muestre los detalles de la regla de firewall existente en el servidor. Proporcione como entrada el nombre de una regla de firewall existente.
```azurecli
az postgres server firewall-rule show --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1"
```
Cuando se realiza correctamente, la salida del comando mostrará los detalles de la regla de firewall que ha especificado, de forma predeterminada en formato JSON. Si se produce un error, la salida mostrará el texto del mensaje de error.

## <a name="delete-firewall-rule"></a>Eliminación de reglas de firewall
Con el nombre del servidor de Azure Database for PostgreSQL y el nombre del grupo de recursos, quite una regla de firewall existente del servidor. Proporcione el nombre de una regla de firewall existente.
```azurecli
az postgres server firewall-rule delete --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1"
```
Cuando se realiza correctamente, no hay ninguna salida. En caso de error, se devolverá el texto del mensaje de error.

## <a name="next-steps"></a>Pasos siguientes
- De forma similar, puede utilizar un explorador web para [crear y administrar reglas de firewall de Azure Database for PostgreSQL mediante Azure Portal](howto-manage-firewall-using-portal.md).
- Para más información, consulte [Reglas de firewall de servidor de Azure Database for PostgreSQL](concepts-firewall-rules.md).
- Para obtener ayuda para la conexión a un servidor de Azure Database for PostgreSQL, consulte [Bibliotecas de conexión para Azure Database for PostgreSQL](concepts-connection-libraries.md).

