### (Opcional) Configure el servicio móvil de .NET para el inicio de sesión de AAD.

>[AZURE.NOTE] Estos pasos son opcionales porque solo se aplican al proveedor de inicio de sesión de Azure Active Directory.

1. Instalar el paquete NuGet **WindowsAzure.MobileServices.Backend.Security**.

2. En Visual Studio, expanda App_Start y abra el archivo WebApiConfig.cs. Agregue el siguiente código al método  `Register`, inmediatamente después de que se inicie  `options`:

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));


<!--HONumber=42-->
