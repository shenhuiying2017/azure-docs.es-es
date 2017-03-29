---
title: "Uso de Azure Active Directory para la autenticación desde Azure Batch | Microsoft Docs"
description: "Batch admite Azure AD para la autenticación desde el servicio y el proveedor de recursos de Batch."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 03/16/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 28665625cde9c7b164986f7b2d9c845136543aba
ms.lasthandoff: 03/18/2017

---

# <a name="authenticate-from-batch-solutions-with-active-directory"></a>Autenticación desde soluciones de Batch con Active Directory

Azure Batch admite la autenticación con [Azure Active Directory][aad_about] (Azure AD) para el servicio Batch y el servicio de administración de este. Azure Active Directory es el directorio basado en la nube multiinquilino y el servicio de administración de identidades de Microsoft. El propio Azure usa Azure Active Directory (AAD) para la autenticación de sus clientes, administradores de servicios y usuarios de la organización.

En este artículo, se describe el uso de Azure AD para autenticar desde aplicaciones que utilizan la biblioteca Batch Management .NET o la biblioteca Batch .NET. En el contexto de las API de .NET de Batch, se muestra cómo usar Azure AD para autenticar a un administrador o coadministrador de la suscripción mediante la autenticación integrada. El usuario autenticado puede emitir solicitudes a Azure Batch.

También es posible usar Azure AD para autenticar el acceso a una aplicación que se ejecuta en modo desatendido. Aquí nos centraremos en el uso de la autenticación integrada de Azure AD y mencionaremos otros recursos a los que puede acudir para obtener información sobre la autenticación de aplicaciones desatendidas.

## <a name="use-azure-ad-with-batch-management-solutions"></a>Uso de Azure AD con soluciones de administración de Batch

La biblioteca Batch Management .NET expone tipos para trabajar con cuentas, claves de cuenta, aplicaciones y paquetes de aplicaciones de Batch. La biblioteca Batch Management .NET es un cliente de proveedor de recursos de Azure y se utiliza junto con [Azure Resource Manager][resman_overview] para administrar estos recursos mediante programación. 

Se requiere Azure AD para autenticar las solicitudes realizadas a través de cualquier cliente de proveedor de recursos de Azure, incluida la biblioteca Batch Management .NET y a través de [Azure Resource Manager][resman_overview].

En esta sección, se utiliza el proyecto de ejemplo [AccountManagement][acct_mgmt_sample], disponible en GitHub, para guiarle en el uso de Azure AD con la biblioteca Batch Management .NET. El ejemplo de AccountManagement es una aplicación de consola que permite acceder mediante programación a una suscripción, crear un grupo de recursos y una nueva cuenta de Batch y realizar ciertas operaciones en la cuenta. 

Para más información sobre el uso de la biblioteca Batch Management .NET y el ejemplo de AccountManagement, consulte [Administración de cuentas y cuotas de Batch con la biblioteca cliente de administración de Batch para .NET](batch-management-dotnet.md).

### <a name="register-your-application-with-azure-ad"></a>Registro de la aplicación con Azure AD

La [biblioteca de autenticación de Azure Active Directory][aad_adal] (ADAL) proporciona una interfaz de programación para Azure AD para usarla en sus aplicaciones. Para llamar a ADAL desde la aplicación, debe registrar la aplicación en un inquilino de Azure AD. Al registrar la aplicación, se facilita a Azure AD información acerca de esta, incluido un nombre para ella en el inquilino de Azure AD. Azure AD proporciona un identificador de aplicación que se utiliza para asociar la aplicación con Azure AD en tiempo de ejecución. Para conocer más detalles acerca del identificador de la aplicación, consulte [Objetos de aplicación y de entidad de servicio de Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).

Siga los pasos que aparecen en la sección [Incorporación de una aplicación](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) en [Integración de aplicaciones con Azure Active Directory][aad_integrate] para registrar la aplicación de ejemplo AccountManagement. Especifique **Aplicación de cliente nativo** como tipo de aplicación. Para el **URI de redirección**, puede especificar cualquier URI válido (como `http://myaccountmanagementsample`), ya que no necesita ser un punto de conexión real:

![](./media/batch-aad-auth/app-registration-management-plane.png)

Una vez completado el proceso de registro, verá que aparecen el identificador de la aplicación y el identificador de objeto (entidad de servicio) para la aplicación.  

![](./media/batch-aad-auth/app-registration-client-id.png)

### <a name="update-your-code-to-reference-your-application-id"></a>Actualización del código para que haga referencia al identificador de la aplicación 

La aplicación cliente usa el identificador de aplicación (conocido también como identificador de cliente) para acceder a Azure AD en tiempo de ejecución. Una vez que haya registrado la aplicación en Azure Portal, actualice el código para usar el identificador de aplicación proporcionado por Azure AD para la aplicación registrada. En la aplicación de ejemplo AccountManagement, copie el identificador de la aplicación desde Azure Portal a la constante adecuada:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
Copie también el URI de redirección especificado durante el proceso de registro.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

### <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Concesión a la API de Azure Resource Manager de acceso a la aplicación

