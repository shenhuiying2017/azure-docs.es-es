---
title: "Autenticación entre servicios en Azure Key Vault mediante .NET"
description: Utilice la biblioteca Microsoft.Azure.Services.AppAuthentication para autenticar en Azure Key Vault mediante .NET.
keywords: "credenciales locales de autenticación de azure key-vault"
author: lleonard-msft
manager: mbaldwin
services: key-vault
ms.author: alleonar
ms.date: 11/15/2017
ms.topic: article
ms.prod: 
ms.service: microsoft-keyvault
ms.technology: 
ms.assetid: 4be434c4-0c99-4800-b775-c9713c973ee9
ms.openlocfilehash: bff4b15ca2f1c985c4b4e27d159adaa5fd039553
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Autenticación entre servicios en Azure Key Vault mediante .NET

Para autenticar en Azure Key Vault, necesitará una credencial de Azure Active Directory (AD), un secreto compartido o un certificado. La administración de estas credenciales puede ser complicada y resulta tentador agrupar las credenciales en una aplicación incluyéndolas en archivos de configuración o de origen.

La biblioteca `Microsoft.Azure.Services.AppAuthentication` para .NET simplifica este problema. Utiliza las credenciales del desarrollador para la autenticación durante el desarrollo local. Cuando la solución se implementa más adelante en Azure, la biblioteca cambia automáticamente a las credenciales de la aplicación.  

El uso de credenciales de desarrollador durante el desarrollo local es más seguro porque no es necesario crear credenciales de Azure AD o compartir credenciales entre los programadores.

La biblioteca `Microsoft.Azure.Services.AppAuthentication` administra la autenticación automáticamente, que a su vez le permite centrarse en la solución, en lugar de en las credenciales.

La biblioteca `Microsoft.Azure.Services.AppAuthentication` admite el desarrollo local con Microsoft Visual Studio, la CLI de Azure o Azure AD Integrated Authentication (Autenticación integrada de Azure AD). Cuando se implementa en Azure App Services o en una máquina virtual (VM) de Azure, la biblioteca usa automáticamente [Identidad de servicio administrada](/azure/active-directory/msi-overview) (MSI). No se requieren cambios de configuración o código. La biblioteca también admite el uso directo de las [credenciales de cliente](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) de Azure AD cuando MSI no está disponible o cuando no se puede determinar el contexto de seguridad del desarrollador durante el desarrollo local.

<a name="asal"></a>
## <a name="using-the-library"></a>Uso de la biblioteca

Para aplicaciones. NET, la manera más sencilla de trabajar con una identidad de servicio administrada (MSI) es a través del paquete `Microsoft.Azure.Services.AppAuthentication`. Aquí tiene información sobre cómo empezar:

1. Agregue una referencia al paquete de NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) para la aplicación.

