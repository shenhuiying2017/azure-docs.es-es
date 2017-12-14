---
title: "Guardar de forma segura la configuración del secreto de la aplicación para una aplicación web | Microsoft Docs"
description: "Cómo guardar de forma segura la configuración del secreto de la aplicación, por ejemplo, las credenciales de Azure o las claves de API de un tercero, con los generadores de configuraciones de .NET 4.7.1, User Secret o el proveedor de Key Vault de ASP.NET Core."
services: visualstudio
documentationcenter: 
author: cawa
manager: paulyuk
editor: 
ms.assetid: 
ms.service: 
ms.workload: web, azure
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cawa
ms.openlocfilehash: 612b8d2c36e9b46e99452e78d1b30fda03474151
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Guardar de forma segura la configuración del secreto de la aplicación para una aplicación web

## <a name="overview"></a>Información general
En este artículo se describe cómo guardar de forma segura las opciones de configuración del secreto de la aplicación para aplicaciones de Azure.

Tradicionalmente, las opciones de configuración de las aplicaciones web se guardaban en archivos de configuración como Web.config. Esta práctica conduce al registro de las opciones de configuración del secreto, como credenciales de la nube, para sistemas de control de código fuente públicos como GitHub. Mientras tanto, puede resultar difícil seguir los procedimientos recomendados de seguridad debido a los gastos necesarios para modificar el código fuente y volver a configurar las opciones de desarrollo.

Para asegurarse de que el proceso de desarrollo es seguro, se crean bibliotecas de herramientas y de marco para guardar las opciones de configuración del secreto de la aplicación de forma segura con pocos cambios en el código fuente o ninguno.

