---
title: "Uso de Azure Key Vault desde una aplicación web | Microsoft Docs"
description: "Use este tutorial para ayudarle a aprender a usar Azure Key Vault desde una aplicación web."
services: key-vault
author: adhurwit
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 09/15/2017
ms.author: adhurwit
ms.openlocfilehash: 107be940b4c105056c63f793fb0111b03469bf66
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="use-azure-key-vault-from-a-web-application"></a>Uso de Azure Key Vault desde una aplicación web

## <a name="introduction"></a>Introducción

Use este tutorial como ayuda para aprender a usar Azure Key Vault desde una aplicación web. Le guiará por el proceso de obtener acceso a un secreto de un almacén de claves de Azure para que se pueda usar en su aplicación web.

**Tiempo estimado para completar el tutorial:** 15 minutos.

Para obtener información general sobre Azure Key Vault, consulte [¿Qué es Azure Key Vault?](key-vault-whatis.md)

## <a name="prerequisites"></a>Requisitos previos

Para realizar este tutorial, necesitará lo siguiente:

* Un URI de un secreto en un almacén de claves de Azure.
* Un Id. de cliente y un secreto de cliente para una aplicación web registrada en Azure Active Directory que tenga acceso a su almacén de claves.
* Una aplicación web. Vamos a mostrarle los pasos para una aplicación ASP.NET MVC implementada en Azure como una aplicación web.