2. Agregue el siguiente código:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // ...

    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(
    azureServiceTokenProvider.KeyVaultTokenCallback));

    // or

    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync(
       "https://management.azure.com/").ConfigureAwait(false);
    ```

La `AzureServiceTokenProvider` clase almacena el token en la memoria y lo recupera de Azure AD justo antes de que expire. Por lo tanto, ya no tiene que comprobar la expiración antes de llamar al método `GetAccessTokenAsync`. Simplemente llame al método cuando desee utilizar el token. 

El método `GetAccessTokenAsync` requiere un identificador de recursos. Para más información, vea [¿Qué servicios de Azure son compatibles con Identidad de servicio administrada?](https://docs.microsoft.com/azure/active-directory/msi-overview#which-azure-services-support-managed-service-identity)


<a name="samples"></a>
## <a name="samples"></a>Muestras

Los siguientes ejemplos muestran la biblioteca `Microsoft.Azure.Services.AppAuthentication` en acción:

1. [Use una identidad de servicio administrada (MSI) para recuperar un secreto de almacén de Azure Key Vault en tiempo de ejecución](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

2. [Implemente mediante programación una plantilla de Azure Resource Manager desde una máquina virtual de Azure con una MSI](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Use el ejemplo .NET Core y MSI para llamar a los servicios de Azure desde una máquina virtual Linux de Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).


<a name="local"></a>
## <a name="local-development-authentication"></a>Autenticación de desarrollo local

Para el desarrollo local, hay dos escenarios de autenticación principales:

- [Autenticación en los servicios de Azure](#authenticating-to-azure-services)
- [Autenticación en los servicios personalizados](#authenticating-to-custom-services)

A continuación, aprenderá los requisitos para cada escenario y las herramientas compatibles.


### <a name="authenticating-to-azure-services"></a>Autenticación en los servicios de Azure

Los equipos locales no son compatibles con la identidad de servicio administrada (MSI).  Como resultado, la biblioteca `Microsoft.Azure.Services.AppAuthentication` utiliza las credenciales de desarrollador para la ejecución en el entorno de desarrollo local. Cuando la solución se implementa en Azure, la biblioteca usa MSI para cambiar a un flujo de concesión de credenciales de cliente de OAuth 2.0.  Esto significa que puede probar el mismo código local y remotamente sin preocuparse.

Para el desarrollo local, `AzureServiceTokenProvider` captura tokens mediante **Visual Studio**, la **interfaz de línea de comandos de Azure** (CLI) o **la autenticación integrada de Azure AD**. Cada opción se prueba secuencialmente y la biblioteca usa la primera opción correcta. Si no funciona ninguna opción, se produce una excepción `AzureServiceTokenProviderException` con información detallada.

### <a name="authenticating-with-visual-studio"></a>Autenticación con Visual Studio

Para usar Visual Studio, compruebe lo siguiente:

1. Ha instalado [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) o una versión superior.

2. La [extensión de Autenticación de aplicación para Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354) está instalada.
 
3. Ha iniciado sesión en Visual Studio y ha seleccionado una cuenta para utilizarla para el desarrollo local. Use **Herramientas**&nbsp;>&nbsp;**Opciones**&nbsp;>&nbsp;**Azure Service Authentication** (Autenticación de servicio de Azure) para elegir una cuenta de desarrollo local. 

Si experimenta problemas con Visual Studio, como errores relacionados con el archivo del proveedor de tokens, revise atentamente estos pasos. 

Puede que sea necesario volver a autenticar el token del desarrollador.  Para ello, vaya a **Herramientas**&nbsp;>&nbsp;**Opciones**>**Azure&nbsp;Service&nbsp;Authentication** (Autenticación de servicio de Azure) y busque un vínculo de **reautenticación** debajo de la cuenta seleccionada.  Selecciónelo para autenticar. 

### <a name="authenticating-with-azure-cli"></a>Autenticación con la CLI de Azure

Para utilizar la CLI de Azure para el desarrollo local:

1. Instale la [CLI de Azure v2.0.12](/cli/azure/install-azure-cli) o posterior. Actualice las versiones anteriores. 

2. Utilice **az login** para iniciar sesión en Azure.

Utilice `az account get-access-token` para comprobar el acceso.  Si recibe un error, compruebe que el paso 1 se haya completado correctamente. 

Si la CLI de Azure no está instalada en el directorio predeterminado, recibirá un error que le notificará que `AzureServiceTokenProvider` no encuentra la ruta de acceso para la CLI de Azure.  Use la variable de entorno **AzureCLIPath** para definir la carpeta de instalación de la CLI de Azure. `AzureServiceTokenProvider` agrega el directorio especificado en la variable de entorno **AzureCLIPath** a la variable de entorno **Path** cuando sea necesario.

Si ha iniciado la sesión en la CLI de Azure con varias cuentas o la cuenta tiene acceso a varias suscripciones, debe especificar la suscripción específica que se va a usar.  Para ello, utilice lo siguiente:

```
az account set --subscription <subscription-id>
```

Este comando genera un resultado solo en caso de error.  Para comprobar la configuración de la cuenta actual, use:

```
az account list
```

### <a name="authenticating-with-azure-ad-integrate-authentication"></a>Autenticación con Azure AD Integrated Authentication (Autenticación integrada de Azure AD)

Para usar la autenticación de Azure AD, compruebe lo siguiente:

- El directorio activo local se [sincroniza con Azure AD](/azure/active-directory/connect/active-directory-aadconnect).

- El código se ejecuta en una máquina unidad a un dominio.


### <a name="authenticating-to-custom-services"></a>Autenticación en los servicios personalizados

Cuando un servicio llama a los servicios de Azure, los pasos anteriores funcionan porque los servicios de Azure permiten el acceso a los usuarios y a las aplicaciones.  

Al crear un servicio que llama a un servicio personalizado, use las credenciales de cliente de Azure AD para la autenticación de desarrollo local.  Hay dos opciones: 

1.  Usar a una entidad de servicio para iniciar sesión en Azure:

    1.  [Cree una entidad de servicio](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Use la CLI de Azure para iniciar sesión:

        ```
        az login --service-principal -u <principal-id> --password <password>
           --tenant <tenant-id> --allow-no-subscriptions
        ```

        Puesto que la entidad de servicio puede no tener acceso a la suscripción, use el argumento `--allow-no-subscriptions`.

2.  Usar variables de entorno para especificar los detalles de la entidad de servicio.  Para más información, vea [Ejecución de la aplicación con una entidad de servicio](#running-the-application-using-a-service-principal).

Una vez que haya iniciado sesión en Azure, `AzureServiceTokenProvider` usa la entidad de servicio para recuperar un token para el desarrollo local.

Esto se aplica solo a desarrollo local. Cuando la solución se implementa en Azure, la biblioteca cambia a la autenticación de MSI.

<a name="msi"></a>
## <a name="running-the-application-using-a-managed-service-identity"></a>Ejecución de la aplicación con una identidad de servicio administrada 

Cuando se ejecuta el código en un Azure App Service o una máquina virtual de Azure con MSI habilitado, la biblioteca usa automáticamente Identidad de servicio administrada. No se requiere ningún cambio de código. 


<a name="sp"></a>
## <a name="running-the-application-using-a-service-principal"></a>Ejecución de la aplicación con una entidad de servicio 

Puede ser necesario crear una credencial de cliente de Azure AD para autenticar. Algunos ejemplos frecuentes son:

1. El código se ejecuta en un entorno de desarrollo local, pero no en la identidad del desarrollador.  Service Fabric, por ejemplo, usa la [cuenta de NetworkService](/azure/service-fabric/service-fabric-application-secret-management) para el desarrollo local.
 
2. El código se ejecuta en un entorno de desarrollo local y se autentica en un servicio personalizado, por lo que no puede usar la identidad de desarrollador. 
 
3. Se ejecuta el código en un recurso de procesos de Azure que aún no admite Identidad de servicio administrada, como Azure Batch.

Para usar un certificado para iniciar sesión en Azure AD:

1. Cree un [certificado de entidad de servicio](/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

2. Implemente el certificado en los almacenes _LocalMachine_ o _CurrentUser_. 

3. Establezca una variable de entorno denominada **AzureServicesAuthConnectionString** para:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={LocalMachine or CurrentUser}.
    ```
 
    Reemplace _{AppId}_, _{TenantId}_ y _{Thumbprint}_ por los valores generados en el paso 1.

    **CertificateStoreLocation** debe ser _CurrentUser_ o _LocalMachine_, según el plan de implementación.

