---
title: "Diseño de la primera base de datos de Azure Database for MySQL con la CLI de Azure"
description: "En este tutorial se explica cómo crear y administrar la base de datos y el servidor de Azure Database for MySQL con la CLI de Azure 2.0 desde la línea de comandos."
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 02/28/2018
ms.custom: mvc
ms.openlocfilehash: a609bbdf70599d0cceaf988a9a0bef51bc28716d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="design-your-first-azure-database-for-mysql-database"></a>Diseño de la primera base de datos de Azure Database for MySQL

Azure Database for MySQL es un servicio de base de datos relacional de Microsoft Cloud basado en el motor de base de datos de MySQL Community Edition. En este tutorial, usa la CLI (interfaz de la línea de comandos) de Azure y otras utilidades para aprender a hacer lo siguiente:

> [!div class="checklist"]
> * Creación de una instancia de Azure Database for MySQL
> * Configuración del firewall del servidor
> * Uso de la [herramienta de línea de comandos mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para crear una base de datos
> * Carga de datos de ejemplo
> * Datos de consulta
> * Actualización de datos
> * Restauración de datos

Puede usar Azure Cloud Shell en el explorador, o bien [instalar la CLI de Azure 2.0]( /cli/azure/install-azure-cli) en su propio equipo para ejecutar los bloques de código de este tutorial.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este artículo es preciso que ejecute la versión 2.0 o posterior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

Si tiene varias suscripciones, elija la adecuada donde se encuentre el recurso o para la cual se facture. Seleccione un identificador de suscripción específico en su cuenta mediante el comando [az account set](/cli/azure/account#az_account_set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Cree un [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) con el comando [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran recursos de Azure como un grupo.

En el ejemplo siguiente, se crea un grupo de recursos denominado `myresourcegroup` en la ubicación `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
## <a name="add-the-extension"></a>Adición de la extensión
Agregue la extensión de administración de Azure Database for MySQL actualizada con el comando siguiente:
```azurecli-interactive
az extension add --name rdbms
``` 

Compruebe que tiene instalada la versión de la extensión correcta. 
```azurecli-interactive
az extension list
```

El JSON que se devuelva debe incluir lo siguiente: 
```json
{
    "extensionType": "whl",
    "name": "rdbms",
    "version": "0.0.3"
}
```

Si no se devuelve la versión 0.0.3, ejecute el siguiente procedimiento para actualizar la extensión: 
```azurecli-interactive
az extension update --name rdbms
```

## <a name="create-an-azure-database-for-mysql-server"></a>Creación de un servidor de Azure Database for MySQL
Cree un servidor de Azure Database for MySQL con el comando az mysql server create. Un servidor puede administrar varias bases de datos. Normalmente se usa una base de datos independiente para cada proyecto o para cada usuario.

En el ejemplo siguiente se crea una Base de datos de Azure para el servidor MySQL situado en `westus` en el grupo de recursos `myresourcegroup` con el nombre `mydemoserver`. El servidor tiene un inicio de sesión de administrador con el nombre `myadmin`. Se trata de un servidor Gen 4 de uso general con dos núcleos virtuales. Sustituya `<server_admin_password>` por su propio valor.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 5.7
```
> [!IMPORTANT]
> El inicio de sesión y la contraseña de administrador del servidor que especifique aquí serán necesarios para iniciar sesión más adelante en ese servidor y en las bases de datos que se especificarán en esta guía de inicio rápido. Recuerde o grabe esta información para su uso posterior.


## <a name="configure-firewall-rule"></a>Configurar de la regla de firewall
Cree una regla de firewall de nivel de servidor de Azure Database for MySQL con el comando az mysql server firewall-rule create. Una regla de firewall de nivel de servidor permite que una aplicación externa, como una herramienta de línea de comandos de **mysql** o MySQL Workbench para conectarse al servidor a través del firewall del servicio Azure MySQL. 

En el ejemplo siguiente se crea una regla de firewall para un intervalo predefinido de direcciones. En este ejemplo se muestra todo el posible intervalo de direcciones IP.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowAllIPs --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="get-the-connection-information"></a>Obtención de la información de conexión

Para conectarse al servidor, debe proporcionar las credenciales de acceso y la información del host.
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

El resultado está en formato JSON. Tome nota de los valores de **fullyQualifiedDomainName** y **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-mysql"></a>Conexión al servidor con MySQL
Use la [herramienta de línea de comandos mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para establecer una conexión con el servidor de Azure Database for MySQL. En este ejemplo, el comando es:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
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
Ahora que sabe cómo conectarse a la base de datos de Azure Database for MySQL, complete algunas tareas básicas.

En primer lugar, cree una tabla y cárguela con algunos datos. Vamos a crear una tabla que almacena la información del inventario.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Carga de datos en las tablas
Ahora que tiene una tabla, inserte algunos datos en ella. En la ventana de símbolo del sistema abierta, ejecute la consulta siguiente para insertar algunas filas de datos.
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

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

El comando `az mysql server restore` necesita los parámetros siguientes:
| Configuración | Valor sugerido | DESCRIPCIÓN  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Grupo de recursos en el que existe el servidor de origen.  |
| Nombre | mydemoserver-restored | Nombre del nuevo servidor que se crea mediante el comando de restauración. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Seleccione un momento dado en el que quiere restaurar. Esta fecha y hora debe estar dentro del período de retención de copia de seguridad del servidor de origen. Use el formato de fecha y hora ISO8601. Por ejemplo, puede usar su propia zona horaria local, como `2017-04-13T05:59:00-08:00`, o usar el formato de hora Zulú UTC `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | Nombre o identificador del servidor de origen desde el que se va a restaurar. |

Al restaurar un servidor a un momento dado, se crea un servidor que se copia como servidor original a un momento dado que especifique. Los valores de ubicación y plan de tarifa del servidor restaurado son los mismos que los del servidor de origen.

El comando es sincrónico y se devolverá después de que se haya restaurado el servidor. Una vez finalizada la restauración, busque el servidor que ha creado. Compruebe que los datos se han restaurado del modo esperado.

## <a name="next-steps"></a>Pasos siguientes
En este tutorial aprendió lo siguiente:
> [!div class="checklist"]
> * Creación de un servidor de Azure Database for MySQL
> * Configuración del firewall del servidor
> * Uso de la [herramienta de línea de comandos mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para crear una base de datos
> * Carga de datos de ejemplo
> * Datos de consulta
> * Actualización de datos
> * Restauración de datos

> [!div class="nextstepaction"]
> [Ejemplos de la CLI de Azure para Azure Database for MySQL](./sample-scripts-azure-cli.md)
