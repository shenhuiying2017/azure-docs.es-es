El proyecto de servidor descargado del portal ya tiene notificaciones push habilitadas.

En el proyecto ASP.NET, puede comprobar lo siguiente:

* El paquete `Microsoft.Azure.Mobile.Server.Notifications` NuGet está instalado.

* En WebApiConfig.cs, se llama al método `UseDefaultConfiguration()` en el objeto MobileAppConfiguration. Esto a su vez llama al método de extensión `AddPushNotifications()` ofrecido por el paquete NuGet anterior.

<!---HONumber=August15_HO8-->