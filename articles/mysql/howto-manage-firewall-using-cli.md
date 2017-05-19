---
title: "Creación y administración de reglas de firewall de Azure Database for MySQL mediante la CLI de Azure | Microsoft Docs"
description: "Describe cómo crear y administrar reglas de firewall de Azure Database for MySQL mediante la CLI de Azure."
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
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: d7ff4ae8dbf9610b843c8b48d83b0f3c23ac72d5
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-using-azure-cli"></a>Creación y administración de reglas de firewall de Azure Database for MySQL mediante la CLI de Azure
Las reglas de firewall de nivel de servidor permiten a los administradores administrar el acceso a un servidor de Azure Database for MySQL desde una dirección IP o desde un intervalo de direcciones IP especificado. Con los comandos de la CLI de Azure adecuados, puede crear, actualizar, eliminar, enumerar y mostrar reglas de firewall para administrar el servidor. Para obtener información general sobre los firewalls de Azure Database for MySQL, consulte [Reglas de firewall de un servidor de Azure Database for MySQL](./concepts-firewall-rules.md).

## <a name="prerequisites"></a>Requisitos previos
* [Instalación de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)
* Instalación del SDK de Python para Azure para los servicios PostgreSQL y MySQL
* Instalación del componente de la CLI de Azure para los servicios PostgreSQL y MySQL
* Creación de un servidor de Azure Database for MySQL

## <a name="firewall-rule-commands"></a>Comandos de reglas de firewall:
El comando **az mysql server firewall-rule** se utiliza desde la CLI de Azure para crear, eliminar, enumerar, mostrar y actualizar reglas de firewall.

Comandos:
- **create**: crear una regla de firewall de servidor de Azure para MySQL.
- **delete**: eliminar una regla de firewall de servidor de Azure para MySQL.
- **list**: enumerar las reglas de firewall de servidor de Azure para MySQL.
- **show**: mostrar los detalles de una regla de firewall de servidor de Azure para MySQL.
- **update**: actualizar una regla de firewall de servidor de Azure para MySQL.

## <a name="login-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Inicio de sesión en Azure y lista de los servidores de Azure Database for MySQL
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
5. Si no está seguro de los nombres, muestre los servidores de Azure Database for MySQL para su suscripción y grupo de recursos.
```azurecli
az mysql server list --resource-group myResourceGroup
```
Anote el valor del atributo name de la lista, que se usará para especificar con qué servidor MySQL desea trabajar. Si es necesario, confirme los detalles de dicho servidor con el atributo name para confirmar que el nombre es correcto:
```azurecli
az mysql server show --resource-group myResourceGroup --name mysqlserver4demo
```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Lista de reglas de firewall en el servidor de Azure Database for MySQL 
Utilizando el nombre del servidor y el nombre del grupo de recursos, enumere las reglas de firewall existentes en el servidor. Tenga en cuenta que el atributo de nombre de servidor se especifica en el modificador **--server** y no en el modificador **--name**.
```azurecli
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo
```
La salida mostrará las reglas existentes, de forma predeterminada en formato JSON. Puede usar la opción **--output table** para obtener una salida en formato de tabla más legible.
```azurecli
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo --output table
```
## <a name="create-firewall-rule-on-azure-database-for-mysql-server"></a>Creación de reglas de firewall en el servidor de Azure Database for MySQL
Con el nombre del servidor de Azure MySQL y el nombre del grupo de recursos, cree una nueva regla de firewall en el servidor. Dé un nombre a la regla y las direcciones IP inicial y final para designar un intervalo de direcciones IP al que se permitirá el acceso.
```azurecli
az mysql server firewall-rule create --resource-group myResourceGroup  --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
Para permitir el acceso a una única dirección IP, proporcione la misma dirección como dirección IP de inicio y de final, como en el ejemplo.
```azurecli
az mysql server firewall-rule create --resource-group myResourceGroup  
--server mysql --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
Cuando se realiza correctamente, la salida del comando mostrará los detalles de la regla de firewall que ha creado, de forma predeterminada en formato JSON. Si se produce un error, la salida mostrará el texto del mensaje de error.

## <a name="update-firewall-rule-on-azure-database-for-mysql-server"></a>Actualización de reglas de firewall en el servidor de Azure Database for MySQL 
Con el nombre del servidor de Azure MySQL y el nombre del grupo de recursos, actualice una regla de firewall existente en el servidor. Proporcione como entrada el nombre de una regla de firewall existente y los atributos de dirección IP de inicio y final que se van a actualizar.
```azurecli
az mysql server firewall-rule update --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Cuando se realiza correctamente, la salida del comando mostrará los detalles de la regla de firewall que ha actualizado, de forma predeterminada en formato JSON. Si se produce un error, la salida mostrará el texto del mensaje de error.

> [!NOTE]
> Si la regla de firewall no existe, el comando update la creará.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Presentación de los detalles de las reglas de firewall en el servidor de Azure Database for MySQL
Con el nombre del servidor de Azure MySQL y el nombre del grupo de recursos, muestre los detalles de la regla de firewall existente en el servidor. Proporcione como entrada el nombre de una regla de firewall existente.
```azurecli
az mysql server firewall-rule show --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
Cuando se realiza correctamente, la salida del comando mostrará los detalles de la regla de firewall que ha especificado, de forma predeterminada en formato JSON. Si se produce un error, la salida mostrará el texto del mensaje de error.

## <a name="delete-firewall-rule-on-azure-database-for-mysql-server"></a>Eliminación de reglas de firewall en el servidor de Azure Database for MySQL
Con el nombre del servidor de Azure MySQL y el nombre del grupo de recursos, quite una regla de firewall existente del servidor. Proporcione el nombre de una regla de firewall existente.
```azurecli
az mysql server firewall-rule delete --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
Cuando se realiza correctamente, no hay ninguna salida. En caso de error, se devolverá el texto del mensaje de error.

## <a name="next-steps"></a>Pasos siguientes
- Para más información, consulte [Reglas de firewall de servidor de Azure Database for MySQL](./concepts-firewall-rules.md).
- [Creación y administración de reglas de firewall de Azure Database for MySQL mediante Azure Portal](./howto-manage-firewall-using-portal.md)
