---
title: "Creación de una aplicación ASP.NET en Azure con SQL Database | Microsoft Docs"
description: "Aprenda a comenzar a trabajar con una aplicación ASP.NET en Azure, con conexión a una instancia de SQL Database."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/07/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d7006a50d35412021f7e475df526661854b23dc8
ms.lasthandoff: 04/22/2017


---
# <a name="create-an-aspnet-app-in-azure-with-sql-database"></a>Creación de una aplicación ASP.NET en Azure con SQL Database

En este tutorial se muestra cómo desarrollar una aplicación web ASP.NET orientada a datos, conectarla a Azure SQL Database y habilitar la funcionalidad orientada a datos. Cuando haya terminado, tendrá una aplicación ASP.NET que se ejecuta en [Azure App Service](../app-service/app-service-value-prop-what-is.md) y que está conectada a SQL Database.

![Aplicación ASP.NET publicada en la aplicación web de Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

## <a name="before-you-begin"></a>Antes de empezar

Antes de ejecutar este ejemplo, [descargue e instale la versión gratuita de Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Asegúrese de que habilita **Desarrollo de Azure** durante la instalación de Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Descarga del ejemplo
En este paso, descargará una aplicación ASP.NET de ejemplo.

### <a name="get-the-sample-project"></a>Obtención del proyecto de ejemplo

Descargue el proyecto de ejemplo haciendo clic [aquí](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip).

Extraiga el archivo `dotnet-sqldb-tutorial-master.zip` descargado en un directorio de trabajo.

> [!TIP]
> Puede obtener el mismo proyecto de ejemplo mediante la clonación del repositorio de GitHub:
>
> ```bash
> git clone https://github.com/Azure-Samples/dotnet-sqldb-tutorial.git
> ```
>
>

Este proyecto de ejemplo contiene una aplicación CRUD (crear, leer, actualizar, eliminar) de [ASP.NET MVC](https://www.asp.net/mvc) creada en [Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="run-the-application"></a>Ejecución de la aplicación

Desde el directorio extraído, inicie `dotnet-sqldb-tutorial-master\DotNetAppSqlDb.sln` en Visual Studio 2017.

Cuando se abra la solución de ejemplo, escriba `F5` para ejecutarla en el explorador.

Debería ver una lista sencilla de tareas pendientes en la página principal. Pruebe a agregar unas cuantas tareas pendientes a la lista vacía.

![Cuadro de diálogo New ASP.NET Project](./media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

El contexto de base de datos usa una cadena de conexión denominada `MyDbConnection`. Esta cadena de conexión se define en `Web.config` y se hace referencia a ella en `Models\MyDatabaseContext.cs`. El nombre de la cadena de conexión es todo lo que necesita más adelante para conectar la aplicación web de Azure a Azure SQL Database. 

## <a name="publish-to-azure-with-sql-database"></a>Publicación en Azure con SQL Database

En el **Explorador de soluciones**, haga clic con el botón derecho en su proyecto **DotNetAppSqlDb** y seleccione **Publicar**.

![Publicar desde el Explorador de soluciones](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Asegúrese de que **Microsoft Azure App Service** está seleccionado y haga clic en **Publicar**.

![Publicar desde la página de información general del proyecto](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-to-app-service.png)

Con ello se abrirá el cuadro de diálogo **Crear App Service** que le ayudará a crear todos los recursos de Azure necesarios para ejecutar la aplicación web de ASP.NET en Azure.

### <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

En el cuadro de diálogo **Crear App Service**, haga clic en **Agregar una cuenta** y, a continuación, inicie sesión en su suscripción de Azure. Si ya ha iniciado sesión en una cuenta de Microsoft, asegúrese de que esa cuenta contiene la suscripción de Azure. Si la cuenta de Microsoft con la que inició sesión no contiene su suscripción de Azure, haga clic en ella para agregar la cuenta correcta.
   
![Inicio de sesión en Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

Una vez que haya iniciado sesión, estará listo para crear todos los recursos que necesita para la aplicación web de Azure en este cuadro de diálogo.

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

En primer lugar, necesita un _grupo de recursos_. 

> [!NOTE] 
> Un grupo de recursos es un contenedor lógico en el que se implementan y se administran recursos de Azure como aplicaciones web, bases de datos y cuentas de almacenamiento.
>
>

Junto a **Grupo de recursos**, haga clic en **Nuevo**.

Asigne el nombre **myResourceGroup** al grupo de recursos y haga clic en **Aceptar**.

### <a name="create-an-app-service-plan"></a>Creación de un plan del Servicio de aplicaciones

La aplicación web de Azure también necesita un _plan de App Service_. 

> [!NOTE]
> Un plan de App Service representa la colección de recursos físicos que se utiliza para hospedar las aplicaciones. Todas las aplicaciones asignadas a un plan de App Service comparten los recursos definidos por él, lo que permite ahorrar costos al hospedar varias aplicaciones. 
>
> Los planes de App Service definen lo siguiente:
>
> - Región (Europa del Norte, este de EE. UU., Sudeste Asiático)
> - Tamaño de la instancia (pequeño, mediano, grande)
> - Recuento de escala (una, dos, tres instancias, etc.) 
> - SKU (Gratis, Compartido, Básico, Estándar y Premium)
>
>

Junto a **Plan de App Service**, haga clic en **Nuevo**. 

En el cuadro de diálogo **Configurar el plan de App Service**, configure el nuevo plan de App Service con los valores siguientes:

- **Plan de App Service**: escriba **myAppServicePlan**. 
- **Ubicación**: elija **Europa occidental**, o cualquier otra región que desee.
- **Tamaño**: elija **Gratis**, o cualquier otro [plan de tarifa](https://azure.microsoft.com/pricing/details/app-service/) que desee.

Haga clic en **Aceptar**.

![Creación de un plan de Servicio de aplicaciones](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

### <a name="configure-the-web-app-name"></a>Configuración del nombre de la aplicación web

En **Nombre de la aplicación web**, escriba un nombre único de aplicación web. Este nombre se utilizará como parte del nombre DNS predeterminado para la aplicación (`<app_name>.azurewebsites.net`), por lo que debe ser único entre todas las aplicaciones de Azure. Más adelante puede asignar un nombre de dominio personalizado a la aplicación antes de exponerlo a los usuarios.

También puede aceptar el nombre generado automáticamente, que ya es único.

Para prepararse para el siguiente paso, haga clic en **Explorar servicios adicionales de Azure**.

![Configurar el nombre de la aplicación web](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-name.png)

### <a name="create-a-sql-server-instance"></a>Creación de una instancia de SQL Server

En la pestaña **Servicios**, haga clic en el icono **+** junto a **SQL Database**. 

En el cuadro de diálogo **Configurar SQL Database**, haga clic en **Nuevo** junto a **SQL Server**. 

En **Nombre del servidor**, escriba un nombre único. Este nombre se usará como parte del nombre DNS predeterminado para el servidor de base de datos (`<server_name>.database.windows.net`), por lo que debe ser único en todas las instancias de SQL Server en Azure. 

Configure el resto de los campos como desee y haga clic en **Aceptar**.

![Creación de una instancia de SQL Server](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

### <a name="configure-the-sql-database"></a>Configuración de la instancia de SQL Database

En **Nombre de la base de datos**, escriba `myToDoAppDb`, o cualquier nombre que quiera.

En **Nombre de la cadena de conexión**, escriba `MyDbConnection`. Este nombre debe coincidir con la cadena de conexión a la que se hace referencia en `Models\MyDatabaseContext.cs`.

![Configuración de SQL Database](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

### <a name="create-and-publish-the-web-app"></a>Creación y publicación de la aplicación web

Haga clic en **Crear**. 

Una vez que el asistente finaliza la creación de los recursos de Azure, publica automáticamente la aplicación ASP.NET en Azure por primera vez y, a continuación, inicia la aplicación web de Azure publicada en el explorador predeterminado.

Pruebe a agregar unos cuantos elementos de tareas pendientes a la lista vacía.

![Aplicación ASP.NET publicada en la aplicación web de Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

¡Enhorabuena! La aplicación ASP.NET orientada a datos se ejecuta en directo en Azure App Service.

## <a name="access-the-sql-database-locally"></a>Acceso local a la instancia de SQL Database

Visual Studio le permite explorar y administrar su nueva instancia de SQL Database de manera fácil en el **Explorador de objetos de SQL Server**.

### <a name="create-a-database-connection"></a>Creación de una conexión de base de datos

Abra el **Explorador de objetos de SQL Server**; para ello, escriba `Ctrl`+`\`, `Ctrl`+`S`.

En la parte superior del **Explorador de objetos de SQL Server**, haga clic en el botón **Agregar SQL Server**.

### <a name="configure-the-database-connection"></a>Configuración de la conexión de base de datos

En el cuadro de diálogo **Conectar**, expanda el nodo **Azure**. A continuación se enumeran todas las instancias de SQL Database de Azure.

Seleccione la instancia de SQL Database que creó anteriormente. La conexión que usó anteriormente se rellena automáticamente en la parte inferior.

Escriba la contraseña de administrador de base de datos que usó anteriormente y haga clic en **Conectar**.

![Configuración de la conexión de base de datos de Visual Studio](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

### <a name="allow-client-connection-from-your-computer"></a>Permitir la conexión de cliente desde el equipo

Se abre el cuadro de diálogo **Create a new firewall rule** (Crear nueva regla de firewall). De forma predeterminada, la instancia de SQL Server solo permite conexiones desde servicios de Azure, como la aplicación web de Azure. Para conectarse a la base de datos directamente desde Visual Studio, debe crear una regla de firewall en la instancia de SQL Server que permita la dirección IP pública del equipo local.

Esto es muy sencillo en Visual Studio. El cuadro de diálogo ya se ha rellenado con la dirección IP pública de su equipo.

Asegúrese de que **Add my client IP** (Agregar mi IP de cliente) está seleccionado y haga clic en **Aceptar**. 

![Configuración del firewall para la instancia de SQL Server](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

Una vez que Visual Studio termina de crear la configuración de firewall para su instancia de SQL Server, la conexión se muestra en el **Explorador de objetos de SQL Server**.

Aquí, puede realizar las operaciones de base de datos más comunes, como ejecutar consultas, crear vistas y procedimientos almacenados, entre otras. En el ejemplo siguiente se muestra cómo ver los datos de base de datos. 

![Exploración de objetos de SQL Database](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Actualización de aplicaciones con Migraciones de Code First

En este paso, usará Migraciones de Code First en Entity Framework para realizar un cambio en el esquema de base de datos y publicarlo en Azure.

### <a name="update-your-data-model"></a>Actualización del modelo de datos

Abra `Models\Todo.cs` en el editor de código. Agregue la siguiente propiedad a la clase `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Ejecución de Migraciones de Code First

A continuación, ejecute algunos comandos para realizar actualizaciones en la base de datos localdb. 

En el menú **Herramientas**, haga clic en **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**. La consola se abre normalmente en la ventana inferior.

Habilite Migraciones de Code First de esta forma:

```PowerShell
Enable-Migrations
```

Agregue una migración como esta:

```PowerShell
Add-Migration AddProperty
```

Actualice la base de datos localdb de esta forma:

```PowerShell
Update-Database
```

Pruebe los cambios mediante la ejecución de la aplicación con `F5`.

Si la aplicación se carga sin errores, la característica Migraciones de Code First ha funcionado correctamente. Sin embargo, la página todavía tiene el mismo aspecto porque la aplicación lógica no usa aún esta propiedad. 

### <a name="use-the-new-property"></a>Uso de la nueva propiedad

Vamos a realizar algunos cambios en el código para usar la propiedad `Done`. Para simplificar, en este tutorial va a cambiar las vistas `Index` y `Create` para ver la propiedad en acción.

Abra `Controllers\TodosController.cs`.

Busque el método `Create()` y agregue `Done` a la lista de propiedades en el atributo `Bind`. Cuando haya terminado, la firma de su método `Create()` debería tener el siguiente aspecto:

```csharp
public ActionResult Create([Bind(Include = "id,Description,CreatedDate,Done")] Todo todo)
```

Abra `Views\Todos\Create.cshtml`.

En el código Razor, debería ver una etiqueta `<div class="form-group">` que usa `model.Description` y, a continuación, otra etiqueta `<div class="form-group">` que usa `model.CreatedDate`. Inmediatamente después de estas dos etiquetas, agregue otra etiqueta `<div class="form-group">` que use `model.Done`, como esta:

```csharp
<div class="form-group">
    @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
    <div class="col-md-10">
        <div class="checkbox">
            @Html.EditorFor(model => model.Done)
            @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
        </div>
    </div>
</div>
```

Abra `Views\Todos\Index.cshtml`.

Busque la etiqueta `<th></th>` vacía. Justo encima de esta etiqueta, agregue el siguiente código Razor:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Busque la etiqueta `<td>` que contenga los métodos auxiliares `Html.ActionLink()`. Justo encima de esta etiqueta, agregue el siguiente código Razor:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Eso es todo lo que necesita para ver los cambios en las vistas `Index` y `Create`. 

Escriba `F5` de nuevo para ejecutar la aplicación.

Ahora, debería poder agregar un elemento de tarea pendiente y marcar **Listo**. A continuación se debería mostrar en su página principal como un elemento completado. Recuerde que esto es todo lo que puede hacer por ahora porque no ha cambiado la vista `Edit`.

### <a name="enable-code-first-migrations-in-azure"></a>Habilitación de Migraciones de Code First en Azure

Ahora que el cambio de código funciona, incluida la migración de la base de datos, lo publicará en la aplicación web de Azure y actualizará también su instancia de SQL Database con Migraciones de Code First.

Igual que antes, haga clic con el botón derecho en su proyecto y seleccione **Publicar**.

Haga clic en **Configuración** para abrir el Asistente para publicación.

![Abrir la configuración de publicación](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

En el asistente, haga clic en **Siguiente**.

Asegúrese de que la cadena de conexión de su instancia de SQL Database se rellena en **MyDatabaseContext (MyDbConnection)**. Debe seleccionar la base de datos **myToDoAppDb** en la lista desplegable. 

Seleccione **Ejecutar Migraciones de Code First (se ejecuta al iniciar la aplicación)** y luego haga clic en **Guardar**.

![Habilitar Migraciones de Code First en una aplicación web de Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

### <a name="publish-your-changes"></a>Publicación de los cambios

Ahora que ha habilitado Migraciones de Code First en su aplicación web de Azure, publique los cambios en el código.

En la página de publicación, haga clic en **Publicar**.

Intente volver a crear nuevos elementos de tareas pendientes y seleccione **Listo**; se mostrarán en su página de inicio como un elemento completado.

![Aplicación web de Azure después de aplicar Migraciones de Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

> [!NOTE]
> Observe que aún se muestran todos los elementos de tareas pendientes existentes. Cuando vuelva a publicar su aplicación de ASP.NET, los datos existentes en su instancia de SQL Database no se perderán. Además, Migraciones de Code First solo cambia el esquema de datos y deja intactos los datos existentes.
>
>

## <a name="stream-application-logs"></a>Transmisión de registros de aplicación

Puede transmitir mensajes de seguimiento directamente desde la aplicación web de Azure hasta Visual Studio.

Abra `Controllers\TodosController.cs`.

Observe que cada acción comienza con un método `Trace.WriteLine()`. Este código se agrega para mostrarle lo fácil que es agregar mensajes de seguimiento a la aplicación web de Azure.

### <a name="open-server-explorer"></a>Apertura del Explorador de servidores

Puede configurar el registro de la aplicación web de Azure en el **Explorador de servidores**. 

Para abrirlo, escriba `Ctrl`+`Alt`+`S`.

### <a name="enable-log-streaming"></a>Habilitación de la secuencia de registros

En el **Explorador de servidores**, expanda **Azure** > **App Service**.

Expanda el grupo de recursos **myResourceGroup** que creó cuando creó la aplicación web de Azure por primera vez.

Haga clic con el botón derecho en la aplicación web de Azure y seleccione **Ver registros de streaming**.

![Habilitación de la secuencia de registros](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

Los registros se insertan ahora en la ventana **Salida**. 

![Secuencia de registros en la ventana Salida](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

Sin embargo, no verá aún ninguno de los mensajes de seguimiento. Esto se debe a que cuando selecciona primero **Ver registros de streaming**, la aplicación web de Azure establece el nivel de seguimiento en `Error`, que solo registra eventos de error (con el método `Trace.TraceError()`).

### <a name="change-trace-levels"></a>Cambio de los niveles de seguimiento

Para cambiar los niveles de seguimiento para generar otros mensajes de seguimiento, vuelva al **Explorador de servidores**.

Haga clic con el botón derecho en la aplicación web de Azure y seleccione **Configuración**.

En la lista desplegable **Registro de la aplicación (sistema de archivos)**, seleccione **Detallado**. Haga clic en **Guardar**.

![Cambiar el nivel de seguimiento a Detallado](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

> [!TIP]
> Puede experimentar con diferentes niveles de seguimiento para ver qué tipos de mensajes se muestran para cada nivel. Por ejemplo, el nivel **Información** incluye todos los registros creados por `Trace.TraceInformation()`, `Trace.TraceWarning()` y `Trace.TraceError()`, pero no los registros creados por `Trace.WriteLine()`.
>
>

En el explorador, pruebe a hacer clic en torno a la aplicación de lista de tareas pendientes en Azure. Los mensajes de seguimiento se insertan ahora en la ventana **Salida** de Visual Studio.

```
Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
```

### <a name="stop-log-streaming"></a>Detención de las secuencias de registro

Para detener el servicio de secuencias de registros, haga clic en el botón **Detener supervisión** en la ventana **Salida**.

![Detención de las secuencias de registro](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-web-app"></a>Administración de la aplicación web de Azure

Vaya al portal de Azure para ver la aplicación web que ha creado. 

Para ello, inicie sesión en [https://portal.azure.com/](https://portal.azure.com).

En el menú izquierdo, haga clic en **App Service**, a continuación, haga clic en el nombre de la aplicación web de Azure.

![Navegación desde el portal a la aplicación web de Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

Ha llegado a la _hoja_ de su aplicación web (una página del portal que se abre horizontalmente). 

De forma predeterminada, la hoja de la aplicación web muestra la página de **introducción**. Esta página proporciona una visión del funcionamiento de la aplicación. En este caso, también puede realizar tareas de administración básicas como examinar, detener, iniciar, reiniciar y eliminar. Las pestañas del lado izquierdo de la hoja muestran las diferentes páginas de configuración que puede abrir. 

![Hoja de App Service en Azure Portal](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

Estas pestañas de la hoja muestran las muchas y excepcionales características que puede agregar a la aplicación web. La lista siguiente proporciona solo algunas de las posibilidades:

- Asignación de un nombre DNS personalizado
- Enlace de un certificado SSL personalizado
- Configuración de la implementación continua
- Escalado vertical y horizontal
- Adición de la autenticación de usuarios

## <a name="next-steps"></a>Pasos siguientes

Explore los [scripts de PowerShell de las aplicaciones web](app-service-powershell-samples.md) previamente creados.
