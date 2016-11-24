La [biblioteca del Administrador de configuración de Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) proporciona una clase para analizar una cadena de conexión desde un archivo de configuración. La [clase CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) analiza los valores de configuración con independencia de si la aplicación cliente se ejecuta en el equipo de escritorio, en un dispositivo móvil, en una máquina virtual de Azure o en un servicio en la nube de Azure.

Para hacer referencia al paquete CloudConfigurationManager, agregue la siguiente instrucción `using` a su clase:

```csharp
using Microsoft.Azure;    //Namespace for CloudConfigurationManager
```

Este es un ejemplo que muestra cómo recuperar una cadena de conexión de un archivo de configuración:
```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```
El uso del Administrador de configuración Azure es opcional. También se puede utilizar una API como la [clase ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx)de .NET Framework.



<!--HONumber=Nov16_HO3-->


