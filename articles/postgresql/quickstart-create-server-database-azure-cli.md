---
title: Crear una base de datos de Azure para PostgreSQL con la CLI de Azure | Microsoft Docs
description: "Guía de inicio rápido para crear y administrar la base de datos de Azure para PostgreSQL mediante la CLI (interfaz de la línea de comandos) de Azure."
services: postgresql
author: sanagama
ms.author: sanagama
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 01/02/2018
ms.custom: mvc
ms.openlocfilehash: ab07172d62806631f73c1df35c7d646e83ad5221
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="create-an-azure-database-for-postgresql-using-the-azure-cli"></a>Crear una base de datos de Azure para PostgreSQL con la CLI de Azure
La base de datos de Azure para PostgreSQL es un servicio administrado que le permite ejecutar, administrar y escalar bases de datos de PostgreSQL de alta disponibilidad en la nube. La CLI de Azure se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts. En esta guía de inicio rápido se muestra cómo crear una base de datos de Azure para el servidor PostgreSQL en un [grupo de recursos de Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) mediante la CLI de Azure.

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

## <a name="create-an-azure-database-for-postgresql-server"></a>Creación de un servidor de Azure Database for PostgreSQL

Cree un [servidor de Azure Database for PostgreSQL](overview.md) con el comando [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create). Un servidor contiene un conjunto de bases de datos administradas como un grupo. 

En el ejemplo siguiente se crea un servidor denominado `mypgserver-20170401` en el grupo de recursos `myresourcegroup` con el inicio de sesión de administrador de servidor `mylogin`. El nombre de un servidor se asigna al nombre DNS y, por tanto, debe ser único a nivel global en Azure. Sustituya `<server_admin_password>` por su propio valor.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401  --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> El inicio de sesión y la contraseña de administrador del servidor que especifique aquí serán necesarios para iniciar sesión más adelante en ese servidor y en las bases de datos que se especificarán en esta guía de inicio rápido. Recuerde o grabe esta información para su uso posterior.

De forma predeterminada, la base de datos de **postgres** se crea en el servidor. La base de datos [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) es una base de datos predeterminada pensada para que la usen los usuarios, las utilidades y aplicaciones de otros fabricantes. 


## <a name="configure-a-server-level-firewall-rule"></a>Configuración de una regla de firewall de nivel de servidor

