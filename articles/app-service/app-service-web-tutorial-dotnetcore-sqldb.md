---
title: "Creación de una aplicación web .NET Core y SQL Database en Azure App Service | Microsoft Docs"
description: "Aprenda a poner en funcionamiento una aplicación .NET Core en Azure App Service, con conexión a una instancia de SQL Database."
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 01/23/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 1a60c76b2687e4c6561eabf8a19dbfffffbe8681
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="build-a-net-core-and-sql-database-web-app-in-azure-app-service"></a>Creación de una aplicación web .NET Core y SQL Database en Azure App Service

> [!NOTE]
> En este artículo se implementa una aplicación en App Service en Windows. Para implementar App Service en _Linux_, consulte [Compilación de una aplicación web .NET Core y SQL Database en Azure App Service en Linux](./containers/tutorial-dotnetcore-sqldb-app.md).
>

[App Service](app-service-web-overview.md) proporciona un servicio de hospedaje web muy escalable y con aplicación de revisiones de un modo automático en Azure. En este tutorial se muestra cómo crear una aplicación web .NET Core y conectarla a una instancia de SQL Database. Cuando termine, tendrá una aplicación .NET Core con MVC en ejecución en App Service.

![aplicación que se ejecuta en App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

Aprenderá a:

> [!div class="checklist"]
> * Crear una base de datos Azure SQL Database
> * Conectar una aplicación .NET Core a SQL Database
> * Implementación de la aplicación en Azure
> * Actualizar el modelo de datos y volver a implementar la aplicación
> * Transmitir registros de diagnóstico desde Azure
> * Administrar la aplicación en Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial:

1. [Instalación de Git](https://git-scm.com/)
1. [Instalación del SDK 1.1.2 de .NET Core](https://github.com/dotnet/core/blob/master/release-notes/download-archives/1.1.2-download.md)

## <a name="create-local-net-core-app"></a>Creación de una aplicación .NET Core local

En este paso, configurará el proyecto .NET Core local.

### <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

En la ventana del terminal, use `cd` para cambiar a un directorio de trabajo.

Ejecute los comandos siguientes para clonar el repositorio de ejemplo y cambia a su raíz.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

El proyecto de ejemplo contiene una aplicación básica CRUD (crear, leer, actualizar, eliminar) que usa [Entity Framework Core](https://docs.microsoft.com/ef/core/).

### <a name="run-the-application"></a>Ejecución de la aplicación

Ejecute los comandos siguientes para instalar los paquetes necesarios, ejecute las migraciones de bases de datos e inicie la aplicación.

```bash
dotnet restore
dotnet ef database update
dotnet run
```

Vaya a `http://localhost:5000` en un explorador. Seleccione el vínculo **Crear nuevo** y cree un par de elementos de _tareas pendientes_.

![Se conecta correctamente a SQL Database](./media/app-service-web-tutorial-dotnetcore-sqldb/local-app-in-browser.png)

Para detener .NET Core en cualquier momento, presione `Ctrl+C` en el terminal.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Creación de una instancia de SQL Database de producción

En este paso, creará una instancia de SQL Database en Azure. Cuando la aplicación se implementa en Azure, utiliza esta base de datos en la nube.

Para SQL Database, en este tutorial se usa [Azure SQL Database](/azure/sql-database/).

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Creación de un servidor lógico de SQL Database

En Cloud Shell, cree un servidor lógico de SQL Database con el comando [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az_sql_server_create).

Reemplace el marcador de posición *\<server_name>* con un nombre de SQL Database único. Este nombre se usa como parte del punto de conexión de SQL Database, `<server_name>.database.windows.net`, por lo que el nombre debe ser único para todos los servidores lógicos en Azure. El nombre debe contener solo letras minúsculas, números y el carácter de guión (-), y debe tener una longitud de entre 3 y 50 caracteres. Además, reemplace *\<db_username>* y *\<db_password>* por un nombre de usuario y una contraseña de su elección. 


```azurecli-interactive
az sql server create --name <server_name> --resource-group myResourceGroup --location "West Europe" --admin-user <db_username> --admin-password <db_password>
```

Cuando se crea el servidor lógico de SQL Database, la CLI de Azure muestra información similar al ejemplo siguiente:

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server_name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server_name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>Configuración de una regla de firewall del servidor

Cree una [regla de firewall en el nivel de servidor de Azure SQL Database](../sql-database/sql-database-firewall-configure.md) mediante el comando [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az_sql_server_firewall_rule_create). Cuando tanto la dirección IP de inicio como final están establecidas en 0.0.0.0., el firewall solo se abre para otros recursos de Azure. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server_name> --name AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="create-a-database"></a>Creación de una base de datos

Cree una base de datos con un [nivel de rendimiento S0](../sql-database/sql-database-service-tiers.md) en el servidor con el comando [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az_sql_db_create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server_name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Creación de la cadena de conexión

Reemplace la cadena siguiente por el valor de *\<server_name>*, *\<db_username>* y *\<db_password>* que usó anteriormente.

```
Server=tcp:<server_name>.database.windows.net,1433;Initial Catalog=coreDB;Persist Security Info=False;User ID=<db_username>;Password=<db_password>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Esta es la cadena de conexión de la aplicación .NET Core. Cópiela para usarla más adelante.

## <a name="deploy-app-to-azure"></a>Implementación de aplicación en Azure

En este paso, va a implementar la aplicación .NET Core conectada a SQL Database en App Service.

### <a name="configure-local-git-deployment"></a>Configuración de la implementación de Git local

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Creación de un plan de App Service

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Creación de una aplicación web

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Configuración de una variable de entorno

Para establecer las cadenas de conexión de la aplicación de Azure, use el comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) en Cloud Shell. En el comando siguiente, reemplace el parámetro *\<app name>* así como *\<connection_string>* por la cadena de conexión que creó anteriormente.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='<connection_string>' --connection-string-type SQLServer
```

A continuación, establezca la configuración de la aplicación `ASPNETCORE_ENVIRONMENT` en _Producción_. Esta configuración permite saber si ejecuta Azure, porque usa SQLite para el entorno de desarrollo local y SQL Database para el entorno de Azure.

En el ejemplo siguiente se configura una opción de configuración de aplicación `ASPNETCORE_ENVIRONMENT` en la aplicación web de Azure. Reemplace el marcador de posición *\<app_name>*.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

### <a name="connect-to-sql-database-in-production"></a>Conexión a SQL Database en producción

En el repositorio local, abra Startup.cs y busque el código siguiente:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Reemplácelo por el código siguiente, que usa las variables de entorno que configuró anteriormente.

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=MvcMovie.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Si este código detecta que se ejecuta en producción (lo que indica el entorno de Azure), usa la cadena de conexión que configuró para conectarse a SQL Database.

La llamada de `Database.Migrate()` le ayuda cuando se ejecuta en Azure, porque crea automáticamente las bases de datos que necesita la aplicación .NET Core según su configuración de migración. 

Guarde los cambios y confírmelos en el repositorio de Git. 

```bash
git commit -am "connect to SQLDB in Azure"
```

### <a name="push-to-azure-from-git"></a>Inserción en Azure desde Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-web-app"></a>Navegación hasta la aplicación web de Azure

Vaya a la aplicación implementada mediante el explorador web.

```bash
http://<app_name>.azurewebsites.net
```

Agregue algunos elementos de tareas pendientes.

![aplicación que se ejecuta en App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

**¡Enhorabuena!** Está ejecutando una aplicación .NET Core controlada por datos en App Service.

## <a name="update-locally-and-redeploy"></a>Actualización local y nueva implementación

En este paso, modificará el esquema de la base de datos y lo publicará en Azure.

### <a name="update-your-data-model"></a>Actualización del modelo de datos

Abra _Models\Todo.cs_ en el editor de código. Agregue la siguiente propiedad a la clase `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Ejecución de Migraciones de Code First

Ejecute algunos comandos para realizar actualizaciones en la base de datos local.

```bash
dotnet ef migrations add AddProperty
```

Actualice la base de datos local:

```bash
dotnet ef database update
```

### <a name="use-the-new-property"></a>Uso de la nueva propiedad

Realice algunos cambios en el código para usar la propiedad `Done`. Para simplificar, en este tutorial va a cambiar las vistas `Index` y `Create` para ver la propiedad en acción.

Abra _Controllers\TodosController.cs_.

Busque el método `Create()` y agregue `Done` a la lista de propiedades en el atributo `Bind`. Cuando haya terminado, la firma del método `Create()` se parecerá al código siguiente:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Abra _Views\Todos\Create.cshtml_.

En el código Razor debería ver un elemento `<div class="form-group">` para `Description` y, luego, otro elemento `<div class="form-group">` para `CreatedDate`. Inmediatamente después de estos dos elementos, agregue otro elemento `<div class="form-group">` para `Done`:

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

Abra _Views\Todos\Index.cshtml_.

Busque el elemento vacío `<th></th>`. Justo encima de este elemento, agregue el siguiente código Razor:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Busque el elemento `<td>` que contiene los auxiliares de la etiqueta `asp-action`. Justo encima de este elemento, agregue el siguiente código Razor:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.CreatedDate)
</td>
```

Eso es todo lo que necesita para ver los cambios en las vistas `Index` y `Create`.

### <a name="test-your-changes-locally"></a>Prueba de los cambios localmente

Ejecute localmente la aplicación.

```bash
dotnet run
```

Abra el explorador y vaya a `http://localhost:5000/`. Ahora puede agregar una tarea pendiente y marcar **Listo**. A continuación se debería mostrar en su página principal como un elemento completado. Recuerde que la vista `Edit` no muestra el campo `Done`, dado que no cambió la vista `Edit`.

### <a name="publish-changes-to-azure"></a>Publicación de los cambios en Azure

```bash
git commit -am "added done field"
git push azure master
```

Una vez que `git push` esté completo, navegue hasta su aplicación web de Azure y pruebe la nueva funcionalidad.

![Aplicación web de Azure después de aplicar Migraciones de Code First](./media/app-service-web-tutorial-dotnetcore-sqldb/this-one-is-done.png)

Aún se muestran todas las tareas pendientes existentes. Cuando vuelva a publicar la aplicación .NET Core, no se perderán los datos existentes en la instancia de SQL Database. Además, las migraciones de Entity Framework Core solo cambia el esquema de datos y deja intactos los datos existentes.

## <a name="manage-your-azure-web-app"></a>Administración de la aplicación web de Azure

Vaya a [Azure Portal](https://portal.azure.com) para ver la aplicación web que ha creado.

En el menú izquierdo, haga clic en **App Services**, a continuación, haga clic en el nombre de la aplicación web de Azure.

![Navegación desde el portal a la aplicación web de Azure](./media/app-service-web-tutorial-dotnetcore-sqldb/access-portal.png)

De forma predeterminada, el portal muestra la página **Información general**. Esta página proporciona una visión del funcionamiento de la aplicación. En este caso, también puede realizar tareas de administración básicas como examinar, detener, iniciar, reiniciar y eliminar. Las pestañas del lado izquierdo de la página muestran las diferentes páginas de configuración que puede abrir.

![Página de App Service en Azure Portal](./media/app-service-web-tutorial-dotnetcore-sqldb/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>pasos siguientes

¿Qué ha aprendido?

> [!div class="checklist"]
> * Crear una base de datos Azure SQL Database
> * Conectar una aplicación .NET Core a SQL Database
> * Implementación de la aplicación en Azure
> * Actualizar el modelo de datos y volver a implementar la aplicación
> * Transmitir registros desde Azure a un terminal
> * Administrar la aplicación en Azure Portal

Vaya al siguiente tutorial para aprender a asignar un nombre DNS personalizado a una aplicación web.

> [!div class="nextstepaction"]
> [Asignar un nombre DNS personalizado a Azure Web Apps](app-service-web-tutorial-custom-domain.md)