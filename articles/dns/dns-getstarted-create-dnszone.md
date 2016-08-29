<properties
   pageTitle="Introducción a DNS de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo crear zonas DNS para DNS de Azure. Se trata de instrucciones paso a paso para crear la primera zona DNS para empezar a hospedar sus dominios de DNS con PowerShell."
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
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# Creación de una zona DNS mediante PowerShell

> [AZURE.SELECTOR]
- [Portal de Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [CLI de Azure](dns-getstarted-create-dnszone-cli.md)

En este artículo encontrará una guía de los pasos necesarios para crear una zona DNS usando CLI. También puede crear una zona DNS con PowerShell o el Portal de Azure.

[AZURE.INCLUDE [crear-zona-DNS-acerca de](../../includes/dns-create-zone-about-include.md)]

## <a name="tagetag"></a>Acerca de ETags y etiquetas

### <a name="etags"></a>Etags

Supongamos que dos personas o dos procesos tratan de modificar un registro DNS al mismo tiempo. ¿Cuál gana? ¿Y el ganador sabe que solo ha sobrescrito cambios creados por otra persona?

DNS de Azure usa Etag para administrar de forma segura los cambios simultáneos realizados al mismo recurso. Cada recurso DNS (zona o conjunto de registros) tiene un valor de Etag asociado a él. Siempre que se recupera un recurso, también se recupera su Etag. Al actualizar un recurso, tiene la opción de devolver el valor de Etag para que DNS de Azure pueda comprobar dicho valor en las coincidencias de servidor. Puesto que cada actualización a un recurso conlleva la regeneración de Etag, una incoherencia de Etag indica que se ha producido un cambio simultáneo. Los valores de Etag también se usan al crear un recurso para asegurarse de que el recurso no existe aún.

De forma predeterminada, PowerShell para DNS de Azure utiliza Etags para bloquear los cambios simultáneos a zonas y conjuntos de registros. El elemento opcional *-Overwrite* se puede usar para suprimir las comprobaciones de ETag, en cuyo caso, se sobrescribirán todos los cambios simultáneos que se hayan producido.

En el nivel de la API de REST de DNS de Azure, los valores de Etag se especifican mediante los encabezados HTTP. Su comportamiento se indica en la siguiente tabla:

|Encabezado|Comportamiento|
|------|--------|
|None|PUT always succeeds (no Etag checks)|
|If-match <etag>|PUT only succeeds if resource exists and Etag matches|
|If-match * | PUT only succeeds if resource exists|
|If-none-match * |	PUT only succeeds if resource does not exist|

### <a name="tags"></a>Etiquetas

Las etiquetas son diferentes de las Etag. Las etiquetas son una lista de pares nombre-valor que Azure Resource Manager usa para etiquetar los recursos con fines de facturación o agrupación. Para obtener más información sobre las etiquetas, consulte [Uso de etiquetas para organizar los recursos de Azure](../resource-group-using-tags.md).

PowerShell para DNS de Azure admite etiquetas en las zonas y en los conjuntos de registros especificados mediante el parámetro `-Tag` opcional.


## Antes de empezar

Antes de comenzar con la configuración, compruebe que dispone de los elementos siguientes.
	
- Una suscripción de Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o registrarse para obtener una [cuenta gratis](https://azure.microsoft.com/pricing/free-trial/).
	
- Necesitará instalar la versión más reciente de los cmdlets de PowerShell de Azure Resource Manager (1.0 o posterior). Consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) para obtener más información sobre cómo instalar los cmdlets de PowerShell.

## Paso 1: Inicio de sesión

Abre la consola de PowerShell y conéctate a tu cuenta. Para obtener más información, consulte [Uso de Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md).

Use el siguiente ejemplo para ayudarle a conectarse:

	Login-AzureRmAccount

Compruebe las suscripciones para la cuenta.

	Get-AzureRmSubscription 

Especifique la suscripción que desea usar.

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## Paso 2: Creación de un grupo de recursos

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Sin embargo, puesto que todos los recursos DNS son globales y no regionales, la elección de la ubicación del grupo de recursos no incide en DNS de Azure.

Puede omitir este paso si utiliza un grupo de recursos existente.

	New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"


## Paso 3: Registro

El proveedor de recursos Microsoft.Network administra el servicio DNS de Azure. La suscripción a Azure debe estar registrada para usar este proveedor de recursos antes de utilizar DNS de Azure. Se trata de una operación única para cada suscripción.

	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network


## Paso 4: Creación de una zona DNS

Una zona DNS se crea mediante el cmdlet `New-AzureRmDnsZone`. A continuación se muestran ejemplos para crear una zona DNS con o sin etiquetas. Para más información acerca de las etiquetas, consulte la sección sobre [etiquetas](#tags) que aparece en este artículo.

>[AZURE.NOTE] En DNS de Azure, deben especificarse nombres de zona sin un punto de terminación **“.”**. Por ejemplo, "**contoso.com**" en lugar de "**contoso.com.**".

### Creación de una zona DNS

En el ejemplo siguiente, se crea una zona DNS llamada *contoso.com* en el grupo de recursos *MyResourceGroup*. Utilice el ejemplo para crear una zona DNS, sustituyendo los valores por los suyos.

	New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

### Para crear una zona DNS con etiquetas.

En el ejemplo siguiente se muestra cómo crear una zona DNS con dos etiquetas, *project = demo* y *env = test*. Utilice el ejemplo para crear una zona DNS, sustituyendo los valores por los suyos.

	New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )

## Visualización de los registros

Al crear una zona DNS, también se crean los siguientes registros DNS:

- El registro *Inicio de autoridad* (SOA). Se encuentra en la raíz de cada zona DNS.

- Los registros de servidor de nombres (NS) autoritativo. Estos muestran qué servidores de nombres hospedan la zona. DNS de Azure usa un grupo de servidores de nombres, por lo que se pueden asignar diferentes servidores de nombres a zonas distintas en DNS de Azure. Consulte [Delegación de un dominio a DNS de Azure](dns-domain-delegation.md) para más información.

Para ver estos registros, use `Get-AzureRmDnsRecordSet`:

	Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
	RecordType        : SOA
	Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
	Tags              : {}

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
	RecordType        : NS
	Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
	Tags              : {}


Los conjuntos de registros de la raíz (o la *cúspide*) de una zona DNS usan **@** como el nombre del conjunto de registros.


## Prueba

Puede probar su zona DNS con herramientas de DNS, como nslookup, dig o el [cmdlet de PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Si aún no ha delegado el dominio para usar la nueva zona DNS en Azure, necesitará dirigir la consulta DNS directamente a uno de los servidores de nombres de la zona. Los servidores de nombres de su zona se proporcionan en los registros de NS, tal y como se muestra con el comando `Get-AzureRmDnsRecordSet` mencionado anteriormente. Asegúrese de sustituir los valores correctos de su zona en el comando que aparece a continuación.

	nslookup
	> set type=SOA
	> server ns1-01.azure-dns.com
	> contoso.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	contoso.com
        	primary name server = ns1-01.azure-dns.com
        	responsible mail addr = msnhst.microsoft.com
        	serial  = 1
        	refresh = 900 (15 mins)
        	retry   = 300 (5 mins)
        	expire  = 604800 (7 days)
        	default TTL = 300 (5 mins)


## Pasos siguientes

Después de crear una zona DNS, cree [conjuntos de registros y registros](dns-getstarted-create-recordset.md) para iniciar la resolución de nombres para el dominio de Internet.

<!---HONumber=AcomDC_0817_2016-->