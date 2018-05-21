---
title: Creación de una aplicación web de ASP.NET con Redis Cache | Microsoft Docs
description: En esta guía de inicio rápido, aprenderá a crear una aplicación web de ASP.NET con Redis Cache
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 454e23d7-a99b-4e6e-8dd7-156451d2da7c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: quickstart
ms.date: 03/26/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 7bf08849cd03a77095ffe717c8387d79d1961b06
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-aspnet-web-app-with-redis-cache"></a>Guía de inicio rápido: Creación de una aplicación web de ASP.NET con Redis Cache

> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
>

## <a name="introduction"></a>Introducción

En esta guía de inicio rápido se muestra cómo crear e implementar una aplicación web de ASP.NET en Azure App Service mediante Visual Studio 2017. La aplicación de ejemplo se conecta a una instancia de Azure Redis Cache para almacenar y recuperar datos de la caché. Al finalizar la guía de inicio rápido, tendrá una aplicación web en ejecución hospedada en Azure que lee y escribe en una instancia de Azure Redis Cache.

![Prueba sencilla completada en Azure](./media/cache-web-app-howto/cache-simple-test-complete-azure.png)

## <a name="prerequisites"></a>requisitos previos

Para seguir los pasos de esta guía de inicio rápido, debe cumplir los siguientes requisitos previos:

