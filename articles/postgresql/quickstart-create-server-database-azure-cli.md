---
title: "Inicio rápido: Creación de un servidor de Azure Database for PostgreSQL con la CLI de Azure"
description: "Guía de inicio rápido para crear y administrar la base de datos de Azure para PostgreSQL mediante la CLI (interfaz de la línea de comandos) de Azure."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 02/28/2018
ms.custom: mvc
ms.openlocfilehash: 50bb3f8ca1032e704b9805beb54fbd4ea4f8e7c1
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="quickstart-create-an-azure-database-for-postgresql-using-the-azure-cli"></a>Inicio rápido: Creación de un servidor de Azure Database for PostgreSQL con la CLI de Azure
Azure Database for PostgreSQL es un servicio administrado que le permite ejecutar, administrar y escalar bases de datos de PostgreSQL de alta disponibilidad en la nube. La CLI de Azure se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts. En esta guía de inicio rápido se muestra cómo crear una base de datos de Azure para el servidor PostgreSQL en un [grupo de recursos de Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) mediante la CLI de Azure.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este artículo es preciso que ejecute la versión 2.0 o posterior de la CLI de Azure. Para ver la versión instalada, ejecute el comando `az --version`. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

Si ejecuta localmente la CLI, debe iniciar sesión en su cuenta mediante el comando [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in). Anote la propiedad **id** de la salida del comando para el nombre de la suscripción correspondiente.
```azurecli-interactive
az login
```