Cree una regla de firewall de nivel de servidor de Azure PostgreSQL con el comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create). Una regla de firewall de nivel de servidor permite que una aplicación externa, como [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) o [PgAdmin](https://www.pgadmin.org/), se conecte al servidor a través del firewall del servicio Azure PostgreSQL. 

Puede establecer una regla de firewall que abarque un intervalo de IP para poder conectarse desde la red. En el ejemplo siguiente se usa [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) para crear una regla de firewall `AllowAllIps` para un intervalo de direcciones IP. Para abrir todas las direcciones IP, utilice 0.0.0.0 como la dirección IP inicial y 255.255.255.255 como la dirección final.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> El servidor Azure PostgreSQL se comunica a través de puerto 5432. Al conectarse desde una red corporativa, es posible que el firewall de la red no permita el tráfico saliente a través del puerto 5432. Haga que el departamento de TI abra el puerto 5432 para conectarse al servidor de Azure SQL Database.

## <a name="get-the-connection-information"></a>Obtención de la información de conexión

Para conectarse al servidor, debe proporcionar las credenciales de acceso y la información del host.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

El resultado está en formato JSON. Tome nota de los valores de **administratorLogin** y **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "mylogin",
  "fullyQualifiedDomainName": "mypgserver-20170401.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401",
  "location": "westus",
  "name": "mypgserver-20170401",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "PGSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 51200,
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

  Por ejemplo, el siguiente comando se conecta a la base de datos predeterminada denominada **postgres** en el servidor PostgreSQL **mypgserver-20170401.postgres.database.azure.com** con las credenciales de acceso. Escriba el valor de `<server_admin_password>` que eligió cuando se le solicitó una contraseña.
  
  ```azurecli-interactive
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
```

2.  Una vez conectado al servidor, cree una base de datos vacía en el símbolo del sistema.
```sql
CREATE DATABASE mypgsqldb;
```

3.  En el símbolo del sistema, ejecute el siguiente comando para cambiar la conexión a la base de datos **mypgsqldb** recién creada:
```sql
\c mypgsqldb
```

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Conectarse a la base de datos de PostgreSQL mediante pgAdmin

Para conectarse al servidor Azure PostgreSQL mediante la herramienta _pgAdmin_ de la GUI:
1.  Inicie la aplicación _pgAdmin_ en el equipo cliente. Puede instalar _pgAdmin_ desde http://www.pgadmin.org/.
2.  Seleccione **Agregar nuevo servidor** en el menú **Vínculos rápidos**.
3.  En la pestaña **General** del cuadro de diálogo **Crear servidor**, escriba un nombre descriptivo único para el servidor, como **Azure PostgreSQL Server**.
 ![Herramienta pgAdmin: Crear servidor](./media/quickstart-create-server-database-azure-cli/1-pgadmin-create-server.png)
4.  En el cuadro de diálogo **Crear servidor**, en la pestaña **Conexión**:
    - Escriba el nombre completo del servidor (por ejemplo, **mypgserver-20170401.postgres.database.azure.com**) en el cuadro **Host Name/Address** (Nombre o dirección de host). 
    - Escriba el puerto 5432 en el cuadro **Puerto**. 
    - Escriba el **Inicio de sesión del administrador del servidor (user@mypgserver)** obtenido anteriormente en esta guía de inicio rápido y la contraseña que especificó al crear el servidor en los cuadros **Nombre de usuario** y **Contraseña**, respectivamente.
    - Seleccione el **Modo SSL** como **Requerir**. De forma predeterminada, todos los servidores Azure PostgreSQL se crean de modo que se exija SSL. Para desactivar la obligación de SSL, vea los detalles en la sección sobre la [obligación de SSL](./concepts-ssl-connection-security.md).

    ![pgAdmin: Crear servidor](./media/quickstart-create-server-database-azure-cli/2-pgadmin-create-server.png)
5.  Haga clic en **Save**(Guardar).
6.  En el panel izquierdo del explorador, expanda **Grupos de servidores**. Seleccione su **servidor Azure PostgreSQL**.
7.  Elija el **Server** (Servidor) al que se ha conectado y sus **Databases** (Bases de datos). 
8.  Haga clic con el botón derecho en **Bases de datos** para crear una base de datos.
9.  Elija el nombre de base de datos **mypgsqldb** y su propietario como valor **mylogin** para el inicio de sesión de administrador de servidor.
10. Haga clic en **Guardar** para crear una base de datos vacía.
11. En **Explorador**, expanda el grupo **Servidores**. Expanda el servidor que ha creado y verá su base de datos **mypgsqldb**.
 ![pgAdmin: Crear base de datos](./media/quickstart-create-server-database-azure-cli/3-pgadmin-database.png)


## <a name="clean-up-resources"></a>Limpieza de recursos

Elimine el [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) para eliminar todos los recursos que ha creado en la guía de inicio rápido.

> [!TIP]
> Otras guías de inicio rápido de esta colección se basan en los valores de esta. Si tiene previsto seguir trabajando con las siguientes guías de inicio rápido, no elimine los recursos creados en esta. Si no tiene previsto continuar, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido en la CLI de Azure.

```azurecli-interactive
az group delete --name myresourcegroup
```

Si solo desea eliminar el servidor recién creado, puede ejecutar el comando [az postgres server delete](/cli/azure/postgres/server#az_postgres_server_delete).
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mypgserver-20170401
```

## <a name="next-steps"></a>pasos siguientes
> [!div class="nextstepaction"]
> [Migración de una base de datos mediante exportación e importación](./howto-migrate-using-export-and-import.md)