* Instalar [Visual Studio 2017](https://www.visualstudio.com/downloads/) con las cargas de trabajo siguientes:
    * ASP.NET y desarrollo web
    * Desarrollo de Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-visual-studio-project"></a>Creación del proyecto de Visual Studio

Abra Visual Studio y haga clic en **Archivo**, **Nuevo**, **Proyecto**.

![Crear proyecto](./media/cache-web-app-howto/cache-create-project.png)

En el cuadro de diálogo Nuevo proyecto, realice estos pasos:

1. Expanda el nodo **Visual C#** en la lista **Plantillas**.
1. Seleccione **Nube**.
1. Haga clic en **Aplicación web ASP.NET**.
1. Asegúrese de que se selecciona **.NET Framework 4.5.2** o superior.
1. Asigne un nombre al proyecto en el cuadro de texto **Nombre**; en este ejemplo, hemos usado **ContosoTeamStats**.
1. Haga clic en **OK**.

Aparecerá una pantalla para una nueva aplicación web de ASP.NET:

![Seleccionar plantilla de proyecto](./media/cache-web-app-howto/cache-select-template.png)

Seleccione **MVC** como tipo de proyecto.

Asegúrese de especificar **Sin autenticación** en la opción **Autenticación**. Dependiendo de la versión de Visual Studio, puede establecerse otro valor predeterminado. Para cambiarlo, haga clic en **Cambiar autenticación** y seleccione **Sin autenticación**.

Haga clic en **Aceptar** para crear el proyecto.

## <a name="create-a-cache"></a>Creación de una caché

A continuación, creará la caché de la aplicación.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Cree un archivo en el equipo llamado *CacheSecrets.config* y colóquelo en una ubicación donde no se vaya a insertar en el repositorio con el código fuente de la aplicación de ejemplo. En esta guía de inicio rápido, el archivo *CacheSecrets.config* se encuentra aquí, *C:\AppSecrets\CacheSecrets.config*.

Edite el archivo *CacheSecrets.config* y agregue el contenido siguiente:

```xml
<appSettings>
    <add key="CacheConnection" value="<cache-name>.redis.cache.windows.net,abortConnect=false,ssl=true,password=<access-key>"/>
</appSettings>
```

Reemplace `<cache-name>` por su nombre de host de caché.

Reemplace `<access-key>` por la clave principal de la caché.

> [!TIP]
> La clave de acceso secundaria se usa durante la rotación de claves como una clave alternativa mientras vuelve a generar la clave de acceso principal.
>

Guarde el archivo.

## <a name="update-the-mvc-application"></a>Actualización de la aplicación MVC

En esta sección, actualizará la aplicación para admitir una nueva vista que mostrará una prueba sencilla realizada para una instancia de Azure Redis Cache.

* [Actualización del archivo web.config con una configuración de aplicación para la caché](#Update-the-webconfig-file-with-an-app-setting-for-the-cache)
* [Configuración de la aplicación para usar el cliente de StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
* [Actualización de HomeController y Layout](#update-the-homecontroller-and-layout)
* [Adición de una nueva vista de RedisCache](#add-a-new-rediscache-view)

### <a name="update-the-webconfig-file-with-an-app-setting-for-the-cache"></a>Actualización del archivo web.config con una configuración de aplicación para la caché

Cuando se ejecuta la aplicación localmente, la información de *CacheSecrets.config* se usa para conectarse a la instancia de Azure Redis Cache. Más adelante se implementará esta aplicación en Azure. En ese momento, configurará una configuración de aplicación en Azure que la aplicación usará para recuperar la información de conexión de la caché en lugar de este archivo. Puesto que *CacheSecrets.config* no se ha implementado en Azure con la aplicación, solo lo usará al probar la aplicación localmente. Es importante mantener esta información lo más segura posible para impedir el acceso malintencionado a los datos de la caché.

En el **Explorador de soluciones**, haga doble clic en el archivo *web.config* para abrirlo.

![Web.config](./media/cache-web-app-howto/cache-web-config.png)

En el archivo *web.config*, busque el elemento `<appSetting>` y agregue el siguiente atributo `file`. Si utiliza un nombre de archivo o una ubicación diferente, sustituya los valores por los que se muestran en el ejemplo.

* Antes: `<appSettings>`
* Después: ` <appSettings file="C:\AppSecrets\CacheSecrets.config">`

El sistema en tiempo de ejecución de ASP.NET combina el contenido del archivo externo con las marcas del elemento `<appSettings>` . El tiempo de ejecución omite el atributo de archivo si no se encuentra el archivo especificado. Los secretos (cadena de conexión a la caché) no se incluyen como parte del código fuente de la aplicación. Al implementar la aplicación web en Azure, el archivo *CacheSecrests.config* no se implementará.

### <a name="configure-the-application-to-use-stackexchangeredis"></a>Configurar la aplicación para usar StackExchange.Redis

Para configurar la aplicación para usar el paquete NuGet [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) para Visual Studio, haga clic en **Herramientas > Administrador de paquetes NuGet > Consola del Administrador de paquetes**.

Ejecute el siguiente comando desde la ventana `Package Manager Console`:

```powershell
Install-Package StackExchange.Redis
```

El paquete de NuGet se descarga y agrega las referencias de ensamblado requeridas para que la aplicación cliente acceda a Azure Redis Cache con el cliente de caché StackExchange.Redis. Si prefiere usar una versión con nombre seguro de la biblioteca de cliente `StackExchange.Redis`, instale el paquete `StackExchange.Redis.StrongName`.

### <a name="update-the-homecontroller-and-layout"></a>Actualización de HomeController y Layout

En el **Explorador de soluciones**, expanda la carpeta **Controllers** y abra el archivo *HomeController.cs*.

Agregue las dos siguientes instrucciones `using` en la parte superior del archivo para admitir el cliente de caché y la configuración de aplicación.

```csharp
using System.Configuration;
using StackExchange.Redis;
```

Agregue el método siguiente a la clase `HomeController` para admitir una nueva acción `RedisCache` que ejecuta algunos comandos en la nueva caché.

```csharp
    public ActionResult RedisCache()
    {
        ViewBag.Message = "A simple example with Azure Redis Cache on ASP.NET.";

        var lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });

        // Connection refers to a property that returns a ConnectionMultiplexer
        // as shown in the previous example.
        IDatabase cache = lazyConnection.Value.GetDatabase();

        // Perform cache operations using the cache object...

        // Simple PING command
        ViewBag.command1 = "PING";
        ViewBag.command1Result = cache.Execute(ViewBag.command1).ToString();

        // Simple get and put of integral data types into the cache
        ViewBag.command2 = "GET Message";
        ViewBag.command2Result = cache.StringGet("Message").ToString();

        ViewBag.command3 = "SET Message \"Hello! The cache is working from ASP.NET!\"";
        ViewBag.command3Result = cache.StringSet("Message", "Hello! The cache is working from ASP.NET!").ToString();

        // Demostrate "SET Message" executed as expected...
        ViewBag.command4 = "GET Message";
        ViewBag.command4Result = cache.StringGet("Message").ToString();

        // Get the client list, useful to see if connection list is growing...
        ViewBag.command5 = "CLIENT LIST";
        ViewBag.command5Result = cache.Execute("CLIENT", "LIST").ToString().Replace(" id=", "\rid=");

        lazyConnection.Value.Dispose();

        return View();
    }
```

En el **Explorador de soluciones**, expanda la carpeta **Vistas**>**Compartido** y abra el archivo *_Layout.cshtml*.

Sustituya:

```csharp
@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
```

Por:

```csharp
@Html.ActionLink("Azure Redis Cache Test", "RedisCache", "Home", new { area = "" }, new { @class = "navbar-brand" })
```

### <a name="add-a-new-rediscache-view"></a>Adición de una nueva vista de RedisCache

En el **Explorador de soluciones**, expanda la carpeta **Vistas** y, luego, haga clic con el botón derecho en la carpeta **Inicio**. Elija **Agregar** > **Ver...** .

En el cuadro de diálogo Agregar vista, escriba **RedisCache** como nombre de la vista y haga clic en **Agregar**.

Reemplace el código del archivo *RedisCache.cshtml* por el código siguiente:

```csharp
@{
    ViewBag.Title = "Azure Redis Cache Test";
}

<h2>@ViewBag.Title.</h2>
<h3>@ViewBag.Message</h3>
<br /><br />
<table border="1" cellpadding="10">
    <tr>
        <th>Command</th>
        <th>Result</th>
    </tr>
    <tr>
        <td>@ViewBag.command1</td>
        <td><pre>@ViewBag.command1Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command2</td>
        <td><pre>@ViewBag.command2Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command3</td>
        <td><pre>@ViewBag.command3Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command4</td>
        <td><pre>@ViewBag.command4Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command5</td>
        <td><pre>@ViewBag.command5Result</pre></td>
    </tr>
</table>
```

## <a name="run-the-app-locally"></a>Ejecución de la aplicación de forma local

De forma predeterminada, el proyecto está configurado para hospedar la aplicación localmente en [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview) para pruebas y depuración.

En Visual Studio en el menú, haga clic en **Depurar** > **Iniciar depuración** para compilar e iniciar la aplicación localmente para pruebas y depuración.

En el explorador, haga clic en **Azure Redis Cache Test** (Prueba de Azure Redis Cache) en la barra de navegación.

En el ejemplo siguiente, puede ver que la clave `Message` tenía anteriormente un valor almacenado en caché, que se estableció mediante la Consola de Redis en el portal. La aplicación actualizó ese valor almacenado en caché. La aplicación también ejecutó los comandos `PING` y `CLIENT LIST`.

![Prueba sencilla finalizada localmente](./media/cache-web-app-howto/cache-simple-test-complete-local.png)

## <a name="publish-and-run-in-azure"></a>Publicación y ejecución en Azure

Una vez que haya probado con éxito la aplicación localmente, la implementará en Azure y la ejecutará en la nube.

### <a name="publish-the-app-to-azure"></a>Publicación de la aplicación en Azure

En Visual Studio, haga clic con el botón derecho en el nodo del proyecto en el Explorador de soluciones y elija **Publicar**.

![Publicar](./media/cache-web-app-howto/cache-publish-app.png)

Haga clic en **Microsoft Azure App Service**, elija **Crear nuevo** y haga clic en **Publicar**.

![Publicación en el servicio de aplicaciones](./media/cache-web-app-howto/cache-publish-to-app-service.png)

En el cuadro de diálogo **Crear servicio de aplicaciones**, realice los cambios siguientes:

| Configuración | Valor recomendado | DESCRIPCIÓN |
| ------- | :---------------: | ----------- |
| **Nombre de aplicación** | Use el valor predeterminado | El nombre de la aplicación será el nombre de host de la aplicación cuando se implementa en Azure. El nombre puede tener un sufijo de marca de tiempo que se le agrega, si es necesario, para que sea único. |
| **Suscripción** | Elegir la suscripción de Azure | En esta suscripción se cargarán los costos de hospedaje relacionados. Si tiene varias suscripciones de Azure, compruebe que se selecciona la suscripción deseada.|
| **Grupo de recursos** | Use el mismo grupo de recursos que donde creó la caché. Por ejemplo, *TestResourceGroup*. | El grupo de recursos le ayuda a administrar todos los recursos como un grupo. Más adelante, puede eliminar simplemente el grupo si desea eliminar la aplicación. |
| **Plan de App Service** | Haga clic en **Nuevo** y cree un nuevo plan de App Service llamado *TestingPlan*. <br />Use la misma **ubicación** que utilizó al crear la caché. <br />Elija **Libre** para el tamaño. | Un plan de App Service define un conjunto de recursos de proceso con los que se ejecuta una aplicación web. |

![Cuadro de diálogo de App Service](./media/cache-web-app-howto/cache-create-app-service-dialog.png)

Una vez haya configurado los valores de hospedaje de App Service, haga clic en **Crear** para crear una nueva instancia de App Service para su aplicación.

Supervise la ventana **Salida** en Visual Studio para ver el estado de la publicación en Azure. Cuando haya finalizado la publicación, la dirección URL de App Service se registrará como se muestra a continuación:

![Salida de publicación](./media/cache-web-app-howto/cache-publishing-output.png)

### <a name="add-the-app-setting-for-the-cache"></a>Adición de la configuración de aplicación para la caché

Una vez finalizada la publicación de la nueva instancia de App Service, agregue una nueva configuración de aplicación. Esta configuración se usará para almacenar la información de conexión de caché. Escriba el nombre de la aplicación en la barra de búsqueda en la parte superior de Azure Portal para buscar la nueva instancia de App Service que acaba de crear.

![Búsqueda de App Service](./media/cache-web-app-howto/cache-find-app-service.png)

Agregue una nueva configuración de aplicación llamada **CacheConnection** para la aplicación que se usará para conectarse a la caché. Use el mismo valor que configuró para `CacheConnection` en el archivo *CacheSecrets.config*. El valor contiene la clave de acceso y el nombre de host de la caché.

![Adición de la configuración de aplicación](./media/cache-web-app-howto/cache-add-app-setting.png)

### <a name="run-the-app-in-azure"></a>Ejecución de la aplicación en Azure

En el explorador, vaya a la dirección URL de App Service. La dirección URL se muestra en los resultados de la operación de publicación en la ventana de salida de Visual Studio. También se proporciona en Azure Portal en la página de información general de la instancia de App Service que creó.

Haga clic en **Azure Redis Cache Test** (Prueba de Azure Redis Cache) en la barra de navegación para probar el acceso a la caché.

![Prueba sencilla completada en Azure](./media/cache-web-app-howto/cache-simple-test-complete-azure.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Si va a seguir con el tutorial siguiente, puede mantener los recursos creados en esta guía de inicio rápido y volverlos a utilizar.

En caso contrario, si ya ha terminado con la aplicación de ejemplo de la guía de inicio rápido, puede eliminar los recursos de Azure creados en este tutorial para evitar cargos. 

> [!IMPORTANT]
> La eliminación de un grupo de recursos es irreversible y el grupo de recursos y todos los recursos que contiene se eliminarán de forma permanente. Asegúrese de no eliminar por accidente el grupo de recursos o los recursos equivocados. Si ha creado los recursos para hospedar este ejemplo dentro de un grupo de recursos existente que contiene recursos que desea mantener, puede eliminar cada recurso individualmente de sus hojas respectivas, en lugar de eliminar el grupo de recursos.
>

Inicie sesión en el [Portal de Azure](https://portal.azure.com) y haga clic en **Grupos de recursos**.

Escriba el nombre del grupo de recursos en el cuadro de texto **Filtrar por nombre...**. En las instrucciones de este tema se usa un grupo de recursos llamado *TestResources*. En el grupo de recursos de la lista de resultados, haga clic en **...** y, a continuación, en **Eliminar grupo de recursos**.

![Eliminar](./media/cache-web-app-howto/cache-delete-resource-group.png)

Se le pedirá que confirme la eliminación del grupo de recursos. Escriba el nombre del grupo de recursos para confirmar y haga clic en **Eliminar**.

Transcurridos unos segundos, el grupo de recursos y todos los recursos que contiene se eliminan.

## <a name="next-steps"></a>Pasos siguientes

En el siguiente tutorial, usará Azure Redis Cache en un escenario del mundo real para mejorar el rendimiento de una aplicación. Actualizará esta aplicación para almacenar en caché los resultados de la tabla de clasificación mediante el patrón cache-aside con ASP.NET y una base de datos.

> [!div class="nextstepaction"]
> [Creación de una tabla de clasificación cache-aside en ASP.NET](cache-web-app-cache-aside-leaderboard.md)
