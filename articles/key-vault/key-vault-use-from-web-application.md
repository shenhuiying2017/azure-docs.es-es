<properties 
	pageTitle="Usar el Almacén de claves de Azure desde una aplicación web | Introducción" 
	description="Use este tutorial como ayuda para aprender a usar el Almacén de claves de Azure desde una aplicación web." 
	services="key-vault" 
	documentationCenter="" 
	authors="adamhurwitz" 
	manager=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="key-vault" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="adhurwit"/>

# Uso del Almacén de claves de Azure desde una aplicación web #

## Introducción  
Use este tutorial como ayuda para aprender a usar el Almacén de claves de Azure desde una aplicación web. Le guiará por el proceso de obtener acceso a un secreto de un almacén de claves de Azure para que se pueda usar en su aplicación web.

**Tiempo estimado para completar el tutorial:** 15 minutos.


Para obtener información general sobre el Almacén de claves de Azure, consulte [¿Qué es el Almacén de clave de Azure?](key-vault-whatis.md)

## Requisitos previos 

Para realizar este tutorial, necesitará lo siguiente:

- Un URI de un secreto en un almacén de claves de Azure.
- Un id. de cliente y un secreto de cliente para una aplicación web registrada con Azure Active Directory que tenga acceso a su almacén de claves.
- Una aplicación web. Vamos a mostrarle los pasos para una aplicación ASP.NET MVC implementada en Azure como una aplicación web. 

> [AZURE.NOTE]Para los fines de este tutorial, es esencial que haya realizado los pasos enumerados en [Introducción al Almacén de claves de Azure](key-vault-get-started.md) para así disponer del URI de un secreto y del id. de cliente y el secreto de cliente de una aplicación web.

La aplicación web que va a tener acceso el Almacén de claves es la que está registrada en Azure Active Directory y a la que se le ha concedido para él. Si este no es el caso, vuelva al apartado sobre cómo registrar una aplicación del tutorial de introducción y repita los pasos enumerados.

Este tutorial está diseñado para desarrolladores web que comprendan los conceptos básicos de la creación de aplicaciones web en Azure. Para obtener más información sobre las aplicaciones web de Azure, consulte [Información general de aplicaciones web](../app-service-web-overview.md).



## <a id="packages"></a>Incorporación de paquetes NuGet ##
Hay tres paquetes que la aplicación web debe tener instalados.

- Biblioteca de autenticación de Active Directory: contiene métodos para interactuar con Azure Active Directory y administrar la identidad de usuario.
- Biblioteca de Almacén claves de Azure: contiene métodos para interactuar con el Almacén de claves de Azure.


Los tres paquetes se pueden instalar mediante la Consola del Administrador de paquetes con el comando Install-Package.

	// this is currently the latest stable version of ADAL
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	//this is a preview version of the Key Vault Library
	Install-Package Microsoft.Azure.KeyVault -Pre


## <a id="webconfig"></a>Modificación de Web.Config ##
Hay tres configuraciones de aplicaciones que deben agregarse al archivo web.config como se indica a continuación.

	<!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

	<!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


Si no va a hospedar la aplicación como una aplicación web de Azure, debe agregar los valores reales de ClientId, Secreto de cliente y URI de secreto al web.config. De lo contrario, deje estos valores ficticios porque iremos agregando los valores reales en el Portal de Azure para lograr un nivel de seguridad adicional.


## <a id="gettoken"></a>Agregar el método para obtener un token de acceso ##
Para usar la API del Almacén de claves necesita un token de acceso. El cliente del Almacén de claves controla las llamadas a la API del Almacén de claves, pero deberá suministrarle una función que obtenga el token de acceso.

A continuación se muestra el código para obtener un token de acceso de Azure Active Directory. Este código puede estar en cualquier parte de la aplicación. Quiero agregar una clase Utils o EncryptionHelper.

	//add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System.Web.Configuration;
	
	//this is an optional property to hold the secret after it is retrieved
	public static string EncryptSecret { get; set; }

	//the method that will be provided to the KeyVaultClient
	public async static Task<string> GetToken(string authority, string resource, string scope)
    {
	    var authContext = new AuthenticationContext(authority);
	    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
	    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);
	    
	    if (result == null)
	    	throw new InvalidOperationException("Failed to obtain the JWT token");
	    
	    return result.AccessToken;
    }

## <a id="appstart"></a>Recuperación del secreto en Inicio de aplicación ##
Ahora tenemos el código para llamar a la API de Almacén de claves y recuperar el secreto. El siguiente código se puede colocar en cualquier parte siempre que se llame antes de que sea necesario usarlo. He colocado este código en el evento Inicio de aplicación en Global.asax para que se ejecute una vez en el inicio y permita que esté disponible el secreto para la aplicación.

	//add these using statements
	using Microsoft.Azure.KeyVault;
	using System.Web.Configuration;

	// I put my GetToken method in a Utils class. Change for wherever you placed your method. 
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

	var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;
	
	//I put a variable in a Utils class to hold the secret for general  application use. 
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>Agregar la configuración de la aplicación en el Portal de Azure (opcional) ##
Si tiene una aplicación web de Azure ahora puede agregar los valores reales para AppSettings en el Portal de Azure. De esta manera, los valores reales no estarán en web.config sino que estarán protegidos a través del Portal donde cuenta con capacidades de control de acceso independientes. Estos valores se sustituirán por los valores que escribió en web.config. Asegúrese de que los nombres sean los mismos.

![Configuración de la aplicación mostrada en el Portal de Azure][1]



## <a id="next"></a>Pasos siguientes ##


Para conocer las referencias de programación, consulte [Referencia de la API de cliente de C# del Almacén de claves](https://msdn.microsoft.com/library/azure/dn903628.aspx).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
 

<!---HONumber=July15_HO2-->