A continuación, debe delegar el acceso a la aplicación a la API de Azure Resource Manager. El identificador de Azure AD para la API de Resource Manager es **Windows Azure Service Management API**.

Siga estos pasos en Azure Portal:

1. En el panel de navegación izquierdo de Azure Portal, elija **Más servicios**, haga clic en **Registros de aplicaciones**y, luego, en **Agregar**.
2. Busque el nombre de la aplicación en la lista de registros de aplicación:

    ![Buscar el nombre de la aplicación](./media/batch-aad-auth/search-app-registration.png)

3. Se mostrará la hoja **Configuración**. En la sección **Acceso de API**, seleccione **Permisos necesarios**.
4. Haga clic en **Agregar** para agregar un nuevo permiso necesario. 
5. En el paso 1, escriba **Windows Azure Service Management API**, seleccione esa API en la lista de resultados y haga clic en el botón **Seleccionar**.
6. En el paso 2, active la casilla de verificación situada junto a **Access Azure classic deployment model as organization users** (Acceder al modelo de implementación clásica como usuarios de la organización) y haga clic en el botón **Seleccionar**.
7. Haga clic en el botón **Listo**.

La hoja **Permisos necesarios** ya muestra que se han concedido permisos a la aplicación para las API de ADAL y de Resource Manager. La primera vez que registra la aplicación con Azure AD, se conceden permisos a ADAL de forma predeterminada.

![Delegar permisos para la API de Azure Resource Manager](./media/batch-aad-auth/required-permissions-management-plane.png)


### <a name="acquire-an-azure-ad-authentication-token"></a>Adquisición de un token de autenticación de Azure AD

La aplicación de ejemplo de AccountManagement define las constantes que proporcionan el punto de conexión para Azure AD y para Azure Resource Manager. La aplicación de ejemplo usa estas constantes para consultar Azure AD para obtener información de suscripción. No cambie estas constantes:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure service management resource 
private const string ResourceUri = "https://management.core.windows.net/";
```

Después de registrar el ejemplo de AccountManagement en el inquilino de Azure AD y proporcionar los valores necesarios en el código fuente de ejemplo, el ejemplo estará listo para autenticarse con Azure AD. Cuando ejecute el ejemplo, ADAL intentará adquirir un token de autenticación. En este paso, le pedirá las credenciales de Microsoft: 

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

Después de proporcionar sus credenciales, la aplicación de ejemplo emitirá las solicitudes autenticadas para el servicio de administración de Batch. 

## <a name="use-azure-ad-with-batch-service-solutions"></a>Uso de Azure AD con las soluciones de servicio de Batch

La biblioteca .NET de Batch proporciona tipos para la creación de flujos de trabajo de procesamiento en paralelo con el servicio Batch. El servicio Batch admite la autenticación con [clave compartida](https://docs.microsoft.com/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service) y la autenticación a través de Azure AD. En esta sección se describe la autenticación a través de Azure AD.

>[!NOTE]
>Cuando se crea una cuenta de Batch, puede especificar si los grupos se deben asignar en una suscripción administrada por Batch o en una suscripción de usuario. Si la cuenta asigna grupos en una suscripción de usuario, deberá usar Azure AD para autenticar las solicitudes a los recursos de esa cuenta.
>
>

La autenticación de aplicaciones .NET de Batch a través de Azure AD es similar a la autenticación de aplicaciones .NET de administración de Batch. Hay algunas diferencias que se describen en esta sección.

### <a name="batch-service-endpoints"></a>Puntos de conexión de servicio de Batch

Los puntos de conexión de servicio de Batch son diferentes a los que se utilizan con .NET de administración de Batch.

El punto de conexión de Azure AD para el servicio Batch es:

`https://login.microsoftonline.com/common`

El punto de conexión del recurso para el servicio Batch es:

`https://batch.core.windows.net/`

### <a name="grant-the-batch-service-api-access-to-your-application"></a>Concesión a la API del servicio Batch de acceso a la aplicación

Antes de poder autenticar mediante Azure AD desde la aplicación de Batch debe registrar la aplicación con Azure AD y conceder acceso a la API del servicio Batch. El identificador de Azure AD para la API del servicio Batch es **Microsoft Azure Batch (MicrosoftAzureBatch)**.

