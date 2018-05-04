---
title: Protección de la conexión con Azure SQL Database desde App Service mediante una identidad de servicio administrada | Microsoft Docs
description: Aprenda a hacer que la conectividad con la base de datos sea más segura con una identidad de servicio administrada y también a aplicar esto a otros servicios de Azure.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/17/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 1b51638754287d3359eaea7bd5da3f71bf15cc89
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-secure-sql-database-connection-with-managed-service-identity"></a>Tutorial: Protección de la conexión con Azure SQL Database mediante una identidad de servicio administrada

[App Service](app-service-web-overview.md) proporciona un servicio de hospedaje web muy escalable y con aplicación de revisiones de un modo automático en Azure. También proporciona una [identidad de servicio administrada](app-service-managed-service-identity.md) para la aplicación, la cual constituye una solución inmediata para proteger el acceso a [Azure SQL Database](/azure/sql-database/) y a otros servicios de Azure. Las identidades de servicio administradas de App Service hacen que su aplicación sea más segura mediante la eliminación de los secretos de aplicación como, por ejemplo, las credenciales en las cadenas de conexión. En este tutorial, agregará una identidad de servicio administrada a la aplicación web de ASP.NET de ejemplo que se creó en [Tutorial: Compilación de una aplicación ASP.NET en Azure con SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md). Cuando haya terminado, la aplicación de ejemplo se conectará a SQL Database de forma segura sin necesidad de nombres de usuario ni contraseñas.

Aprenderá a:

> [!div class="checklist"]
> * Habilitar la identidad de servicio administrada
> * Conceder a SQL Database acceso a la identidad de servicio
> * Configurar el código de la aplicación para autenticarse con SQL Database mediante la autenticación de Azure Active Directory
> * Conceder los privilegios mínimos a la identidad de servicio en SQL Database

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>requisitos previos

Este artículo continúa donde terminó [Tutorial: Compilación de una aplicación ASP.NET en Azure con SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md). Si no lo ha hecho ya, siga este tutorial primero. Como alternativa, puede adaptar los pasos para su propia aplicación ASP.NET con SQL Database.

<!-- ![app running in App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png) -->

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-managed-service-identity"></a>Habilitar la identidad de servicio administrada

Para habilitar una identidad de servicio para la aplicación de Azure, use el comando [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az_webapp_identity_assign) de Cloud Shell. En el siguiente comando, reemplace *\<app name>*.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app name>
```

Este es un ejemplo de la salida después de crear la identidad en Azure Active Directory:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

Usará el valor de `principalId` en el paso siguiente. Si desea ver los detalles de la nueva identidad en Azure Active Directory, ejecute el siguiente comando opcional con el valor de `principalId`:

```azurecli-interactive
az ad sp show --id <principalid>`
```

## <a name="grant-database-access-to-identity"></a>Concesión del acceso de la base de datos a la identidad

A continuación, conceda a la base de datos acceso a la identidad de servicio de la aplicación, mediante el comando [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az_sql_server_ad-admin_create) en Cloud Shell. En el siguiente comando, reemplace *\<server_name>* y <principalid_from_last_step>. Escriba un nombre de administrador para *\<admin_user>*.

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server_name> --display-name <admin_user> --object-id <principalid_from_last_step>
```

La identidad de servicio administrada ya tiene acceso al servidor de Azure SQL Database.

## <a name="modify-connection-string"></a>Modificación de la cadena de conexión

Modifique la conexión que estableció anteriormente para la aplicación mediante el comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) de Cloud Shell. En el siguiente comando, reemplace *\<app name>* por el nombre de la aplicación, y reemplace *\<server_name>* y *\<db_name>* por los nombres correspondientes de su instancia de SQL Database.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='Server=tcp:<server_name>.database.windows.net,1433;Database=<db_name>;' --connection-string-type SQLAzure
```

## <a name="modify-aspnet-code"></a>Modificación del código ASP.NET

En el proyecto **DotNetAppSqlDb** en Visual Studio, abra _packages.config_ y agregue la siguiente línea en la lista de paquetes.

```xml
<package id="Microsoft.Azure.Services.AppAuthentication" version="1.1.0-preview" targetFramework="net461" />
```

Abra _Models\MyDatabaseContext.cs_ y agregue las siguientes instrucciones `using` en la parte superior del archivo:

```csharp
using System.Data.SqlClient;
using Microsoft.Azure.Services.AppAuthentication;
using System.Web.Configuration;
```

En la clase `MyDatabaseContext`, agregue el constructor siguiente:

```csharp
public MyDatabaseContext(SqlConnection conn) : base(conn, true)
{
    conn.ConnectionString = WebConfigurationManager.ConnectionStrings["MyDbConnection"].ConnectionString;
    // DataSource != LocalDB means app is running in Azure with the SQLDB connection string you configured
    if(conn.DataSource != "(localdb)\\MSSQLLocalDB")
        conn.AccessToken = (new AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;

    Database.SetInitializer<MyDatabaseContext>(null);
}
```

