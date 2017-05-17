---
title: "Diseño de la primera base de datos de Azure Database for MySQL: CLI de Azure | Microsoft Docs"
description: "En este tutorial se explica cómo crear y administrar la base de datos y el servidor de Azure Database for MySQL con la CLI de Azure 2.0."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 99238b9c0bcc7fa80e8c72cda25f7ed809cc5195
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="design-your-first-azure-database-for-mysql-database"></a>Diseño de la primera base de datos de Azure Database for MySQL

Azure Database for MySQL es un servicio de base de datos relacional de Microsoft Cloud basado en el motor de base de datos de MySQL Community Edition. En este tutorial, usa la CLI (interfaz de la línea de comandos) de Azure y otras utilidades para aprender a hacer lo siguiente:

> [!div class="checklist"]
> * Creación de una instancia de Azure Database for MySQL
> * Configuración del firewall del servidor Uso de la [herramienta de línea de comandos de mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para crear una base de datos
> * Carga de datos de ejemplo
> * Datos de consulta
> * Actualización de datos
> * Restauración de datos

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla.

```azurecli
az login
```
Siga las instrucciones del símbolo del sistema para abrir la dirección URL https://aka.ms/devicelog en el explorador y, luego, escriba el código generado en el **símbolo del sistema**.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Cree un [grupo de recursos](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) con el comando [az group create](https://docs.microsoft.com/cli/azure/group#create). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran recursos de Azure como un grupo.

En el ejemplo siguiente, se crea un grupo de recursos denominado `mycliresource` en la ubicación `westus`.

```azurecli
az group create --name mycliresource --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Creación de un servidor de Azure Database for MySQL
Cree un servidor de Azure Database for MySQL con el comando az mysql server create. Un servidor puede administrar varias bases de datos. Habitualmente se usa una base de datos independiente para cada proyecto o para cada usuario.

En el ejemplo siguiente se crea un servidor de Azure Database for MySQL en `westus` en el grupo de recursos `mycliresource` con el nombre `mycliserver`. El servidor tiene un inicio de sesión de administrador con el nombre `myadmin` y la contraseña `Password01!`. El servidor se crea con un nivel de rendimiento **Básico** y **50** unidades de proceso compartidas entre todas las bases de datos en el servidor. Puede escalar o reducir verticalmente el proceso y el almacenamiento según las necesidades de la aplicación.

```azurecli
az mysql server create --resource-group mycliresource --name mycliserver
--location westus --user myadmin --password Password01!
--performance-tier Basic --compute-units 50
```

## <a name="configure-firewall-rule"></a>Configurar de la regla de firewall
Cree una regla de firewall de nivel de servidor de Azure Database for MySQL con el comando az mysql server firewall-rule create. Una regla de firewall de nivel de servidor permite que una aplicación externa, como una herramienta de línea de comandos de **mysql** o MySQL Workbench para conectarse al servidor a través del firewall del servicio Azure MySQL. 

En el ejemplo siguiente se crea una regla de firewall para un intervalo predefinido de direcciones. En este ejemplo se muestra todo el posible intervalo de direcciones IP.

```azurecli
az mysql server firewall-rule create --resource-group mycliresource
--server mycliserver --name AllowYourIP --start-ip-address 0.0.0.0
--end-ip-address 255.255.255.255
```

## <a name="get-the-connection-information"></a>Obtención de la información de conexión

Para conectarse al servidor, debe proporcionar las credenciales de acceso y la información del host.
```azurecli
az mysql server show --resource-group mycliresource --name mycliserver
```

El resultado está en formato JSON. Tome nota de los valores de **fullyQualifiedDomainName** y **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mycliserver.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mycliresource/providers/Microsoft.DBforMySQL/servers/mycliserver",
  "location": "westus",
  "name": "mycliserver",
  "resourceGroup": "mycliresource",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "MYSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

## <a name="connect-to-the-server-using-mysql"></a>Conexión al servidor con MySQL
Use la [herramienta de línea de comandos mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para establecer una conexión con el servidor de Azure Database for MySQL. En este ejemplo, el comando es:
```cmd
mysql -h mycliserver.database.windows.net -u myadmin@mycliserver -p
```

## <a name="create-a-blank-database"></a>Crear una base de datos en blanco
Una vez conectado al servidor, cree una base de datos vacía.
```sql
mysql> CREATE DATABASE mysampledb;
```

En el símbolo del sistema, ejecute el comando siguiente para cambiar la conexión a esta base de datos recién creada:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Creación de tablas en la base de datos
Ahora que sabe cómo conectarse a la base de datos de Azure Database for MySQL, podemos ver cómo completar algunas tareas básicas.

En primer lugar, podemos crear una tabla y cargarla con algunos datos. Vamos a crear una tabla que almacena la información del inventario.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Carga de datos en las tablas
Ahora que tenemos una tabla, podemos insertar algunos datos en ella. En la ventana de símbolo del sistema abierta, ejecute la consulta siguiente para insertar algunas filas de datos.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Ahora tiene dos filas de datos de ejemplo en la tabla que creó anteriormente.

## <a name="query-and-update-the-data-in-the-tables"></a>Consulta y actualización de los datos en las tablas
Ejecute la consulta siguiente para recuperar información de la tabla de base de datos.
```sql
SELECT * FROM inventory;
```

También puede actualizar los datos en las tablas.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

La fila se actualiza en consecuencia cuando se recuperan los datos.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restauración de una base de datos a un momento anterior en el tiempo
Imagine que eliminó accidentalmente esta tabla. No se puede recuperar con facilidad. Azure Database for MySQL permite volver a cualquier momento dado en el período de los últimos 35 días y restaurar este momento en un servidor nuevo. Puede usar este servidor nuevo para recuperar los datos eliminados. Los pasos siguientes restauran el servidor de ejemplo a un punto antes de que se agregara la tabla.

Para realizar la restauración, necesita la información siguiente:

- Punto de restauración: seleccione el momento antes de que se modificara la base de datos. Debe ser mayor o igual que el valor de la copia de seguridad más antigua de la base de datos de origen.
- Servidor de destino: especifique el nombre del nuevo servidor donde desea restaurar
- Servidor de origen: especifique el nombre del servidor desde donde desea restaurar
- Ubicación: no se puede seleccionar la región; de forma predeterminada, es la misma que la del servidor de origen
- Plan de tarifa: no se puede cambiar este valor al restaurar un servidor. Es el mismo que el del servidor de origen. 

```azurecli
az mysql server restore --resource-group mycliresource --name mycliserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mycliserver
```

Para restaurar el servidor y [restaurar a un momento dado](./howto-restore-server-portal.md) antes de que se eliminara la tabla. Restaurar un servidor a un momento dado distinto crea un servidor nuevo duplicado como el servidor original a partir del momento dado que especifique, siempre que se encuentren dentro del período de retención para el [nivel de servicio](./concepts-service-tiers.md).

## <a name="next-steps"></a>Pasos siguientes
En este tutorial aprendió lo siguiente:
> [!div class="checklist"]
> * Creación de una instancia de Azure Database for MySQL
> * Configuración del firewall del servidor Uso de la [herramienta de línea de comandos de mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para crear una base de datos
> * Carga de datos de ejemplo
> * Datos de consulta
> * Actualización de datos
> * Restauración de datos

[Ejemplos de la CLI de Azure para Azure Database for MySQL](./sample-scripts-azure-cli.md)

