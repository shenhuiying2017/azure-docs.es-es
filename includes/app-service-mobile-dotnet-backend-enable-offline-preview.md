El proyecto de servidor descargado del portal ya dispone de las funciones de datos habilitadas.

En el proyecto ASP.NET, deberá ver lo siguiente:

* Los paquetes `Microsoft.Azure.Mobile.Server.Tables` y `Microsoft.Azure.Mobile.Server.Entity` de NuGet están instalados.

* En WebApiConfig.cs, se llama al método `UseDefaultConfiguration()` en el objeto MobileAppConfiguration. Esto a su vez llama al método de extensión `AddTablesWithEntityFramework()` ofrecido por el paquete NuGet anterior.

<!---HONumber=August15_HO6-->