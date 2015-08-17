El proyecto de servidor descargado del portal ya dispone de la autenticación habilitada.

En el proyecto ASP.NET, deberá ver lo siguiente:

* El paquete `Microsoft.Azure.Mobile.Server.Authentication` NuGet está instalado.

* En WebApiConfig.cs, se llama al método `UseDefaultConfiguration()` en el objeto MobileAppConfiguration. Esto a su vez llama al método de extensión `AddAppServiceAuthentication()` ofrecido por el paquete NuGet anterior. También registra un middleware de OWIN necesario para la autenticación mediante una llamada a `app.UseAppServiceAuthentication()`durante el inicio OWIN.

<!---HONumber=August15_HO6-->