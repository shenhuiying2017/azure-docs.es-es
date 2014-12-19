### (Opcional) Configure el servicio móvil de .NET para el inicio de sesión de AAD.

>[WACOM.NOTE] Estos pasos son opcionales porque solo se aplican al proveedor de inicio de sesión de Azure Active Directory.

1. Instale el paquete de NuGet **WindowsAzure.MobileServices.Backend.Security**.

2. En Visual Studio, expanda App_Start y abra el archivo WebApiConfig.cs. Agregue el siguiente código al método "Registrar", inmediatamente después de que se inicie:

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));

