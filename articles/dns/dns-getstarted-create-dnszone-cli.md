<properties
   pageTitle="Creación de una zona DNS con CLI| Microsoft Azure"
   description="Aprenda a crear zonas DNS para DNS de Azure paso a paso para empezar a hospedar el dominio DNS con la CLI."
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# Creación de una zona DNS de Azure con CLI


> [AZURE.SELECTOR]
- [Portal de Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [CLI de Azure](dns-getstarted-create-dnszone-cli.md)


En este artículo encontrará una guía de los pasos necesarios para crear una zona DNS usando CLI. También puede crear una zona DNS con PowerShell o el Portal de Azure.

[AZURE.INCLUDE [crear-zona-DNS-acerca de](../../includes/dns-create-zone-about-include.md)]


## Antes de empezar

En estas instrucciones se usa la CLI de Microsoft Azure. Asegúrese de actualizar a la CLI de Azure más reciente (0.9.8 o posterior) para utilizar los comandos de DNS de Azure. Escriba `azure -v` para comprobar qué versión de la CLI de Azure está instalada en su equipo.

## Paso 1: Configuración de la CLI de Azure

### 1\. Instalación de la CLI de Azure

Puede instalar la CLI de Azure para Windows, Linux o MAC. Para poder administrar DNS de Azure con la CLI de Azure, es necesario completar estos pasos. Puede consultar mas información disponible en [Instalación de la CLI de Azure](../xplat-cli-install.md). Los comandos DNS requieren la CLI de Azure versión 0.9.8 o superior.

Puede encontrar todos los comandos para el proveedor de la red en CLI con el comando siguiente:

	Azure network

### 2\. Cambio del modo de CLI

La DNS de Azure usa el Administrador de recursos de Azure. Asegúrese de cambiar al modo de CLI para usar los comandos ARM.

	Azure config mode arm

### 3\. Inicio de sesión en la cuenta de Azure.

Se le pedirá que se autentique con sus credenciales. Tenga en cuenta que solo puede usar cuentas de ORGID.

    Azure login -u "username"

### 4\. Selección de la suscripción

Elección de la suscripción de Azure que se va a usar.

    Azure account set "subscription name"

### 5\. Crear un grupo de recursos

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Sin embargo, puesto que todos los recursos DNS son globales y no regionales, la elección de la ubicación del grupo de recursos no incide en DNS de Azure.

Puede omitir este paso si utiliza un grupo de recursos existente.

    Azure group create -n myresourcegroup --location "West US"


### 6\. Registro

El proveedor de recursos Microsoft.Network administra el servicio DNS de Azure. La suscripción a Azure debe estar registrada para usar este proveedor de recursos antes de utilizar DNS de Azure. Se trata de una operación única para cada suscripción.

	Azure provider register --namespace Microsoft.Network


## Paso 2: Creación de una zona DNS

Una zona DNS se crea con el comando `azure network dns zone create`. Opcionalmente, puede crear una zona DNS junto con etiquetas. Las etiquetas son una lista de pares nombre-valor que Azure Resource Manager usa para etiquetar los recursos con fines de facturación o agrupación. Para obtener más información sobre las etiquetas, consulte [Uso de etiquetas para organizar los recursos de Azure](../resource-group-using-tags.md).

En DNS de Azure, deben especificarse nombres de zona sin la terminación **“.”**. Por ejemplo, "**contoso.com**" en lugar de "**contoso.com.**".


### Creación de una zona DNS

En el ejemplo siguiente, se crea una zona DNS denominada *contoso.com* en el grupo de recursos denominado *MyResourceGroup*.

Utilice el ejemplo para crear la zona DNS, sustituyendo los valores por los suyos.

    Azure network dns zone create myresourcegroup contoso.com

### Creación de una zona DNS y etiquetas.

La CLI de DNS de Azure es compatible con las etiquetas de las zonas de DNS especificadas usando el parámetro opcional *-Tag*. En el ejemplo siguiente se muestra cómo crear una zona DNS con dos etiquetas, project = demo y env = test.

Utilice el ejemplo a continuación para crear una zona DNS y etiquetas, sustituyendo los valores que aquí aparecen por los suyos.

	Azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"

## Visualización de los registros

Al crear una zona DNS, también se crean los siguientes registros DNS:

- El registro “Inicio de autoridad” (SOA). Se encuentra en la raíz de cada zona DNS.

- Los registros de servidor de nombres (NS) autoritativo. Estos muestran qué servidores de nombres hospedan la zona. DNS de Azure usa un grupo de servidores de nombres, por lo que se pueden asignar diferentes servidores de nombres a zonas distintas en DNS de Azure. Consulte [Delegación de un dominio a DNS de Azure](dns-domain-delegation.md) para obtener más información.

Para ver estos registros, use `azure network dns-record-set show`.<BR> *Uso: network dns record-set show <grupo de recursos><nombre de zona dns><nombre><tipo>*


En el ejemplo siguiente, al ejecutar el comando con el grupo de recursos *myresourcegroup*, el nombre del conjunto de registros *"@"* (para un registro raíz) y el tipo *SOA* se produce el siguiente resultado:


	azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/SOA
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    SOA record:
	data:      Email                         : msnhst.microsoft.com
	data:      Expire time                   : 604800
	data:      Host                          : edge1.azuredns-cloud.net
	data:      Minimum TTL                   : 300
	data:      Refresh time                  : 900
	data:      Retry time                    : 300
	data:                                    :
<BR> Para ver los registros NS creados con la zona, use el comando siguiente:

	azure network dns record-set show myresourcegroup "contoso.com" "@" NS
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/NS
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    NS records
	data:        Name server domain name     : ns1-05.azure-dns.com
	data:        Name server domain name     : ns2-05.azure-dns.net
	data:        Name server domain name     : ns3-05.azure-dns.org
	data:        Name server domain name     : ns4-05.azure-dns.info
	data:
	info:    network dns-record-set show command OK

>[AZURE.NOTE] Los conjuntos de registros de la raíz (o la *cúspide*) de una zona DNS usan **@** como el nombre del conjunto de registros.

## Prueba

Puede probar su zona DNS con herramientas DNS, como nslookup, DIG o el cmdlet de PowerShell `Resolve-DnsName`.

Si aún no ha delegado el dominio para usar la nueva zona DNS en Azure, deberá dirigir la consulta de DNS directamente a uno de los servidores de nombres de la zona. Los servidores de nombres de su zona se proporcionan en los registros de NS, como se muestra con el comando "azure network dns-record-set show" mencionado anteriormente. Asegúrese de sustituir los valores correctos de su zona en el comando que aparece a continuación.

En el ejemplo siguiente se usa DIG para consultar el dominio contoso.com con los servidores de nombres asignados para la zona DNS. La consulta tiene que señalar a un servidor de nombres para el que se usó *@<servidor de nombres para la zona>* y el nombre de zona con DIG.

	 <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
	(1 server found)
	global options: +cmd
 	Got answer:
	->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
 	flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
 	WARNING: recursion requested but not available

 	OPT PSEUDOSECTION:
 	EDNS: version: 0, flags:; udp: 4000
  	QUESTION SECTION:
	contoso.com.                        IN      A

 	AUTHORITY SECTION:
	contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
	msnhst.microsoft.com. 6 900 300 604800 300

	Query time: 93 msec
	SERVER: 208.76.47.5#53(208.76.47.5)
	WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
	MSG SIZE  rcvd: 120

## Pasos siguientes

Después de crear una zona DNS, cree [conjuntos de registros y registros](dns-getstarted-create-recordset-cli.md) para iniciar la resolución de nombres para el dominio de Internet.

<!---HONumber=AcomDC_0817_2016-->