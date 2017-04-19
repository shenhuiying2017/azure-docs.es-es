Las aplicaciones .NET pueden usar el cliente de caché **StackExchange.Redis** , que se puede configurar en Visual Studio mediante un paquete NuGet que simplifica la configuración de aplicaciones de cliente de caché. 

> [!NOTE]
> Para obtener más información, consulte la página Github [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis) y la [documentación del cliente de caché StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis#documentation).
> 
> 

Para configurar una aplicación cliente en Visual Studio usando el paquete NuGet StackExchange.Redis, haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y elija **Administrar paquetes NuGet**. 

![Manage NuGet packages](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Escriba **StackExchange.Redis** o **StackExchange.Redis.StrongName** en el cuadro de texto de búsqueda, seleccione la versión que desee en los resultados y haga clic en **Instalar**.

> [!NOTE]
> Si prefiere usar una versión con nombre seguro de la biblioteca de cliente **StackExchange.Redis**, elija **StackExchange.Redis.StrongName**; de lo contrario, elija **StackExchange.Redis**.
> 
> 

![StackExchange.Redis NuGet package](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

El paquete de NuGet se descarga y agrega las referencias de ensamblado requeridas para que la aplicación cliente acceda a Caché en Redis de Azure con el cliente de caché StackExchange.Redis.

> [!NOTE]
> Si anteriormente ha configurado el proyecto para usar StackExchange.Redis, puede buscar actualizaciones para el paquete desde el **Administrador de paquetes NuGet**. Para buscar e instalar versiones actualizadas del paquete StackExchange.Redis NuGet, haga clic en **Actualizaciones** en la ventana del **Administrador de paquetes NuGet**. Si existe una actualización para el paquete StackExchange.Redis de NuGet, puede actualizar el proyecto para usar la versión actualizada.
> 
> 

También puede instalar el paquete NuGet StackExchange.Redis haciendo clic en **Administrador de paquetes NuGet**, **Consola del Administrador de paquetes** desde el menú **Herramientas** y ejecutando el comando siguiente en la ventana **Consola del Administrador de paquetes**.
    
```
Install-Package StackExchange.Redis
```