Si tiene varias suscripciones, elija la suscripción adecuada en la que se debe facturar el recurso. Seleccione el identificador de suscripción específico en su cuenta mediante el comando [az account set](/cli/azure/account#az_account_set). Sustituya la propiedad **id** de la salida **az login** para su suscripción en el marcador de posición de id. de suscripción.
```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) con el comando [az group create](/cli/azure/group#az_group_create). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran recursos de Azure como un grupo. Debe proporcionar un nombre único. En el ejemplo siguiente, se crea un grupo de recursos denominado `myresourcegroup` en la ubicación `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```
## <a name="add-the-extension"></a>Adición de la extensión
Agregue la extensión de administración actualizada de Azure Database for PostgreSQL con el comando siguiente:
```azurecli-interactive
az extension add --name rdbms
``` 
## <a name="create-an-azure-database-for-postgresql-server"></a>Creación de un servidor de Azure Database for PostgreSQL

Cree un [servidor de Azure Database for PostgreSQL](overview.md) con el comando [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create). Un servidor contiene un conjunto de bases de datos administradas como un grupo. 

En el ejemplo siguiente se crea un servidor llamado `mydemoserver` en la región Oeste de EE. UU., en el grupo de recursos `myresourcegroup` y con el inicio de sesión de administrador de servidor `myadmin`. Se trata de un servidor **Gen 4** de **Uso general** con 2 **Núcleos virtuales**. El nombre de un servidor se asigna al nombre DNS y, por tanto, debe ser único a nivel global en Azure. Sustituya `<server_admin_password>` por su propio valor.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 9.6
```

> [!IMPORTANT]
> El inicio de sesión y la contraseña de administrador del servidor que especifique aquí serán necesarios para iniciar sesión más adelante en ese servidor y en las bases de datos que se especificarán en esta guía de inicio rápido. Recuerde o grabe esta información para su uso posterior.

De forma predeterminada, la base de datos de **postgres** se crea en el servidor. La base de datos [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) es una base de datos predeterminada pensada para que la usen los usuarios, las utilidades y aplicaciones de otros fabricantes. 


## <a name="configure-a-server-level-firewall-rule"></a>Configuración de una regla de firewall de nivel de servidor

Cree una regla de firewall de nivel de servidor de Azure PostgreSQL con el comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create). Una regla de firewall de nivel de servidor permite que una aplicación externa, como [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) o [PgAdmin](https://www.pgadmin.org/), se conecte al servidor a través del firewall del servicio Azure PostgreSQL. 

Puede establecer una regla de firewall que abarque un intervalo de IP para poder conectarse desde la red. En el ejemplo siguiente se usa [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) para crear una regla de firewall `AllowAllIps` para un intervalo de direcciones IP. Para abrir todas las direcciones IP, utilice 0.0.0.0 como la dirección IP inicial y 255.255.255.255 como la dirección final.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> El servidor Azure PostgreSQL se comunica a través de puerto 5432. Al conectarse desde una red corporativa, es posible que el firewall de la red no permita el tráfico saliente a través del puerto 5432. Haga que el departamento de TI abra el puerto 5432 para conectarse al servidor de Azure SQL Database.

## <a name="get-the-connection-information"></a>Obtención de la información de conexión

Para conectarse al servidor, debe proporcionar las credenciales de acceso y la información del host.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

El resultado está en formato JSON. Tome nota de los valores de **administratorLogin** y **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-postgresql-database-using-psql"></a>Conectarse a la base de datos de PostgreSQL mediante psql

Si el equipo cliente tiene PostgreSQL instalado, puede usar una instancia local de [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) para conectarse a un servidor Azure PostgreSQL. Ahora vamos a usar la utilidad de línea de comandos psql para conectarnos al servidor Azure PostgreSQL.

1. Ejecute el comando psql siguiente para conectarse a un servidor de Azure Database for PostgreSQL
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Por ejemplo, el siguiente comando se conecta a la base de datos predeterminada llamada **postgres** en el servidor PostgreSQL **mydemoserver.postgres.database.azure.com** con las credenciales de acceso. Escriba el valor de `<server_admin_password>` que eligió cuando se le solicitó una contraseña.
  
  ```azurecli-interactive
psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
```

2.  Una vez conectado al servidor, cree una base de datos vacía en el símbolo del sistema.
```sql
CREATE DATABASE mypgsqldb;
```

3.  En el símbolo del sistema, ejecute el siguiente comando para cambiar la conexión a la base de datos **mypgsqldb** recién creada:
```sql
\c mypgsqldb
```

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>Conexión al servidor de PostgreSQL mediante pgAdmin

pgAdmin es una herramienta de código abierto que se usa con PostgreSQL. Puede instalar pgAdmin desde el [sitio web de pgAdmin](http://www.pgadmin.org/). La versión de pgAdmin que use podría ser diferente de la que se usa en esta guía de inicio rápido. Lea la documentación de pgAdmin si necesita instrucciones adicionales.

1. Abra la aplicación pgAdmin en el equipo cliente.

2. En la barra de herramientas, vaya a **Object** (Objeto), mantenga el mouse sobre **Create** (Crear) y seleccione **Server** (Servidor).

3. En el cuadro de diálogo **Create - Server** (Crear: servidor) de la pestaña **General**, escriba un nombre descriptivo único para el servidor, como **mydemoserver**.

   ![Pestaña "General"](./media/quickstart-create-server-database-azure-cli/9-pgadmin-create-server.png)

4. En la pestaña **Connection** (Conexión) del cuadro de diálogo **Create - Server** (Crear: servidor), rellene la tabla de configuración.

   ![Pestaña "Conexión"](./media/quickstart-create-server-database-azure-cli/10-pgadmin-create-server.png)

    parámetro pgAdmin |Valor|DESCRIPCIÓN
    ---|---|---
    Host name/address | Nombre de servidor | El valor de nombre de servidor que usó al crear el servidor de Azure Database for MySQL. El servidor de ejemplo es **mydemoserver.postgres.database.azure.com.** Use el nombre de dominio completo (**\*.postgres.database.azure.com**) tal como se muestra en el ejemplo. Si no recuerda el nombre del servidor, siga los pasos de la sección anterior para obtener la información de conexión. 
    Port | 5432 | El puerto se usará al conectarse al servidor de Azure Database for PostgreSQL. 
    Maintenance Database | *postgres* | Nombre de la base de datos predeterminada que crea el sistema.
    Nombre de usuario | Nombre de inicio de sesión del administrador del servidor | El nombre de usuario de inicio de sesión del administrador del servidor que suministró al crear el servidor de Azure Database for PostgreSQL. Si no recuerda el nombre de usuario, siga los pasos de la sección anterior para obtener la información de conexión. El formato es *username@servername*.
    Password | Contraseña del administrador | La contraseña que eligió al crear el servidor antes en esta guía de inicio rápido.
    Rol | Déjelo en blanco | No es necesario proporcionar un nombre de rol en este momento. Deje el campo en blanco.
    SSL Mode | *Require* | Puede establecer el modo SSL en la pestaña SSL de pgAdmin. De forma predeterminada, todos los servidores de Azure Database for PostgreSQL se crean de modo que se exija SSL. Para desactivar la obligación de SSL, consulte la sección sobre la [aplicación de SSL](./concepts-ssl-connection-security.md).
    
5. Seleccione **Guardar**.

6. En el panel izquierdo del **explorador**, expanda el nodo **Servidores**. Seleccione el servidor, por ejemplo, **mydemoserver**. Haga clic para conectarse a él.

7. Expanda el nodo de servidor y **Bases de datos**. La lista debe incluir las bases de datos *postgres* existentes y otras bases de datos que haya creado. Con Azure Database for PostgreSQL puede crear varias bases de datos por servidor.

8. Haga clic con el botón derecho en **Bases de datos**, elija el menú **Crear** y, a continuación, seleccione **Base de datos**.

9. Escriba el nombre de base de datos que quiera en el campo **Base de datos**, por ejemplo, **mypgsqldb2**.

10. Seleccione el **propietario** de la base de datos en el cuadro de lista. Elija el nombre de inicio de sesión del administrador del servidor, como en el ejemplo, **my admin**.

   ![Creación de una base de datos en pgadmin](./media/quickstart-create-server-database-azure-cli/11-pgadmin-database.png)

11. Seleccione **Guardar** para crear una base de datos vacía.

12. En el panel del **explorador**, puede ver la base de datos que ha creado en la lista de bases de datos, debajo del nombre del servidor.




## <a name="clean-up-resources"></a>Limpieza de recursos

Elimine el [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) para eliminar todos los recursos que ha creado en la guía de inicio rápido.

> [!TIP]
> Otras guías de inicio rápido de esta colección se basan en los valores de esta. Si tiene previsto seguir trabajando con las siguientes guías de inicio rápido, no elimine los recursos creados en esta. Si no tiene previsto continuar, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido en la CLI de Azure.

```azurecli-interactive
az group delete --name myresourcegroup
```

Si solo desea eliminar el servidor recién creado, puede ejecutar el comando [az postgres server delete](/cli/azure/postgres/server#az_postgres_server_delete).
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>pasos siguientes
> [!div class="nextstepaction"]
> [Migración de una base de datos mediante exportación e importación](./howto-migrate-using-export-and-import.md)