Este constructor configura un objeto SqlConnection personalizado para que use un token de acceso para Azure SQL Database desde App Service. Con el token de acceso, la aplicación App Service se autentica con Azure SQL Database con su identidad de servicio administrada. Para más información, consulte [Obtención de tokens para recursos de Azure](app-service-managed-service-identity.md#obtaining-tokens-for-azure-resources). La instrucción `if` le permite seguir probando la aplicación localmente con LocalDB.

> [!NOTE]
> `SqlConnection.AccessToken` se admite actualmente solo en .NET Framework 4.6 y versiones posteriores, no en [.NET Core](https://www.microsoft.com/net/learn/get-started/windows).
>

Para usar este nuevo constructor, abra `Controllers\TodosController.cs` y busque la línea `private MyDatabaseContext db = new MyDatabaseContext();`. El código existente usa el controlador `MyDatabaseContext` predeterminado para crear una base de datos mediante la cadena de conexión estándar que tenía el nombre de usuario y la contraseña en texto no cifrado antes de que [lo cambiara](#modify-connection-string).

Reemplace esta línea al completo por el siguiente código:

```csharp
private MyDatabaseContext db = new MyDatabaseContext(new SqlConnection());
```

### <a name="publish-your-changes"></a>Publicación de los cambios

Ahora lo único que queda es publicar los cambios en Azure.

En el **Explorador de soluciones**, haga clic con el botón derecho en su proyecto **DotNetAppSqlDb** y seleccione **Publicar**.

![Publicar desde el Explorador de soluciones](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

En la página de publicación, haga clic en **Publicar**. Cuando la página web nueva muestra su lista de tareas pendientes, la aplicación se conecta a la base de datos mediante la identidad de servicio administrada.

![Aplicación web de Azure después de aplicar Migraciones de Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Ahora ya puede editar la lista de tareas pendientes como antes.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="grant-minimal-privileges-to-identity"></a>Concesión de privilegios mínimos a la identidad

Durante los pasos anteriores, probablemente ha observado que la identidad de servicio administrada está conectada a SQL Server como administrador de Azure AD. Para conceder privilegios mínimos a la identidad de servicio administrada, debe iniciar sesión en el servidor de Azure SQL Database como administrador de Azure AD y, a continuación, agregar un grupo de Azure Active Directory que contenga la identidad de servicio. 

### <a name="add-managed-service-identity-to-an-azure-active-directory-group"></a>Incorporación de una identidad de servicio administrada a un grupo de Azure Active Directory

En Cloud Shell, agregue la identidad de servicio administrada de la aplicación en un nuevo grupo de Azure Active Directory denominado _myAzureSQLDBAccessGroup_, como se muestra en el siguiente script:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group <group_name> --name <app_name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiid
az ad group member list -g $groupid
```

Si desea ver la salida JSON completa para cada comando, use los parámetros `--query objectId --output tsv`.

### <a name="reconfigure-azure-ad-administrator"></a>Reconfiguración del administrador de Azure AD

Anteriormente, asignó la identidad de servicio administrada como administrador de Azure AD para la instancia de SQL Database. No se puede usar esta identidad para el inicio de sesión interactivo (para agregar usuarios de la base de datos), por lo que deberá usar su usuario real de Azure AD. Para agregar su usuario de Azure AD, siga los pasos descritos en [Aprovisionamiento de un administrador de Azure Active Directory para el servidor de Azure SQL Database](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server). 

### <a name="grant-permissions-to-azure-active-directory-group"></a>Concesión de permisos al grupo de Azure Active Directory

En Cloud Shell, inicie sesión en SQL Database mediante el comando SQLCMD. Reemplace _\<servername>_ por el nombre del servidor de SQL Database, y reemplace _\<AADusername>_ y _\<AADpassword>_ por sus credenciales de usuario de Azure AD.

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

En el símbolo del sistema de SQL, ejecute los siguientes comandos que permitirán agregar el grupo de Azure Active Directory que creó anteriormente como un usuario.

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
GO
```

Escriba `EXIT` para volver al símbolo del sistema de Cloud Shell. A continuación, vuelva a ejecutar SQLCMD, pero especifique el nombre de la base de datos en _\<dbname>_.

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -d <db_name> -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

En el símbolo del sistema de SQL para la base de datos que desee, ejecute los comandos siguientes para conceder permisos de lectura y escritura al grupo de Azure Active Directory.

```sql
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

## <a name="next-steps"></a>Pasos siguientes

¿Qué ha aprendido?

> [!div class="checklist"]
> * Habilitar la identidad de servicio administrada
> * Conceder a SQL Database acceso a la identidad de servicio
> * Configurar el código de la aplicación para autenticarse con SQL Database mediante la autenticación de Azure Active Directory
> * Conceder los privilegios mínimos a la identidad de servicio en SQL Database

Vaya al siguiente tutorial para aprender a asignar un nombre DNS personalizado a una aplicación web.

> [!div class="nextstepaction"]
> [Asignar un nombre DNS personalizado a Azure Web Apps](app-service-web-tutorial-custom-domain.md)