>[!IMPORTANT]
>* Este ejemplo se basa en un método más antiguo del aprovisionamiento manual de identidades de AAD. Actualmente hay una nueva característica en la versión preliminar denominada [Identidad de servicio administrada (MSI)](https://docs.microsoft.com/azure/active-directory/msi-overview), que puede aprovisionar identidades de AAD automáticamente. Consulte el siguiente ejemplo en [GitHub](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) para obtener más información.

> [!NOTE]
>* Para los fines de este tutorial, es esencial que haya realizado los pasos enumerados en [Introducción a Azure Key Vault](key-vault-get-started.md) para así disponer del URI de un secreto y del id. de cliente y el secreto de cliente de una aplicación web.


La aplicación web a la que va a tener acceso el almacén de claves es la que está registrada en Azure Active Directory y a la que se ha concedido acceso al almacén de claves. Si este no es el caso, vuelva al apartado sobre cómo registrar una aplicación del tutorial de introducción y repita los pasos enumerados.

Este tutorial está diseñado para desarrolladores web que comprendan los conceptos básicos de la creación de aplicaciones web en Azure. Para obtener más información sobre Azure Web Apps, consulte [Información general de Web Apps](../app-service/app-service-web-overview.md).

## <a id="packages"></a>Incorporación de paquetes NuGet

Hay dos paquetes que la aplicación web debe tener instalados.

* Biblioteca de autenticación de Active Directory: contiene métodos para interactuar con Azure Active Directory y administrar la identidad de usuario.
* Biblioteca de Almacén claves de Azure:  contiene métodos para interactuar con Azure Key Vault.

Los dos paquetes se pueden instalar mediante la Consola del Administrador de paquetes con el comando Install-Package.

```
// this is currently the latest stable version of ADAL
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>Modificación de Web.Config

Hay tres configuraciones de aplicaciones que deben agregarse al archivo web.config como se indica a continuación.

```
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
```

Si no va a hospedar la aplicación como una aplicación web de Azure, debe agregar los valores reales de ClientId, Secreto de cliente y URI de secreto al web.config. De lo contrario, deje estos valores ficticios porque iremos agregando los valores reales en Azure Portal para lograr un mayor nivel de seguridad.

## <a id="gettoken"></a>Agregar el método para obtener un token de acceso

Para usar la API de Key Vault necesita un token de acceso. El cliente de Key Vault controla las llamadas a la API de Key Vault, pero deberá suministrarle una función que obtenga el token de acceso.  

A continuación se muestra el código para obtener un token de acceso de Azure Active Directory. Este código puede estar en cualquier parte de la aplicación. Quiero agregar una clase Utils o EncryptionHelper.  

```cs
//add these using statements
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Threading.Tasks;
using System.Web.Configuration;

//this is an optional property to hold the secret after it is retrieved
public static string EncryptSecret { get; set; }

//the method that will be provided to the KeyVaultClient
public static async Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                WebConfigurationManager.AppSettings["ClientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

> [!NOTE]
>* Actualmente, la nueva característica Managed Service Identity (MSI) es la manera más fácil de autenticar. Para más detalles, consulte el vínculo siguiente sobre el ejemplo de uso de [Key Vault con MSI en una aplicación de .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) y el [tutorial de MSI con App Service y Functions](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity). 
>* Otra forma de autenticar una aplicación de Azure AD es mediante un secreto de cliente y un identificador de cliente. Y su uso en una aplicación web permite la separación de deberes y proporciona mayor control sobre la administración de claves. Pero se basa en colocar el secreto de cliente en las opciones de configuración, lo que, para algunos, puede ser tan arriesgado como poner el secreto que se desea proteger en las opciones de configuración. A continuación encontrará información sobre el uso de un identificador de cliente y un certificado en lugar de un identificador de cliente y un secreto de cliente para autenticar la aplicación de Azure AD.

## <a id="appstart"></a>Recuperación del secreto en Inicio de aplicación

Ahora tenemos el código para llamar a la API de Key Vault y recuperar el secreto. El siguiente código se puede colocar en cualquier parte siempre que se llame antes de que sea necesario usarlo. He colocado este código en el evento Inicio de aplicación en Global.asax para que se ejecute una vez en el inicio y permita que esté disponible el secreto para la aplicación.

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general  application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Agregar la configuración de la aplicación en Azure Portal (opcional)

Si tiene una instancia de Azure Web App, ahora puede agregar los valores reales para AppSettings en Azure Portal. De esta manera, los valores reales no estarán en web.config sino que estarán protegidos a través del Portal donde cuenta con capacidades de control de acceso independientes. Estos valores se sustituirán por los valores que escribió en web.config. Asegúrese de que los nombres sean los mismos.

![Configuración de la aplicación mostrada en Azure Portal][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Autenticación con un certificado, en lugar de con un secreto de cliente

Otra forma de autenticar una aplicación de Azure AD es mediante el uso de un Id. de cliente y un certificado, en lugar de un Id. de cliente y un secreto de cliente. Estos son los pasos necesarios para usar un certificado en una aplicación web de Azure:

1. Obtener o crear un certificado
2. Asociar el certificado a una aplicación de Azure AD
3. Agregar código a la aplicación web para que use el certificado
4. Agregar un certificado a la aplicación web

### <a name="get-or-create-a-certificate"></a>Obtener o crear un certificado

Para nuestros propósito, crearemos un certificado de prueba. A continuación encontrará un par de comandos que se pueden usar en un símbolo del sistema para desarrolladores para crear un certificado. Cambie al directorio a aquel en el que desea que se creen los archivos de certificado.  Además, para las fechas de inicio y fin del certificado, utilice la fecha actual más 1 año.

```
makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2017 -e 07/31/2018 -r
pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123
```

Tome nota de la fecha de finalización y la contraseña del archivo .pfx (en este ejemplo: 31/07/2017 y test123). Las necesitará más adelante.

Para más información sobre cómo crear un certificado de prueba, vea [Procedimientos para crear su propio certificado de prueba](https://msdn.microsoft.com/library/ff699202.aspx)

### <a name="associate-the-certificate-with-an-azure-ad-application"></a>Asociar el certificado a una aplicación de Azure AD

Ahora que tiene un certificado, tendrá que asociarlo a una aplicación de Azure AD. Actualmente, Azure Portal no admite este flujo de trabajo; esto puede realizarse mediante PowerShell. Ejecute los siguientes comandos para asociar el certificado con la aplicación de Azure AD:

```ps
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets all -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

Después de ejecutar estos comandos, podrá ver la aplicación en Azure AD. Al buscar, asegúrese de que seleccionar "Aplicaciones que tiene mi compañía" en lugar de "Aplicaciones que usa mi compañía" en el cuadro de diálogo de búsqueda.

Para obtener más información acerca de los objetos Application y los objetos ServicePrincipal de Azure AD, consulte [Objetos Application y objetos ServicePrincipal](../active-directory/active-directory-application-objects.md)

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>Agregar código a la aplicación web para que use el certificado

Ahora agregaremos código a Web App para tener acceso al certificado y usarlo para la autenticación.

En primer lugar, este es el código para tener acceso al certificado.

```cs
public static class CertificateHelper
{
    public static X509Certificate2 FindCertificateByThumbprint(string findValue)
    {
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
        try
        {
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                findValue, false); // Don't validate certs, since the test root isn't installed.
            if (col == null || col.Count == 0)
                return null;
            return col[0];
        }
        finally
        {
            store.Close();
        }
    }
}
```

Observe que StoreLocation es CurrentUser, en lugar de LocalMachine. Fíjese también en que usamos 'false' en el método Find, ya que usamos un certificado de prueba.

El siguiente código usa CertificateHelper y crea ClientAssertionCertificate, que es necesario para la autenticación.

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

Este es el nuevo código para obtener el token de acceso. Esto reemplaza al método GetToken en el ejemplo anterior. Para su comodidad, le hemos dado un nombre diferente.

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```

Para facilitar su uso, hemos colocado todo este código en la clase Utils del proyecto de aplicación web.

El último cambio de código se da en el método Application_Start. En primer lugar, es preciso llamar al método GetCert() para cargar ClientAssertionCertificate. Y, a continuación, se cambia el método de devolución de llamada que se suministra al crear un nuevo KeyVaultClient. Tenga en cuenta que esto reemplaza al código que teníamos en el ejemplo anterior.

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>Adición de un certificado a Web App a través de Azure Portal

La adición de un certificado a Web App es un sencillo proceso de dos pasos. En primer lugar, vaya a Azure Portal y navegue a Web App. En la hoja Configuración de la aplicación web, haga clic en la entrada de "Dominios personalizados y SSL". En la hoja que se abre podrá cargar el certificado que creó en el ejemplo anterior, KVWebApp.pfx; asegúrese de que recuerda la contraseña del archivo pfx.

![Adición de un certificado a Web App en Azure Portal][2]

Lo último que debe hacer es agregar una configuración de aplicación a la aplicación web cuyo nombre es WEBSITE\_LOAD\_CERTIFICATES y cuyo valor es *. Esto garantizará que se cargan todos los certificados. Si desea cargar solo los certificados que ha cargado, puede escribir una lista separada por comas de sus huellas digitales.

Para obtener más información sobre cómo agregar un certificado a una aplicación Web, consulte [Uso de certificados en las aplicaciones de Azure Websites](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)

### <a name="add-a-certificate-to-key-vault-as-a-secret"></a>Adición de un certificado a Key Vault como secreto

En lugar de cargar el certificado directamente al servicio Web App, puede almacenarlo en Key Vault como un secreto e implementarlo desde allí. Este es un proceso de dos pasos que se describe en la siguiente entrada de blog [Deploying Azure Web App Certificate through Key Vault](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)

## <a id="next"></a>Pasos siguientes

Para conocer las referencias de programación, consulte [Referencia de la API de cliente de C# de Azure Key Vault](https://msdn.microsoft.com/library/azure/dn903628.aspx).

<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
