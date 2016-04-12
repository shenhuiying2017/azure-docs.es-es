### Análisis de la cadena de conexión

La biblioteca de Microsoft Azure Configuration Manager, mencionada anteriormente, proporciona una clase para analizar una cadena de conexión desde un archivo de configuración. La [clase CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) analiza las opciones de configuración, con independencia de si se está ejecutando la aplicación cliente en el escritorio, en un dispositivo móvil, en una máquina virtual de Azure o en un servicio en la nube de Azure.

Agregue el siguiente código al método `Main()`:

    // Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

El uso del Administrador de configuración Azure es opcional. También se puede utilizar una API como la [clase ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) de .NET Framework.

<!---HONumber=AcomDC_0406_2016-->