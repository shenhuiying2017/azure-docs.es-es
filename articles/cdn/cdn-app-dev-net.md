<properties
	pageTitle="Introducción a la biblioteca de red CDN de Azure para .NET | Microsoft Azure"
	description="Aprenda a escribir aplicaciones .NET para administrar la red CDN de Azure con Visual Studio."
	services="cdn"
	documentationCenter=".net"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="casoper"/>

# Introducción a la biblioteca de red CDN de Azure para .NET

Puede usar la [biblioteca de red CDN de Azure para .NET](https://msdn.microsoft.com/library/mt657769.aspx) para automatizar la creación y la administración de perfiles y puntos de conexión de CDN. Este tutorial lo guiará por la creación de una aplicación de consola .NET sencilla que muestra algunas de las operaciones disponibles. Con este tutorial no se pretende describir todos los aspectos de la biblioteca de red CDN de Azure para .NET en detalle.

Necesitará Visual Studio 2015 para completar este tutorial. [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) está disponible gratis para su descarga.

Encontrará un ejemplo completado de este tutorial [aquí](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

## Preparación

Para poder escribir código de administración de CDN, es necesario llevar a cabo algunos preparativos. En primer lugar, vamos a crear un grupo de recursos que contendrá el perfil de CDN que crearemos en este tutorial. A continuación, configuraremos Azure Active Directory para que proporcione autenticación para la aplicación. Una vez hecho esto, aplicaremos permisos al grupo de recursos para que solo los usuarios autorizados de nuestro inquilino de Azure AD puedan interactuar con el perfil de CDN.

### Creación del grupo de recursos

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Haga clic en el botón **Nuevo** de la parte superior izquierda, en **Administración** y en **Grupo de recursos**.
	
	![Crear un grupo de recursos](./media/cdn-app-dev-net/cdn-new-rg-1.png)

3. Llame a su grupo de recursos *CdnConsoleTutorial*. Seleccione la suscripción y elija una ubicación cercana. Si lo desea, puede hacer clic en la casilla **Anclar al panel** para anclar el grupo de recursos al panel en el portal. Así resultará más fácil encontrarlo. Cuando termine las selecciones, haga clic en **Crear**.

	![Asignar un nombre al grupo de recursos](./media/cdn-app-dev-net/cdn-new-rg-2.png)

4. Una vez creado el grupo de recursos, si no lo ancló al panel, para buscarlo, haga clic en **Examinar** y **Grupos de recursos**. Haga clic en el grupo de recursos para abrirlo. Tome nota del valor en **Id. de suscripción**. lo necesitará más adelante.

	 ![Asignar un nombre al grupo de recursos](./media/cdn-app-dev-net/cdn-subscription-id.png)

### Creación de la aplicación de Azure AD

Existen dos enfoques para la autenticación de la aplicación con Azure Active Directory: usuarios individuales o una entidad de servicio. Una entidad de servicio se parece a una cuenta de servicio en Windows. En lugar de conceder a usuarios concretos permisos para interactuar con los perfiles de CDN, concedemos los permisos a la entidad de servicio. Las entidades de servicio se suelen utilizar para procesos automatizados no interactivos. Aunque en este tutorial escribimos una aplicación de consola interactiva, nos centraremos en el enfoque de entidad de servicio.

Para crear una entidad de servicio, se siguen varios pasos, incluida la creación de una aplicación de Azure Active Directory. Para ello, vamos a [completar este tutorial](../resource-group-create-service-principal-portal.md).

> [AZURE.IMPORTANT] Asegúrese de seguir los pasos descritos en el [tutorial vinculado](../resource-group-create-service-principal-portal.md). Es *muy importante* que lo complete exactamente como se describe. Asegúrese de tomar nota de su **identificador de inquilino**, el **nombre de dominio del inquilino** (por lo general, un dominio *.onmicrosoft.com*, a menos que haya especificado uno personalizado), el **id. de cliente** y la **clave de autenticación de cliente**, ya que los necesitaremos más adelante. Tenga cuidado de proteger su **id. de cliente** y **clave de autenticación de cliente**, ya que cualquiera puede usar estas credenciales para ejecutar operaciones como la entidad de servicio.
> 	
> Cuando llegue al paso denominado [Configuración de aplicación multiinquilino](../resource-group-create-service-principal-portal.md#configure-multi-tenant-application), seleccione **No**.
> 
> Cuando llegue al paso [Asignación de aplicación a un rol](../resource-group-create-service-principal-portal.md#assign-application-to-role), use el grupo de recursos que creamos antes, *CdnConsoleTutorial*, pero en lugar del rol **Lector**, asigne el rol **CDN Profile Contributor** (Colaborador de perfil de CDN). Después de asignar la aplicación al rol **CDN Profile Contributor** (Colaborador de perfil de CDN) en el grupo de recursos, vuelva a este tutorial.

Una vez creada la entidad de servicio y asignado el rol **CDN Profile Contributor** (Colaborador de perfil de CDN), la hoja **Usuarios** del grupo de recursos debería parecerse a la siguiente.

![Hoja Usuarios](./media/cdn-app-dev-net/cdn-service-principal.png)


### Autenticación interactiva de usuarios

Si, en lugar de una entidad de servicio, prefiere la autenticación interactiva de usuario individual, el proceso se parece mucho al usado con una entidad de servicio. De hecho, debe seguir el mismo procedimiento, con algunos cambios menores.

>[AZURE.IMPORTANT] Siga estos pasos únicamente si opta por usar la autenticación de usuario individual en lugar de una entidad de servicio.

1. Al crear la aplicación, en lugar de **Aplicación web**, elija **Aplicación de cliente nativo**. 
	
	![Aplicación nativa](./media/cdn-app-dev-net/cdn-native-application.png)
	
2. En la página siguiente, se le pedirá un **URI de redirección**. No se validará el URI, pero debe recordarlo. Lo necesitará más adelante.

3. No hay necesidad de crear una **clave de autenticación de cliente**.

4. En lugar de asignar una entidad de servicio al rol **CDN Profile Contributor** (Colaborador de perfil de CDN), vamos a asignar usuarios individuales o grupos. En este ejemplo, puede ver que hemos asignado *Usuario de demostración de CDN* al rol **CDN Profile Contributor** (Colaborador de perfil de CDN).
	
	![Acceso de usuario individual](./media/cdn-app-dev-net/cdn-aad-user.png)


## Creación del proyecto e incorporación de paquetes NuGet

Ahora que hemos creado un grupo de recursos para los perfiles de CDN y concedido permiso a la aplicación de Azure AD para administrar perfiles y puntos de conexión de CDN dentro de ese grupo, podemos comenzar a crear la aplicación.

En Visual Studio 2015, haga clic en **Archivo**, **Nuevo**, **Proyecto** para abrir el cuadro de diálogo Nuevo proyecto. Expanda **Visual C#** y seleccione **Windows** en el panel de la izquierda. Haga clic en **Aplicación de consola** en el panel central. Asigne un nombre al proyecto y haga clic en **Aceptar**.

![Nuevo proyecto](./media/cdn-app-dev-net/cdn-new-project.png)

El proyecto va a usar algunas bibliotecas de Azure contenidas en paquetes NuGet. Vamos a agregarlos al proyecto.

1. Haga clic en el menú **Herramientas**, **Administrador de paquetes NuGet** y **Consola del Administrador de paquetes**.

	![Administrar paquetes NuGet](./media/cdn-app-dev-net/cdn-manage-nuget.png)

2. En la Consola del Administrador de paquetes, ejecute el siguiente comando para instalar la **biblioteca de autenticación de Active Directory (ADAL)**:

	`Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`

3. Ejecute lo siguiente para instalar la **biblioteca de administración de CDN de Azure**:

	`Install-Package Microsoft.Azure.Management.Cdn`

## Directivas, constantes, método Main y métodos auxiliares

Escribamos la estructura básica del programa.

1. En la pestaña Program.cs, reemplace las directivas `using` del principio por lo siguiente:

	```
	using System;
	using System.Collections.Generic;
	using Microsoft.Azure.Management.Cdn;
	using Microsoft.Azure.Management.Cdn.Models;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Rest;
	```

2. Necesitamos definir algunas constantes que los métodos van a usar. En la clase `Program`, pero antes del método `Main`, agregue lo siguiente. Asegúrese de reemplazar los marcadores de posición, incluidos los **&lt;corchetes angulares&gt;**, por sus propios valores según sea necesario.

	```
	//Tenant app constants
	private const string clientID = "<YOUR CLIENT ID>";
	private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
	private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";

	//Application constants
	private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
	private const string profileName = "CdnConsoleApp";
	private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
	private const string resourceGroupName = "CdnConsoleTutorial";
	private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
	```

3. También en el nivel de clase, defina estas dos variables. Las usaremos más adelante para determinar si el perfil y el punto de conexión ya existen.

	```
	static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
	```

4.  Reemplace el método `Main` de la siguiente forma:

	```
	static void Main(string[] args)
	{
		//Get a token
		AuthenticationResult authResult = GetAccessToken();

		// Create CDN client
		CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
			{ SubscriptionId = subscriptionId };

		ListProfilesAndEndpoints(cdn);

		// Create CDN Profile
		CreateCdnProfile(cdn);

		// Create CDN Endpoint
		CreateCdnEndpoint(cdn);
		
		Console.WriteLine();

		// Purge CDN Endpoint
		PromptPurgeCdnEndpoint(cdn);

		// Delete CDN Endpoint
		PromptDeleteCdnEndpoint(cdn);

		// Delete CDN Profile
		PromptDeleteCdnProfile(cdn);

		Console.WriteLine("Press Enter to end program.");
		Console.ReadLine();
	}
	```

5. Algunos de los otros métodos van a plantear al usuario preguntas que necesitan "Sí" o "No" como respuesta. Agregue el método siguiente para hacerlo algo más fácil:

	```
	private static bool PromptUser(string Question)
	{
		Console.Write(Question + " (Y/N): ");
		var response = Console.ReadKey();
		Console.WriteLine();
		if (response.Key == ConsoleKey.Y)
		{
			return true;
		}
		else if (response.Key == ConsoleKey.N)
		{
			return false;
		}
		else
		{
			// They're not pressing Y or N.  Let's ask them again.
			return PromptUser(Question);
		}
	}
	```

Ahora que ya hemos escrito la estructura básica del programa, deberíamos crear los métodos a los que llama el método `Main`.

## Autenticación

Para poder usar la biblioteca de administración de CDN de Azure, es necesario autenticar la entidad de servicio y obtener un token de autenticación. Este método usa ADAL para recuperar el token.

```
private static AuthenticationResult GetAccessToken()
{
	AuthenticationContext authContext = new AuthenticationContext(authority); 
	ClientCredential credential = new ClientCredential(clientID, clientSecret);
	AuthenticationResult authResult = 
		authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

	return authResult;
}
```

Si utiliza la autenticación de usuario individual, el método `GetAccessToken` tendrá un aspecto algo distinto.

>[AZURE.IMPORTANT] Use este ejemplo de código únicamente si opta por la autenticación de usuario individual en lugar de una entidad de servicio.

```
private static AuthenticationResult GetAccessToken()
{
	AuthenticationContext authContext = new AuthenticationContext(authority);
	AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
		clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

	return authResult;
}
```

Asegúrese de reemplazar `<redirect URI>` por el URI de redirección que especificó al registrar la aplicación en Azure AD.

## Lista de perfiles y puntos de conexión de CDN

Ahora estamos preparados para realizar operaciones de CDN. Lo primero que hace el método es proporcionar una lista de todos los perfiles y puntos de conexión en el grupo de recursos y, si encuentra una coincidencia para los nombres de perfil y punto de conexión especificados en nuestras constantes, lo tiene en cuenta más adelante para no crear duplicados.

```
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
	// List all the CDN profiles in this resource group
	var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
	foreach (Profile p in profileList)
	{
		Console.WriteLine("CDN profile {0}", p.Name);
		if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
		{
			// Hey, that's the name of the CDN profile we want to create!
			profileAlreadyExists = true;
		}

		//List all the CDN endpoints on this CDN profile
		Console.WriteLine("Endpoints:");
		var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
		foreach (Endpoint e in endpointList)
		{
			Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
			if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
			{
				// The unique endpoint name already exists.
				endpointAlreadyExists = true;
			}
		}
		Console.WriteLine();
	}
}
```

## Creación de perfiles y puntos de conexión de CDN

A continuación, vamos a crear un perfil.

```
private static void CreateCdnProfile(CdnManagementClient cdn)
{
	if (profileAlreadyExists)
	{
		Console.WriteLine("Profile {0} already exists.", profileName);
	}
	else
	{
		Console.WriteLine("Creating profile {0}.", profileName);
		ProfileCreateParameters profileParms =
			new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
		cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
	}
}
```

Una vez creado el perfil, crearemos un punto de conexión.

```
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
	if (endpointAlreadyExists)
	{
		Console.WriteLine("Profile {0} already exists.", profileName);
	}
	else
	{
		Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
		EndpointCreateParameters endpointParms =
			new EndpointCreateParameters()
			{
				Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
				IsHttpAllowed = true,
				IsHttpsAllowed = true,
				Location = resourceLocation
			};
		cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
	}
}
```

>[AZURE.NOTE] El ejemplo anterior asigna al punto de conexión un origen denominado *Contoso* con el nombre de host `www.contoso.com`. Debe cambiarlo para que señale al nombre de host de su propio origen.

## Purga de un punto de conexión

Suponiendo que se haya creado el punto de conexión, una tarea habitual que podríamos llevar a cabo en el programa es purgar el contenido del punto de conexión.

```
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
	if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
	{
		Console.WriteLine("Purging endpoint. Please wait...");
		cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
		Console.WriteLine("Done.");
		Console.WriteLine();
	}
}
```

>[AZURE.NOTE] En el ejemplo anterior, la cadena `/*` denota que deseo purgar todo el contenido en la raíz de la ruta de acceso del punto de conexión. Esto equivale a activar **Purgar todo** en el cuadro de diálogo de purga del Portal de Azure. En el método `CreateCdnProfile`, he creado el perfil como perfil de **CDN de Azure de Verizon** con el código `Sku = new Sku(SkuName.StandardVerizon)`, por lo que esto saldrá bien. Sin embargo, los perfiles de **CDN de Azure de Akamai** no admiten **Purgar todo**, así que, si estuviera usando un perfil de Akamai para este tutorial, tendría que incluir las rutas de acceso específicas que purgar.

## Eliminación de perfiles y puntos de conexión de CDN

Los últimos métodos que incluiremos eliminarán el punto de conexión y el perfil.

```
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
	if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
	{
		Console.WriteLine("Deleting endpoint. Please wait...");
		cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
		Console.WriteLine("Done.");
		Console.WriteLine();
	}
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
	if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
	{
		Console.WriteLine("Deleting profile. Please wait...");
		cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
		Console.WriteLine("Done.");
		Console.WriteLine();
	}
}
```

## Ejecución del programa

Ahora podemos compilar y ejecutar el programa haciendo clic en el botón **Iniciar** en Visual Studio.

![Programa en ejecución](./media/cdn-app-dev-net/cdn-program-running-1.png)

Cuando el programa llega el mensaje anterior, debería poder ver, de vuelta en el grupo de recursos en el Portal de Azure, que se ha creado el perfil.

![¡Éxito!](./media/cdn-app-dev-net/cdn-success.png)

Después, se pueden confirmar los mensajes para ejecutar el resto del programa.

![Finalización del programa](./media/cdn-app-dev-net/cdn-program-running-2.png)

## Pasos siguientes

Para ver el proyecto completado en este tutorial, [descargue el ejemplo](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Para encontrar más documentación sobre la biblioteca de administración de CDN de Azure para .NET, consulte la [referencia en MSDN](https://msdn.microsoft.com/library/mt657769.aspx).

<!---HONumber=AcomDC_0615_2016-->