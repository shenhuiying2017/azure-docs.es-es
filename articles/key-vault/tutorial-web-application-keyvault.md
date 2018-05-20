---
title: Configuración de una aplicación web de Azure para que lea un secreto desde el almacén de claves | Microsoft Docs
description: Tutorial de configuración de una aplicación ASP.NET Core para que lea un secreto desde el almacén de claves
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 04/16/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: b4e317a82b93513c6161d9da0c55883e99580cbb
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
---
# <a name="tutorial-configure-an-azure-web-application-to-read-a-secret-from-key-vault"></a>Tutorial: configuración de una aplicación web de Azure para que lea un secreto desde el almacén de claves

En este tutorial se verán los pasos necesarios para conseguir que una aplicación web de Azure lea información del almacén de claves mediante identidades de servicio administradas. Aprenderá a:

> [!div class="checklist"]
> * Crear un almacén de claves.
> * Almacenar un secreto en el almacén de claves.
> * Crear una aplicación web de Azure.
> * Habilitar las identidades de servicio administradas.
> * Conceder los permisos necesarios para que la aplicación lea los datos del almacén de claves.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tutorial es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

Para iniciar sesión en Azure mediante la CLI puede escribir:

```azurecli
az login
```

## <a name="create-resource-group"></a>Creación de un grupo de recursos

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#az_group_create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurecli
az group create --name ContosoResourceGroup --location eastus
```

El grupo de recursos que acaba de crear se utiliza a lo largo de este tutorial.

## <a name="create-an-azure-key-vault"></a>Crear una instancia de Azure Key Vault

A continuación, creará una instancia de Key Vault en el grupo de recursos creado en el paso anterior. Debe proporcionar cierta información:

>[!NOTE]
> Aunque "ContosoKeyVault" se utiliza como nombre de la instancia de Key Vault a lo largo de esta guía de inicio rápido, el nombre debe ser único.

* El nombre del almacén: **ContosoKeyVault**.
* Nombre del grupo de recursos: **ContosoResourceGroup**.
* Ubicación: **Este de EE. UU**.

```azurecli
az keyvault create --name '<YourKeyVaultName>' --resource-group ContosoResourceGroup --location eastus
```

La salida de este comando muestra las propiedades del almacén de claves que acaba de crear. Tome nota de las dos propiedades siguientes:

* **Nombre de almacén**: en este ejemplo es **ContosoKeyVault**. Utilizará el nombre de la instancia de Key Vault para todos los comandos de Key Vault.
* **Identificador URI del almacén**: en este ejemplo, https://<YourKeyVaultName>.vault.azure.net/. Las aplicaciones que utilizan el almacén a través de su API de REST deben usar este identificador URI.

>[!IMPORTANT]
> Si se produce el error que indica que el parámetro "vault_name" debe seguir el patrón siguiente: "^[a-zA-Z0-9-]{3,24}$", el valor del parámetro -name no era único o no constituía una cadena de caracteres alfanuméricos de 3 a 24 caracteres de largo.

En este momento, su cuenta de Azure es la única autorizada para realizar operaciones en este nuevo almacén.

## <a name="add-a-secret-to-key-vault"></a>Incorporación de un secreto a Key Vault

Estamos agregando un secreto para ayudar a ilustrar cómo funciona. Se puede almacenar una cadena de conexión de SQL o cualquier otra información que necesite guardar de forma segura, pero que esté disponibles para la aplicación. En este tutorial, la contraseña se llamará **AppSecret** y almacenará el valor de **MySecret**.

Escriba los siguientes comandos para crear un secreto en Key Vault denominado **AppSecret** que almacene el valor **MySecret**:

```azurecli
az keyvault secret set --vault-name '<YourKeyVaultName>' --name 'AppSecret' --value 'MySecret'
```

Para ver el valor contenido en el secreto como texto sin formato:

```azurecli
az keyvault secret show --name 'AppSecret' --vault-name '<YourKeyVaultName>'
```

Este comando muestra la información secreta, URI incluido. Después de completar estos pasos debe tener un identificador URI para un secreto en una instancia de Azure Key Vault. Tome nota de esta información. La necesitará en otro paso más adelante.

## <a name="create-a-web-app"></a>Creación de una aplicación web

En esta sección se crea una aplicación ASP.NET MVC y se implementa en Azure como aplicación web. Para obtener más información sobre Azure Web Apps, consulte [Información general de Web Apps](../app-service/app-service-web-overview.md).

1. Cree un proyecto nuevo en Visual Studio seleccionando **Archivo > Nuevo > Proyecto**. 

2. En el cuadro de diálogo **Nuevo proyecto**, seleccione **Visual C# > Web > Aplicación web ASP.NET Core**.

3. Asigne el nombre **WebKeyVault** a la aplicación y seleccione **Aceptar**.
   >[!IMPORTANT]
   > Debe asignar un nombre a la aplicación WebKeyVault para que el código que copie y pegue coincida con el espacio de nombres. Si llama al sitio de otra forma, debe modificar el código para que coincidan.

    ![Cuadro de diálogo Nuevo proyecto de ASP.NET](media/tutorial-web-application-keyvault/aspnet-dialog.png)

4. Puede implementar cualquier tipo de aplicación web ASP.NET Core en Azure. Para este tutorial, seleccione la plantilla **Aplicación web** y asegúrese de que la autenticación se establece en **Sin autenticación**.

    ![Cuadro de diálogo Sin autenticación de ASP.NET](media/tutorial-web-application-keyvault/aspnet-noauth.png)

5. Seleccione **Aceptar**.

6. Una vez creado el proyecto de ASP.NET Core, se mostrará la página de bienvenida de ASP.NET Core con numerosos vínculos a recursos que le ayudarán a empezar a trabajar.

7. En el menú, seleccione **Depurar > Iniciar sin depurar** para ejecutar la aplicación web localmente.

## <a name="modify-the-web-app"></a>Modificación de la aplicación web

Hay dos paquetes NuGet que la aplicación web debe tener instalados. Para instalarlos, siga los pasos siguientes:

1. En el Explorador de soluciones, haga clic con el botón derecho en el nombre del sitio web.
2. Seleccione **Administrar paquetes NuGet para la solución...**
3. Seleccione la casilla situada junto al cuadro de búsqueda. **Incluya la versión preliminar**.
4. Busque los dos paquetes NuGet que se enumeran a continuación y acepte para que agreguen a la solución:

    * [Microsoft.Azure.Services.AppAuthentication (versión preliminar)](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication): facilita la recuperación de cambios en los tokens de acceso para los escenarios de autenticación de servicio a servicio de Azure. 
    * [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/2.4.0-preview): contiene métodos para interactuar con Key Vault.

5. Use el Explorador de soluciones para abrir `Program.cs` y reemplace el contenido del archivo Program.cs por el código siguiente. Reemplace ```<YourKeyVaultName>``` por el nombre del almacén de claves:

    ```csharp
    
    using Microsoft.AspNetCore;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureKeyVault;
    
        namespace WebKeyVault
        {
        public class Program
        {
        public static void Main(string[] args)
        {
        BuildWebHost(args).Run();
        }
    
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
                }
             )
                .UseStartup<Startup>()
                .Build();
    
            private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
        }
        }
    ```

6. Utilice el Explorador de soluciones para ir a la sección **Páginas** y abra `About.cshtml`. Reemplace el contenido del archivo **About.cshtml.cs** por el código siguiente:

    ```csharp
    
    using Microsoft.AspNetCore.Mvc.RazorPages;
    using Microsoft.Extensions.Configuration;
    
    namespace WebKeyVault.Pages
    {
        public class AboutModel : PageModel
        {
            public AboutModel(IConfiguration configuration)
            {
                _configuration = configuration;
            }
    
            private readonly IConfiguration _configuration = null;
            public string Message { get; set; }
    
            public void OnGet()
            {
                Message = "My key val = " +  _configuration["AppSecret"];
            }
        }
    }
    
    ```

7. En el menú principal, elija **Depurar** > **Iniciar sin depurar**. Cuando aparezca el explorador, vaya a la página **Acerca de**. Aparecerá el valor de AppSecret.

>[!IMPORTANT]
> Si se produce un mensaje HTTP de error 502.5 (error de procesamiento), verifique el nombre de la instancia de Key Vault especificado en `Program.cs`

## <a name="publish-the-web-application-to-azure"></a>Publicación de la aplicación web en Azure

1. Encima del editor, seleccione **WebKeyVault**.
2. Seleccione **Publicar**.
3. Seleccione **Publicar** de nuevo.
4. Seleccione **Crear**.

>[!IMPORTANT]
> Se abrirá una ventana del explorador y verá un mensaje 502.5 de error de procesamiento. Se espera que esto sea así. Debe conceder derechos de identidad de aplicación para leer los secretos de Key Vault.

## <a name="enable-managed-service-identity"></a>Habilitación de la Identidad de servicio administrada

Azure Key Vault proporciona una manera de almacenar de forma segura las credenciales y otras claves y secretos, pero el código tiene que autenticarse en Key Vault para recuperarlos. Para solucionar este problema, Managed Service Identity (MSI) proporciona a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory (Azure AD). Puede usar esta identidad para autenticar cualquier servicio que admita la autenticación de Azure AD, incluido Key Vault, sin necesidad de tener credenciales en el código.

1. Vuelva a la CLI de Azure.
2. Ejecute el comando assign-identity para crear la identidad de esta aplicación:

```azurecli
az webapp assign-identity --name WebKeyVault --resource-group ContosoResourcegroup
```

>[!NOTE]
>Este es el equivalente de ir al portal y cambiar la **Identidad de servicio administrada** a **Activada** en las propiedades de la aplicación web.

## <a name="grant-rights-to-the-application-identity"></a>Concesión de derechos a la identidad de aplicación

Desde Azure Portal, vaya a las directivas de acceso de Key Vault y concédase el acceso de administración de secretos para Key Vault. Esto le permitirá ejecutar la aplicación en la máquina de desarrollo local.

1. Busque el la instancia de Key Vault en el cuadro de diálogo **Buscar recursos** de Azure Portal.
2. Seleccione **Directivas de acceso**.
3. Seleccione **Agregar nuevo**, en la sección **Permisos de secretos**, seleccione **Obtener** y **Lista**.
4. Seleccione **Seleccionar la entidad de seguridad** y agregue la identidad de aplicación. Tendrá el mismo nombre que la aplicación.
5. Selección de **Aceptar**.

Ahora la cuenta de Azure y la identidad de aplicación tendrán derechos para leer información de Key Vault. Si actualiza la página verá la página de inicio del sitio. Si selecciona **Acerca de** verá que el valor almacenado en Key Vault.

## <a name="clean-up-resources"></a>Limpieza de recursos

Para eliminar un grupo de recursos y todos sus recursos, use el comando **az group delete**.

  ```azurecli
  az group delete -n ContosoResourceGroup
  ```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Guía del desarrollador de Azure Key Vault](key-vault-developers-guide.md)