4. Ejecute la aplicación. 

Para iniciar sesión con la credencial de secreto compartido de Azure AD:

1. Crear una [entidad de servicio con una contraseña](/azure/azure-resource-manager/resource-group-authenticate-service-principal) y concédale acceso a Key Vault. 

2. Establezca una variable de entorno denominada **AzureServicesAuthConnectionString** para:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}. 
    ```

    Reemplace _{AppId}_, _{TenantId}_ y _{ClientSecret}_ por los valores generados en el paso 1.

3. Ejecute la aplicación. 

Una vez que todo está configurado correctamente, no es necesario realizar más cambios de código.  `AzureServiceTokenProvider` usa la variable de entorno y el certificado para autenticar en Azure AD. 

<a name="connectionstrings"></a>
## <a name="connection-string-support"></a>Compatibilidad de la cadena de conexión

De forma predeterminada, `AzureServiceTokenProvider` utiliza varios métodos para recuperar un token. 

Para controlar el proceso, utilice una cadena de conexión pasada al constructor `AzureServiceTokenProvider` o especificada en la variable de entorno *AzureServicesAuthConnectionString*. 

Se admiten las siguientes opciones:

| Opción de&nbsp;cadena&nbsp;de conexión | Escenario | Comentarios|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Desarrollo local | AzureServiceTokenProvider utiliza AzureCli para obtener el token. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Desarrollo local | AzureServiceTokenProvider utiliza Visual Studio para obtener el token. |
| `RunAs=CurrentUser;` | Desarrollo local | AzureServiceTokenProvider utiliza Azure AD Integrated Authentication (Autenticación integrada de Azure AD) para obtener el token. |
| `RunAs=App;` | Identidad de servicio administrada | AzureServiceTokenProvider utiliza Identidad de servicio administrada para obtener el token. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint`<br>`   ={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`  | Entidad de servicio | `AzureServiceTokenProvider` usa el certificado para obtener el token desde Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};`<br>`   CertificateSubjectName={Subject};CertificateStoreLocation=`<br>`   {LocalMachine or CurrentUser}` | Entidad de servicio | `AzureServiceTokenProvider` usa el certificado para obtener el token desde Azure AD.|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Entidad de servicio |`AzureServiceTokenProvider` usa el secreto para obtener el token desde Azure AD. |


## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Identidad de servicio administrada](/azure/app-service/app-service-managed-service-identity).

- Obtenga información acerca de diferentes maneras de [autenticar y autorizar aplicaciones](/azure/app-service/app-service-authentication-overview).

- Más información sobre los [escenarios de autenticación](/azure/active-directory/develop/active-directory-authentication-scenarios#web-browser-to-web-application) de Azure AD.