1. Siga los pasos que aparecen en la sección [Incorporación de una aplicación](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) en [Integración de aplicaciones con Azure Active Directory][aad_integrate] para registrar la aplicación de Batch. Como **URI de redireccionamiento**, puede especificar cualquier URI válido. No es necesario que sea un punto de conexión real.

    Una vez que ha registrado su aplicación, verá el identificador de la aplicación y el identificador de objeto:

    ![Registro de la aplicación de Batch con Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

2. A continuación, se mostrará la hoja **Configuración**. En la sección **Acceso de API**, seleccione **Permisos necesarios**.
3. En la hoja **Permisos necesarios**, haga clic en el botón **Agregar**.
4. En el paso 1, busque **MicrosoftAzureBatch**, seleccione **Microsoft Azure Batch (MicrosoftAzureBatch)** y haga clic en el botón **Seleccionar**.
5. En el paso 2, active la casilla de verificación situada junto a **Acceder al servicio Azure Batch** y haga clic en el botón **Seleccionar**.
6. Haga clic en el botón **Listo**.

La hoja **Permisos necesarios** ya muestra que la aplicación Azure AD concede acceso a las API de Azure AD y Azure Batch. 

![Permisos de API](./media/batch-aad-auth/required-permissions-data-plane.png)

### <a name="authentication-for-batch-accounts-in-a-user-subscription"></a>Autenticación para cuentas de Batch de una suscripción de usuario

Cuando se crea una nueva cuenta de Batch, puede elegir la suscripción en la que se asignan los grupos. Su elección afecta a cómo se autentican las solicitudes realizadas a los recursos de esa cuenta

De forma predeterminada, los grupos de Batch se asignan en una suscripción del servicio Batch. Si elige esta opción, puede autenticar las solicitudes a los recursos de esa cuenta con la clave compartida o con Azure AD.

También puede especificar que se asignen los grupos de Batch en una suscripción de usuario específica. Si elige esta opción, debe autenticarse con Azure AD.

### <a name="best-practices-for-using-azure-ad-with-batch"></a>Procedimientos recomendados para el uso de Azure AD con Batch

Un token de autenticación de Azure AD expira después de una hora. Cuando se usa un objeto **BatchClient** de larga duración, se recomienda que recupere un token de ADAL en cada solicitud para asegurarse de que siempre tiene un token válido. 

Para lograr esto en. NET, escriba un método que recupere el token de Azure AD y pase ese método a un objeto **BatchTokenCredentials** como un delegado. El método delegado se llama en cada solicitud al servicio Batch para asegurarse de que se proporciona un token válido. De forma predeterminada ADAL almacena en caché los tokens, por lo que solo se recuperará un nuevo token de Azure AD si es necesario. Para obtener un ejemplo, consulte [Ejemplo de código: Uso de Azure AD con .NET de Batch](#code-example-using-azure-ad-with-batch-net) en la sección siguiente. Para más información, consulte [Escenarios de autenticación para Azure AD][aad_auth_scenarios].

### <a name="code-example-using-azure-ad-with-batch-net"></a>Ejemplo de código: Uso de Azure AD con .NET de Batch

Para escribir código .NET de Batch que permita autenticarse con Azure AD, consulte el paquete de [.NET de Azure Batch](https://www.nuget.org/packages/Azure.Batch/) y el paquete de [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

Incluya las siguientes instrucciones `using` en el código:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Haga referencia al punto de conexión común de Azure AD y al punto de conexión de Azure AD para el servicio Batch en el código:  

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/common";
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Haga referencia al punto de conexión de la cuenta de Batch:

```csharp
private const string BatchAccountEndpoint = "https://myaccount.westcentralus.batch.azure.com";
```

Especifique el identificador (Id. de cliente) de su aplicación. El identificador de la aplicación está disponible en el registro de la aplicación en Azure Portal. Consulte la sección titulada [Concesión a la API del servicio Batch de acceso a la aplicación](#grant-the-batch-service-api-access-to-your-application) para recuperarlo. 

```csharp
private const string ClientId = "<application-id>";
```

Especifique también un URI de redirección que puede ser cualquier identificador URI válido.

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Escriba un método de devolución de llamada para adquirir el token de autenticación de Azure AD. El método **AcquireTokenAsync** pide al usuario sus credenciales y las utiliza para adquirir un nuevo token.

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Construya un objeto **BatchTokenCredentials** que tome el delegado como parámetro. Use esas credenciales para abrir un objeto **BatchClient**. A continuación, puede usar ese objeto **BatchClient** para las operaciones posteriores del servicio Batch.

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountEndpoint, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

El método de devolución de llamada **GetAuthenticationTokenAsync** que se menciona anteriormente usa Azure AD para la autenticación integrada de un usuario que está interactuando con la aplicación. La llamada al método **AcquireTokenAsync** solicita al usuario sus credenciales y la aplicación se inicia una vez que el usuario las proporciona. También puede usar Azure AD para autenticar una aplicación desatendida mediante el uso de una entidad de servicio de Azure AD. Para más información, consulte [Objetos de aplicación y de entidad de servicio de Azure Active Directory](../active-directory/develop/active-directory-application-objects.md) y [Uso del portal para crear una aplicación de Active Directory y una entidad de servicio con acceso a los recursos](../resource-group-create-service-principal-portal.md).  
 

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la ejecución de la [aplicación de ejemplo AccountManagement][acct_mgmt_sample], consulte [Administración de cuentas y cuotas de Batch con la biblioteca cliente de administración de Batch para .NET](batch-management-dotnet.md).

Para más información acerca de Azure AD, consulte la [Documentación de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Puede consultar los ejemplos detallados sobre el uso de ADAL que están disponibles en la biblioteca de [ejemplos de código de Azure](https://azure.microsoft.com/resources/samples/?service=active-directory).


[aad_about]: ../active-directory/active-directory-whatis.md "¿Qué es Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Escenarios de autenticación para Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integración de aplicaciones con Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: http://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

