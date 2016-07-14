<properties 
   pageTitle="Creación de zonas DNS y conjuntos de registros de DNS de Azure con el SDK de .NET | Microsoft Azure" 
   description="Creación de conjuntos de registros y zonas DNS con el SDK de .NET" 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/10/2016"
   ms.author="cherylmc"/>


# Creación de conjuntos de registros y zonas DNS con el SDK de .NET

Puede automatizar las operaciones para crear, eliminar o actualizar zonas, conjuntos de registros y registros DNS mediante el SDK de DNS con la biblioteca de administración de DNS de .NET. Un proyecto completo de Visual Studio se encuentra disponible [aquí](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip).

## Paquetes NuGet y declaraciones de espacio de nombres

Para poder utilizar el cliente DNS, tendrá que instalar el paquete NuGet **Biblioteca de administración de DNS de Azure** y agregar los espacios de nombres de administración de DNS al proyecto.
 
1. En **Visual Studio**, abra un proyecto o cree uno nuevo. 

2. Vaya a **Herramientas** **>** **Administrador de paquetes Nuget** **>** **Consola del administrador de paquetes**.

3. Descargue la biblioteca de administración de DNS de Azure.

		using Microsoft.Azure;
		using Microsoft.Azure.Management.Dns;
		using Microsoft.Azure.Management.Dns.Models;

## Inicialización del cliente de administración de DNS

El *DnsManagementClient* contiene los métodos y las propiedades necesarios para administrar los conjuntos de registros y las zonas DNS. Para que el cliente pueda acceder a su suscripción, debe configurar los permisos correctos y generar un token AWT. Para más información, consulte [Authenticating Azure Resource Manager requests](https://msdn.microsoft.com/library/azure/dn790557.aspx) \(Autenticación de solicitudes de Azure Manager Resource).

	// get a token for the AAD application (see the article link above for code)
	string jwt = GetAToken();

	// make the TokenCloudCredentials using subscription ID and token
	TokenCloudCredentials tcCreds = new TokenCloudCredentials(subID, jwt);

	// make the DNS management client
	DnsManagementClient dnsClient = new DnsManagementClient(tcCreds);

## Creación o actualización de una zona DNS

Para crear una zona DNS, se crea un objeto de zona y se pasa a *dnsClient.Zones.CreateOrUpdate*. Como las zonas DNS no están vinculadas a una región específica, la ubicación se establece en "global".<BR> DNS de Azure admite la simultaneidad optimista denominada [Etags](dns-getstarted-create-dnszone.md). El valor "Etag" es una propiedad de la zona. "IfNoneMatch" es una propiedad de ZoneCreateOrUpdateParameters.

	// To create a DNS zone
	Zone z = new Zone("global");
	z.Properties = new ZoneProperties();
	z.Tags.Add("dept", "shopping");
	z.Tags.Add("env", "production");
	ZoneCreateOrUpdateParameters zoneParams = new ZoneCreateOrUpdateParameters(z);
	ZoneCreateOrUpdateResponse responseCreateZone = 
	dnsClient.Zones.CreateOrUpdate("myresgroup", "myzone.com", zoneParams);



## Creación o actualización de conjuntos de registros y registros de DNS

Los registros DNS se administran como un conjunto de registros. Un conjunto de registros es un conjunto de registros con el mismo nombre y tipo de registro dentro de una zona. Para crear o actualizar un conjunto de registros, se crea un objeto "RecordSet" y se pasa a *dnsClient.RecordSets.CreateOrUpdate*. Tenga en cuenta que el nombre del conjunto de registros es relativo al nombre de la zona en lugar de ser el nombre DNS completo. La ubicación se establece en "global".<BR> DNS de Azure admite la simultaneidad optimista [Etags](dns-getstarted-create-dnszone.md). El valor "Etag" es una propiedad de RecordSet. "IfNoneMatch" es una propiedad de RecordSetCreateOrUpdateParameters.
    


	// To create record sets
	RecordSet rsWwwA = new RecordSet("global");
	rsWwwA.Properties = new RecordProperties(3600);
	rsWwwA.Properties.ARecords = new List<ARecord>();
	rsWwwA.Properties.ARecords.Add(new ARecord("1.2.3.4"));
	rsWwwA.Properties.ARecords.Add(new ARecord("1.2.3.5"));
	RecordCreateOrUpdateParameters recordParams = 
								new RecordCreateOrUpdateParameters(rsWwwA);
	RecordCreateOrUpdateResponse responseCreateA = 
								dnsClient.RecordSets.CreateOrUpdate("myresgroup", 
	"myzone.com", "www", RecordType.A, recordParams);
	
    
## Obtención de zonas y conjuntos de registros

Las colecciones de *zonas* y *conjuntos de registros* ofrecen la posibilidad de obtener zonas y conjuntos de registros, respectivamente Los conjuntos de registros se identifican por su tipo y su nombre y por la zona y el grupo de recursos en que se encuentran. Las zonas se identifican por su nombre y el grupo de recursos en que se encuentran.

	ZoneGetResponse getZoneResponse = 
	dnsClient.Zones.Get("myresgroup", "myzone.com");
	RecordGetResponse getRSResp = 
	dnsClient.RecordSets.Get("myresgroup", 
	"myzone.com", "www", RecordType.A);

## Enumeración de zonas y conjuntos de registros

Para mostrar las zonas, utilice el método *List* en la colección de zonas. Para mostrar los conjuntos de registros, utilice los métodos *List* o *ListAll* en la colección de conjuntos de registros. El método *List* difiere del método *ListAll* en que solo devuelve los conjuntos de registros del tipo especificado.

En el ejemplo siguiente se ilustra cómo obtener una lista de zonas y conjuntos de registros DNS.


	ZoneListResponse zoneListResponse = dnsClient.Zones.List("myresgroup", new ZoneListParameters());
	foreach (Zone zone in zoneListResponse.Zones)
	{
    	RecordListResponse recordSets = 
                 			dnsClient.RecordSets.ListAll("myresgroup", "myzone.com", new RecordSetListParameters());

    // do something like write out each record set
	}


## Pasos siguientes

[Proyecto de ejemplo del SDK de Visual Studio](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)

<!----HONumber=AcomDC_0518_2016-->