## <a name="aspnet-and-net-core-applications"></a>Aplicaciones ASP.NET y .NET Core

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Guardar la configuración del secreto en el almacén de User Secret que se encuentra fuera de la carpeta de control de código fuente
Si está realizando un prototipo rápido o no tiene acceso a Internet, comience por sacar la configuración del secreto de la carpeta de control de código fuente y pasarla al almacén de User Secret. El almacén de User Secret es un archivo que se guarda en la carpeta del generador de perfiles de usuario, así que los secretos no se registran en el control de código fuente. En el diagrama siguiente se muestra cómo funciona [User Secret](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio#SecretManager).

![User Secret guarda las opciones de configuración del secreto fuera del control de código fuente](./media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Si está ejecutando la aplicación de consola .NET Core, use Key Vault para guardar el secreto de forma segura.

### <a name="save-secret-settings-in-azure-key-vault"></a>Guardar la configuración del secreto en Azure Key Vault
Si está desarrollando un proyecto de equipo y tiene que compartir código fuente de forma segura, utilice [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Cree una instancia de Key Vault en la suscripción de Azure. Rellene todos los campos necesarios en la interfaz de usuario y haga clic en *Crear* en la parte inferior de la hoja.

    ![Crear una instancia de Azure Key Vault](./media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Concédase a usted mismo y a los miembros del equipo acceso a Key Vault. Si tiene un equipo grande, puede crear un [grupo de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal) y agregar acceso a ese grupo de seguridad para Key Vault. En la lista desplegable *Permisos de secretos*, marque *Get* (Obtener) y *List* (Lista) en *Secret Management Operations* (Operaciones de administración de secretos).

    ![Agregar directiva de acceso de Key Vault](./media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Agregue su secreto a Key Vault en Azure Portal. Para los valores de configuración anidados, reemplace ":" con "--" para que el nombre del secreto de Key Vault sea válido. No se permite que ":" se incluya en el nombre de un secreto de Key Vault.

    ![Agregar secreto de Key Vault](./media/vs-secure-secret-appsettings/add-keyvault-secret.png)

4. Instale la [extensión Azure Services Authentication para Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354). A través de esta extensión, la aplicación puede tener acceso a Key Vault con la identidad de inicio de sesión de Visual Studio.

5. Agregue los siguientes paquetes de NuGet al proyecto:

    ```
    Microsoft.Azure.Services.AppAuthentication
    ```
6. Agregue el código siguiente al archivo Program.cs:

    ```csharp
    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((ctx, builder) =>
            {
                var keyVaultEndpoint = GetKeyVaultEndpoint();
                if (!string.IsNullOrEmpty(keyVaultEndpoint))
                {
                    var azureServiceTokenProvider = new AzureServiceTokenProvider();
                    var keyVaultClient = new KeyVaultClient(
                        new KeyVaultClient.AuthenticationCallback(
                            azureServiceTokenProvider.KeyVaultTokenCallback));
                            builder.AddAzureKeyVault(
                            keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                        }
                    })
                    .UseStartup<Startup>()
                    .Build();

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
7. Agregue la dirección URL de Key Vault al archivo de launchsettings.json. El nombre de la variable de entorno *KEYVAULT_ENDPOINT* se define en el código que agregó en el paso 6.

    ![Agregar la URL de Key Vault como una variable de entorno del proyecto](./media/vs-secure-secret-appsettings/add-keyvault-url.png)

8. Inicie la depuración del proyecto. Debería ejecutarse correctamente.

## <a name="aspnet-and-net-applications"></a>Aplicaciones ASP.NET y .NET

.NET 4.7.1 admite los generadores de configuraciones de Key Vault y Secret, lo que garantiza que los secretos pueden sacarse de la carpeta de control de código fuente sin que se produzcan cambios en el código.
Para continuar, [descargue .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) y migre la aplicación si está utilizando una versión anterior de .NET Framework.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Guardar la configuración del secreto en un archivo secreto fuera de la carpeta de control de código fuente
Si está escribiendo un prototipo rápido y no quiere aprovisionar recursos de Azure, esta es la opción perfecta.

1. Instale el siguiente paquete NuGet en el proyecto.
    ```
    Microsoft.Configuration.ConfigurationBuilders.Basic.1.0.0-alpha1.nupkg
    ```

2. Cree un archivo similar al siguiente. Guárdelo en una ubicación fuera de la carpeta del proyecto.

    ```xml

       <root>
              <secrets ver="1.0">
                     <secret name="secret1" value="foo_one" />
                        <secret name="secret2" value="foo_two" />
                       </secrets>
      </root>
      ```

3. Defina el archivo del secreto para que sea un generador de configuración en el archivo Web.config. Coloque esta sección antes de la sección *appSettings*.

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. Especifique que la sección appSettings está usando el generador de configuración del secreto. Asegúrese de que no hay ninguna entrada para el valor secreto con un valor ficticio.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Depure la aplicación. Debería ejecutarse correctamente.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Guardar la configuración del secreto en Azure Key Vault
Siga las instrucciones de la sección ASP.NET Core para configurar una instancia de Key Vault para el proyecto.

1. Instale el siguiente paquete NuGet en el proyecto.
```
Microsoft.Configuration.ConfigurationBuilders.Azure.1.0.0-alpha1.nupkg
```

2. Definir el generador de configuración de Key Vault en Web.config. Coloque esta sección antes de la sección *appSettings*. Reemplace *vaultName* para que sea el nombre de Key Vault si su instancia de Key Vault está en una instancia de Azure pública, o un URI completo si está usando una nube soberana.

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>
    <configBuilders>
        <builders>
            <add name="KeyVault" vaultName="Test911" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```
3.  Especifique que la sección appSettings está usando el generador de configuración de Key Vault. Asegúrese de que no hay ninguna entrada para el valor secreto con un valor ficticio.

    ```xml
    <appSettings configBuilders="AzureKeyVault">
        <add key="webpages:Version" value="3.0.0.0" />
        <add key="webpages:Enabled" value="false" />
        <add key="ClientValidationEnabled" value="true" />
        <add key="UnobtrusiveJavaScriptEnabled" value="true" />
        <add key="secret" value="" />
    </appSettings>
    ```

4. Inicie la depuración del proyecto. Debería ejecutarse correctamente.
