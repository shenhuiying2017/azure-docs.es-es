<properties
	pageTitle="Conexión de la aplicación a la red virtual con PowerShell"
	description="Instrucciones sobre cómo conectar y trabajar con redes virtuales mediante PowerShell"
	services="app-service"
	documentationCenter=""
	authors="ccompy"
	manager="wpickett"
	editor="cephalin"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/29/2016"
	ms.author="ccompy"/>

# Conexión de la aplicación a la red virtual con PowerShell #

## Información general ##

En el Servicio de aplicaciones de Azure, puede conectar su aplicación (web, móvil o API) a una red virtual de Azure (VNet) de su suscripción. Esta característica se denomina Integración con red virtual. No debe confundirse con la característica Entorno del Servicio de aplicaciones, que le permite ejecutar una instancia del Servicio de aplicaciones de Azure en su red virtual.

La característica Integración con red virtual tiene una interfaz de usuario (IU) en el nuevo portal que puede usar para integrar con redes virtuales implementadas con el modelo de implementación clásico o el modelo de implementación de Azure Resource Manager. Si desea conocer más sobre esta característica, consulte [Integración de su aplicación con una red virtual de Azure](web-sites-integrate-with-vnet.md).

Este artículo no trata sobre el uso de la interfaz de usuario, sino sobre cómo habilitar la integración con PowerShell. Puesto que los comandos para cada modelo de implementación son diferentes, este artículo tiene una sección para cada modelo de implementación.

Antes de continuar con este artículo, asegúrese de que:

- Tiene instalado el último SDK de Azure PowerShell. Puede instalarlo mediante el Instalador de plataforma web.
- Una aplicación que se ejecute en el Servicio de aplicaciones de Azure en un SKU Standard o Premium.

## Redes virtuales clásicas ##

Esta sección explica tres tareas para las redes virtuales que utilizan el modelo de implementación clásica:

1. Conecte la aplicación a una red virtual ya existente que tenga una puerta de enlace y esté configurada para conectividad de punto a sitio.
1. Actualice la información de integración con red virtual para la aplicación.
1. Desconecte la aplicación de la red virtual.

### Colección de una aplicación a una red virtual clásica ###

Para conectar una aplicación a una red virtual, siga estos tres pasos:

1. Declare para la aplicación web que se unirá a una red virtual concreta. La aplicación generará un certificado que se otorgará a la red virtual para la conectividad de punto a sitio.
1. Cargue el certificado de la aplicación web en la red virtual y recupere el URI de paquete de VPN de punto a sitio.
1. Actualice la conexión de red virtual de las aplicaciones web con el URI del paquete de punto a sitio.

Los pasos 1 y 3 admiten el uso de scripts, pero el paso 2 necesita una acción manual puntual en el portal o acceder al punto de conexión de Azure Resource Manager de la red virtual para realizar acciones **PUT** o **PATCH**. Póngase en contacto con el soporte técnico de Azure para habilitar esta opción. Antes de comenzar, asegúrese de que tiene una red virtual clásica con una conectividad de punto a sitio habilitada y una puerta de enlace implementada. Para crear la puerta de enlace y habilitar la conectividad de punto a sitio, debe usar el portal como se describe en [Creación de una puerta de enlace de VPN][createvpngateway].

La red virtual clásica debe estar en la misma suscripción que el plan del Servicio de aplicaciones que contiene la aplicación con la que va a realizar la integración.

##### Configuración del SDK de Azure PowerShell #####

Abra una ventana de PowerShell y configure la cuenta y la suscripción de Azure mediante:

	Login-AzureRmAccount

Este comando abrirá un símbolo del sistema para obtener las credenciales de Azure. Después de iniciar sesión, utilice cualquiera de los siguientes comandos para seleccionar la suscripción que desea usar. Asegúrese de utilizar la suscripción en la que están la red virtual y el plan del Servicio de aplicaciones.

	Select-AzureRmSubscription –SubscriptionName [WebAppSubscriptionName]

o

	Select-AzureRmSubscription –SubscriptionId [WebAppSubscriptionId]

##### Variables usadas en este artículo #####

Para simplificar los comandos, establezca una variable **$Configuration** de PowerShell con la configuración específica.

Establezca una variable en PowerShell con los parámetros siguientes:

	$Configuration = @{}
	$Configuration.WebAppResourceGroup = "[Your web app resource group]"
	$Configuration.WebAppName = "[Your web app name]"
	$Configuration.VnetSubscriptionId = "[Your vnet subscription id]"
	$Configuration.VnetResourceGroup = "[Your vnet resource group]"
	$Configuration.VnetName = "[Your vnet name]"

La ubicación de la aplicación debe ser la ubicación sin espacios. Por ejemplo, oeste de EE. UU. es westus.

	$Configuration.WebAppLocation = "[Your web app Location]"

El elemento siguiente es donde se debe escribir el certificado. Debe ser una ruta con permiso de escritura en el equipo local. Asegúrese de incluir .cer al final.

	$Configuration.GeneratedCertificatePath = "[C:\Path\To\Certificate.cer]"

Para ver lo que ha establecido, escriba **$Configuration**.

	> $Configuration

	Name                           Value
	----                           -----
	GeneratedCertificatePath       C:\vnetcert.cer
	VnetSubscriptionId             efc239a4-88f9-2c5e-a9a1-3034c21ad496
	WebAppResourceGroup            vnetdemo-rg
	VnetResourceGroup              testase1-rg
	VnetName                       TestNetwork
	WebAppName                     vnetintdemoapp
	WebAppLocation                 centralus

En el resto de esta sección se supone que tiene una variable creada como se acaba de describir.

##### Declaración de la red virtual para la aplicación #####

Use el siguiente comando para indicar a la aplicación que va a utilizar esta red virtual concreta. Esto hará que la aplicación genere los certificados necesarios:

	$vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -PropertyObject @{"VnetResourceId" = "/subscriptions/$($Configuration.VnetSubscriptionId)/resourceGroups/$($Configuration.VnetResourceGroup)/providers/Microsoft.ClassicNetwork/virtualNetworks/$($Configuration.VnetName)"} -Location $Configuration.WebAppLocation -ApiVersion 2015-07-01

Si este comando se ejecuta correctamente, **$vnet** debe tener una variable **Properties**. La variable **Properties** debe contener una huella digital del certificado y los datos del certificado.

##### Carga del certificado de la aplicación web en la red virtual #####

Es necesario un paso manual puntual para cada combinación de red virtual y suscripción. Es decir, si va a conectar aplicaciones de la suscripción A a la red virtual A, solo debe hacer este paso una vez, independientemente del número de aplicaciones que configure. Si va a agregar una nueva aplicación a otra red virtual, deberá volver a hacerlo. El motivo es que se genera un conjunto de certificados en el nivel de suscripción en el Servicio de aplicaciones de Azure; el conjunto se genera una vez para cada red virtual a la que se conectarán las aplicaciones.

Si ha seguido estos pasos o si ha realizado la integración con la misma red virtual mediante el portal, los certificados ya se habrán establecido.

El primer paso consiste en generar el archivo .cer. El segundo paso es cargar el archivo .cer en la red virtual. Para generar el archivo .cer a partir de la llamada de API en el paso anterior, ejecute los siguientes comandos.

	$certBytes = [System.Convert]::FromBase64String($vnet.Properties.certBlob)
	[System.IO.File]::WriteAllBytes("$($Configuration.GeneratedCertificatePath)", $certBytes)

El certificado estará en la ubicación especificada con **$Configuration.GeneratedCertificatePath**.

Para cargar el certificado manualmente, use el [Portal de Azure][azureportal] y vaya a **Red virtual (clásica)** –> **Conexiones VPN** –> **Punto a sitio** –> **Administrar certificados**. Desde aquí, cargue el certificado.

##### Obtención del paquete de punto a sitio #####

El siguiente paso en la configuración de una conexión de red virtual en una aplicación web es obtener el paquete de punto a sitio y proporcionarlo a la aplicación web.

Guarde la plantilla siguiente en un archivo llamado GetNetworkPackageUri.json en algún lugar del equipo, por ejemplo, C:\\Azure\\Templates\\GetNetworkPackageUri.json.

	{
		"$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"certData": {
				"type": "string"
			},
			"certThumbprint": {
				"type": "string"
			},
			"networkName": {
				"type": "string"
			}
		},
		"variables": {
			"legacyVnetName": "[concat('Group ', resourceGroup().name, ' ', parameters('networkName'))]"
			},
			"resources": [
			],
		"outputs" : {
			"PackageUri" :
			{
			"value" : "[listPackage(resourceId('Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates', parameters('networkName'), 'primary', parameters('certThumbprint')), '2014-06-01').packageUri]", "type" : "string"
			}
		}
	}


Establezca los parámetros de entrada:

	$parameters = @{"certData" = $vnet.Properties.certBlob ;
	certThumbprint = $vnet.Properties.certThumbprint ;
	"networkName" = $Configuration.VnetName }

Llame al script:

	$output = New-AzureRmResourceGroupDeployment -Name unused -ResourceGroupName $Configuration.VnetResourceGroup -TemplateParameterObject $parameters -TemplateFile C:\PATH\TO\GetNetworkPackageUri.json


La variable **$output.Outputs.packageUri** contendrá ahora el URI del paquete que se asignará a la aplicación web.

##### Carga del paquete de punto a sitio en la aplicación #####

El paso final es proporcionar a la aplicación este paquete. Simplemente ejecute el comando siguiente:

	$vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)/primary" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-07-01 -PropertyObject @{"VnetName" = $Configuration.VnetName ; "VpnPackageUri" = $($output.Outputs.packageUri).Value } -Location $Configuration.WebAppLocation

Si un mensaje le pide que confirme que va a sobrescribir un recurso existente; asegúrese de permitirlo.

Cuando este comando se ejecuta correctamente, la aplicación debe estar conectada a la red virtual. Para confirmar que es correcto, vaya a la consola de la aplicación y escriba lo siguiente:

	SET WEBSITE_

Si hay una variable de entorno llamada WEBSITE\_VNETNAME con un valor que coincide con el nombre de la red virtual de destino, todas las configuraciones se habrán realizado correctamente.

### Actualización de la información de integración con red virtual clásica ###

Para actualizar o volver a sincronizar la información, repita los pasos que siguió cuando creó la integración por primera vez. Estos pasos son:

1. Definir la información de configuración.
1. Declarar la red virtual para la aplicación.
1. Obtener el paquete de punto a sitio.
1. Cargar el paquete de punto a sitio para la aplicación.

### Desconectar la aplicación de una red virtual clásica. ###

Para desconectar la aplicación, necesitará la información de configuración que se estableció durante la integración con red virtual. Con esa información, hay un comando necesario para desconectar la aplicación de la red virtual.

	$vnet = Remove-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-07-01

## Redes virtuales del Administrador de recursos ##

Las redes virtuales del Administrador de recursos tienen API de Azure Resource Manager, lo que simplifica algunos procesos en comparación con las redes virtuales clásicas. Hay un script que le ayudará a completar las siguientes tareas:

- Crear una red virtual del Administrador de recursos e integrar su aplicación con él.
- Crear una puerta de enlace, configurar la conectividad de sitio de punto en una red virtual preexistente del Administrador de recursos e integrar su aplicación con él.
- Integrar la aplicación con una red virtual preexistente del Administrador de recursos que tiene una puerta de enlace y conectividad de punto a sitio habilitadas.
- Desconecte la aplicación de la red virtual.

### Script de integración del Servicio de aplicaciones de red virtual del Administrador de recursos ###

Copie el siguiente script y guárdelo en un archivo. Si no desea utilizar el script, puede informarse sobre él para ver cómo configurar todo con una red virtual del Administrador de recursos.


    function ReadHostWithDefault($message, $default)
    {
    	$result = Read-Host "$message [$default]"
    	if($result -eq "")
	    {
			$result = $default
	    }
		    return $result
    	}

	function PromptCustom($title, $optionValues, $optionDescriptions)
	{
	    Write-Host $title
	    Write-Host
	    $a = @()
	    for($i = 0; $i -lt $optionValues.Length; $i++){
		    Write-Host "$($i+1))" $optionDescriptions[$i]
	    }
	    Write-Host

	    while($true)
	    {
		    Write-Host "Choose an option: "
		    $option = Read-Host
		    $option = $option -as [int]

		    if($option -ge 1 -and $option -le $optionValues.Length)
		    {
			    return $optionValues[$option-1]
		    }
	    }
    }

    function PromptYesNo($title, $message, $default = 0)
    {
	    $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", ""
	    $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", ""
	    $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)
	    $result = $host.ui.PromptForChoice($title, $message, $options, $default)
	    return $result
    }

    function CreateVnet($resourceGroupName, $vnetName, $vnetAddressSpace, $vnetGatewayAddressSpace, $location)
    {
	    Write-Host "Creating a new VNET"
	    $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
	    New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix $vnetAddressSpace -Subnet $gatewaySubnet
    }

    function CreateVnetGateway($resourceGroupName, $vnetName, $vnetIpName, $location, $vnetIpConfigName, $vnetGatewayName, $certificateData, $vnetPointToSiteAddressSpace)
    {
	    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName
	    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

	    Write-Host "Creating a public IP address for this VNET"
	    $pip = New-AzureRmPublicIpAddress -Name $vnetIpName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
	    $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $vnetIpConfigName -Subnet $subnet -PublicIpAddress $pip

	    Write-Host "Adding a root certificate to this VNET"
	    $root = New-AzureRmVpnClientRootCertificate -Name "AppServiceCertificate.cer" -PublicCertData $certificateData

	    Write-Host "Creating Azure VNET Gateway. This may take up to an hour."
	    New-AzureRmVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $resourceGroupName -Location $location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Basic -VpnClientAddressPool $vnetPointToSiteAddressSpace -VpnClientRootCertificates $root
    }

    function AddNewVnet($subscriptionId, $webAppResourceGroup, $webAppName)
    {
	    Write-Host "Adding a new Vnet"
	    Write-Host
	    $vnetName = Read-Host "Specify a name for this Virtual Network"

	    $vnetGatewayName="$($vnetName)-gateway"
	    $vnetIpName="$($vnetName)-ip"
	    $vnetIpConfigName="$($vnetName)-ipconf"

	    # Virtual Network settings
	    $vnetAddressSpace="10.0.0.0/8"
	    $vnetGatewayAddressSpace="10.5.0.0/16"
	    $vnetPointToSiteAddressSpace="172.16.0.0/16"

	    $changeRequested = 0
	    $resourceGroupName = $webAppResourceGroup

	    while($changeRequested -eq 0)
	    {
		    Write-Host
		    Write-Host "Currently, I will create a VNET with the following settings:"
		    Write-Host
		    Write-Host "Virtual Network Name: $vnetName"
		    Write-Host "Resource Group Name:  $resourceGroupName"
		    Write-Host "Gateway Name: $vnetGatewayName"
		    Write-Host "Vnet IP name: $vnetIpName"
		    Write-Host "Vnet IP config name:  $vnetIpConfigName"
		    Write-Host "Address Space:$vnetAddressSpace"
		    Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
		    Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
		    Write-Host
		    $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

		    if($changeRequested -eq 0)
		    {
			    $vnetName = ReadHostWithDefault "Virtual Network Name" $vnetName
			    $resourceGroupName = ReadHostWithDefault "Resource Group Name" $resourceGroupName
			    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
			    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
			    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
			    $vnetAddressSpace = ReadHostWithDefault "Vnet Address Space" $vnetAddressSpace
			    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
			    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
		    }
	    }

	    $ErrorActionPreference = "Stop";

	    # We create the virtual network and add it here. The way this works is:
	    # 1) Add the VNET association to the App. This allows the App to generate certificates, etc. for the VNET.
	    # 2) Create the VNET and VNET gateway, add the certificates, create the public IP, etc., required for the gateway
	    # 3) Get the VPN package from the gateway and pass it back to the App.

	    $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup
	    $location = $webApp.Location

	    Write-Host "Creating App association to VNET"
	    $propertiesObject = @{
	     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
	    }
	    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

	    CreateVnet $resourceGroupName $vnetName $vnetAddressSpace $vnetGatewayAddressSpace $location

	    CreateVnetGateway $resourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

	    Write-Host "Retrieving VPN Package and supplying to App"
	    $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName -VirtualNetworkGatewayName $vnetGatewayName -ProcessorArchitecture Amd64

	    # Put the VPN client configuration package onto the App
	    $PropertiesObject = @{
	    "vnetName" = $VirtualNetworkName; "vpnPackageUri" = $packageUri
	    }

	    New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

	    Write-Host "Finished!"
    }

    function AddExistingVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
		$ErrorActionPreference = "Stop";

		# At this point, the gateway should be able to be joined to an App, but may require some minor tweaking. We will declare to the App now to use this VNET
		Write-Host "Getting App information"
		$webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
		$location = $webApp.Location

		$webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
		$currentVnet = $webAppConfig.Properties.VnetName
		if($currentVnet -ne $null -and $currentVnet -ne "")
		{
			Write-Host "Currently connected to VNET $currentVnet"
		}

		# Display existing vnets
		$vnets = Get-AzureRmVirtualNetwork
		$vnetNames = @()
		foreach($vnet in $vnets)
		{
			$vnetNames += $vnet.Name
	    }

	    Write-Host
	    $vnet = PromptCustom "Select a VNET to integrate with" $vnets $vnetNames

	    # We need to check if this VNET is able to be joined to a App, based on following criteria
		    # If there is no gateway, we can create one.
		    # If there is a gateway:
			    # It must be of type Vpn
			    # It must be of VpnType RouteBased
			    # If it doesn't have the right certificate, we will need to add it.
			    # If it doesn't have a point-to-site range, we will need to add it.

	    $gatewaySubnet = $vnet.Subnets | Where-Object { $_.Name -eq "GatewaySubnet" }

	    if($gatewaySubnet -eq $null -or $gatewaySubnet.IpConfigurations -eq $null -or $gatewaySubnet.IpConfigurations.Count -eq 0)
	    {
			$ErrorActionPreference = "Continue";
		    # There is no gateway. We need to create one.
		    Write-Host "This Virtual Network has no gateway. I will need to create one."

		    $vnetName = $vnet.Name
		    $vnetGatewayName="$($vnetName)-gateway"
		    $vnetIpName="$($vnetName)-ip"
		    $vnetIpConfigName="$($vnetName)-ipconf"

		    # Virtual Network settings
		    $vnetAddressSpace="10.0.0.0/8"
		    $vnetGatewayAddressSpace="10.5.0.0/16"
		    $vnetPointToSiteAddressSpace="172.16.0.0/16"

		    $changeRequested = 0

		    Write-Host "Your VNET is in the address space $($vnet.AddressSpace.AddressPrefixes), with the following Subnets:"
		    foreach($subnet in $vnet.Subnets)
		    {
			    Write-Host "$($subnet.Name): $($subnet.AddressPrefix)"
		    }

		    $vnetGatewayAddressSpace = Read-Host "Please choose a GatewaySubnet address space"

		    while($changeRequested -eq 0)
		    {
			    Write-Host
			    Write-Host "Currently, I will create a VNET gateway with the following settings:"
			    Write-Host
			    Write-Host "Virtual Network Name: $vnetName"
			    Write-Host "Resource Group Name:  $($vnet.ResourceGroupName)"
			    Write-Host "Gateway Name: $vnetGatewayName"
			    Write-Host "Vnet IP name: $vnetIpName"
			    Write-Host "Vnet IP config name:  $vnetIpConfigName"
			    Write-Host "Address Space:$($vnet.AddressSpace.AddressPrefixes)"
			    Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
			    Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
			    Write-Host
			    $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

			    if($changeRequested -eq 0)
			    {
				    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
				    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
				    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
				    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
				    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
			    }
		    }

		    $ErrorActionPreference = "Stop";

		    Write-Host "Creating App association to VNET"
		    $propertiesObject = @{
		     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
		    }

		    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

		    # If there is no gateway subnet, we need to create one.
		    if($gatewaySubnet -eq $null)
		    {
			    $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
			    $vnet.Subnets.Add($gatewaySubnet);
			    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
		    }

		    CreateVnetGateway $vnet.ResourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

		    $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $vnetGatewayName
	    }
	    else
	    {
		    $uriParts = $gatewaySubnet.IpConfigurations[0].Id.Split('/')
		    $gatewayResourceGroup = $uriParts[4]
		    $gatewayName = $uriParts[8]

		    $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $gatewayName

		    # validate gateway types, etc.
		    if($gateway.GatewayType -ne "Vpn")
		    {
			    Write-Error "This gateway is not of the Vpn type. It cannot be joined to an App."
			    return
		    }

		    if($gateway.VpnType -ne "RouteBased")
		    {
			    Write-Error "This gateways Vpn type is not RouteBased. It cannot be joined to an App."
			    return
		    }

		    if($gateway.VpnClientConfiguration -eq $null -or $gateway.VpnClientConfiguration.VpnClientAddressPool -eq $null)
		    {
			    Write-Host "This gateway does not have a Point-to-site Address Range. Please specify one in CIDR notation, e.g. 10.0.0.0/8"
			    $pointToSiteAddress = Read-Host "Point-To-Site Address Space"
			    Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $gateway.Name -VpnClientAddressPool $pointToSiteAddress
		    }

		    Write-Host "Creating App association to VNET"
		    $propertiesObject = @{
		     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnet.Name)"
		    }

		    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

		    # We need to check if the certificate here exists in the gateway.
		    $certificates = $gateway.VpnClientConfiguration.VpnClientRootCertificates

		    $certFound = $false
		    foreach($certificate in $certificates)
		    {
			    if($certificate.PublicCertData -eq $virtualNetwork.Properties.CertBlob)
			    {
				    $certFound = $true
				    break
			    }
		    }

		    if(-not $certFound)
		    {
			    Write-Host "Adding certificate"
			    Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName "AppServiceCertificate.cer" -PublicCertData $virtualNetwork.Properties.CertBlob -VirtualNetworkGatewayName $gateway.Name
		    }
	    }

	    # Now finish joining by getting the VPN package and giving it to the App
	    Write-Host "Retrieving VPN Package and supplying to App"
	    $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $vnet.ResourceGroupName -VirtualNetworkGatewayName $gateway.Name -ProcessorArchitecture Amd64

	    # Put the VPN client configuration package onto the App
	    $PropertiesObject = @{
	    "vnetName" = $vnet.Name; "vpnPackageUri" = $packageUri
	    }

	    New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

	    Write-Host "Finished!"
    }

    function RemoveVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
	    $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
	    $currentVnet = $webAppConfig.Properties.VnetName
	    if($currentVnet -ne $null -and $currentVnet -ne "")
	    {
		    Write-Host "Currently connected to VNET $currentVnet"

		    Remove-AzureRmResource -ResourceName "$($webAppName)/$($currentVnet)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
	    }
	  	  else
	    {
	  	  Write-Host "Not connected to a VNET."
	    }
    }

    Write-Host "Please Login"
    Login-AzureRmAccount

    # Choose subscription. If there's only one we will choose automatically

    $subs = Get-AzureRmSubscription
    $subscriptionId = ""

    if($subs.Length -eq 0)
    {
	    Write-Error "No subscriptions bound to this account."
	    return
    }

    if($subs.Length -eq 1)
    {
	    $subscriptionId = $subs[0].SubscriptionId
    }
    else
    {
	    $subscriptionChoices = @()
	    $subscriptionValues = @()

	    foreach($subscription in $subs){
	    $subscriptionChoices += "$($subscription.SubscriptionName) ($($subscription.SubscriptionId))";
	    $subscriptionValues += ($subscription.SubscriptionId);
	    }

	    $subscriptionId = PromptCustom "Choose a subscription" $subscriptionValues $subscriptionChoices
    }

    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    $resourceGroup = Read-Host "Please enter the Resource Group of your App"

    $appName = Read-Host "Please enter the Name of your App"

    $options = @("Add a NEW Virtual Network to an App", "Add an EXISTING Virtual Network to an App", "Remove a Virtual Network from an App");
    $optionValues = @(0, 1, 2)
    $option = PromptCustom "What do you want to do?" $optionValues $options

    if($option -eq 0)
    {
		AddNewVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 1)
    {
	    AddExistingVnet $subscriptionId $resourceGroup $appName
    }
	if($option -eq 2)
    {
	    RemoveVnet $subscriptionId $resourceGroup $appName
    }

Guarde una copia del script. En este artículo, se llama V2VnetAllinOne.ps1, pero puede usar otro nombre. No hay ningún argumento para este script. Simplemente ejecútelo. Lo primero que hará el script es pedirle que inicie sesión. Después de iniciar sesión, el script obtiene detalles sobre su cuenta y devuelve una lista de suscripciones. Sin contar la solicitud de credenciales, la ejecución inicial del script tiene el siguiente aspecto:

	PS C:\Users\ccompy\Documents\VNET> .\V2VnetAllInOne.ps1
	Please Login

	Environment           : AzureCloud
	Account               : ccompy@microsoft.com
	TenantId              : 722278f-fef1-499f-91ab-2323d011db47
	SubscriptionId        : af5358e1-acac-2c90-a9eb-722190abf47a
	CurrentStorageAccount :

	Choose a subscription

	1) Demo Subscription (af5358e1-acac-2c90-a9eb-722190abf47a)
	2) MS Test (a5350f55-dd5a-41ec-2ddb-ff7b911bb2ef)
	3) Purple Demo Subscription (2d4c99a4-57f9-4d5e-a0a1-0034c52db59d)

	Choose an option:
	3

	Account      : ccompy@microsoft.com
	Environment  : AzureCloud
	Subscription : 2d4c99a4-57f9-4d5e-a0a1-0034c52db59d
	Tenant       : 722278f-fef1-499f-91ab-2323d011db47

	Please enter the Resource Group of your App: hcdemo-rg
	Please enter the Name of your App: v2vnetpowershell
	What do you want to do?

	1) Add a NEW Virtual Network to an App
	2) Add an EXISTING Virtual Network to an App
	3) Remove a Virtual Network from an App

El resto de esta sección explica cada una de estas tres opciones.

### Creación de un red virtual del Administrador de recursos e integración con ella ###

Para crear una nueva red virtual que utiliza el modelo de implementación del Administrador de recursos e integrarla con la aplicación, seleccione **1) Add a NEW Virtual Network to an App** (1) agregar una NUEVA red virtual a una aplicación). Se le solicitará el nombre de la red virtual. En este caso, como puede ver en la siguiente configuración, se usó el nombre v2pshell.

El script proporciona los detalles sobre la red virtual que se va a crear. Si es necesario, se puede cambiar alguno de los valores. En esta ejecución de ejemplo se ha creado una red virtual con la siguiente configuración:

	Virtual Network Name:         v2pshell
	Resource Group Name:          hcdemo-rg
	Gateway Name:                 v2pshell-gateway
	Vnet IP name:                 v2pshell-ip
	Vnet IP config name:          v2pshell-ipconf
	Address Space:                10.0.0.0/8
	Gateway Address Space:        10.5.0.0/16
	Point-To-Site Address Space:  172.16.0.0/12

	Do you wish to change these settings?
	[Y] Yes  [N] No  [?] Help (default is "N"):

Si desea cambiar alguno de los valores, escriba **Y** y realice los cambios. Cuando esté satisfecho con la configuración de la red virtual, escriba **N** o simplemente presione Entrar cuando se le pregunte si desea cambiar la configuración. Desde aquí hasta que finalice, el script le indicará algunas acciones que va realizando hasta que empieza a crear la puerta de enlace de la red virtual. Este paso puede tardar hasta una hora. Durante esta fase no hay ningún indicador de progreso, pero el script le notificará cuando se haya creado la puerta de enlace.

Cuando finalice el script, se mostrará **Finished** (Finalizado). En este punto, tendrá una red virtual del Administrador de recursos con el nombre y la configuración que seleccionó. Esta nueva red virtual también se integrará con su aplicación.

### Integración de la aplicación con una red virtual preexistente del Administrador de recursos ###

Cuando está realizando la integración con una red virtual preexistente, si proporciona una red virtual del Administrador de recursos que no tiene una puerta de enlace o conectividad de punto a sitio, el script lo configurará. Si la red virtual ya tiene esos elementos configurados, el script pasará directamente a la integración de la aplicación. Para iniciar este proceso, simplemente seleccione **2) Add an EXISTING Virtual Network to an App** (2) Agregar una red virtual EXISTENTE a una aplicación).

Esta opción solo funciona si tiene una red virtual de Resource Manager preexistente que se encuentra en la misma suscripción que la aplicación. Después de seleccionar la opción, aparecerá una lista de las redes virtuales del Administrador de recursos.

	Select a VNET to integrate with

	1) v2demonetwork
	2) v2pshell
	3) v2vnetintdemo
	4) v2asenetwork
	5) v2pshell2

	Choose an option:
	5

Simplemente seleccione la red virtual con la que quiere realizar la integración. Si ya tiene una puerta de enlace con la conectividad de punto a sitio habilitada, el script simplemente integrará su aplicación con la red virtual. Si no tiene ninguna puerta de enlace, deberá especificar la subred de la puerta de enlace. La subred de la puerta de enlace debe estar en el espacio de direcciones de red virtual y no puede estar en ninguna otra subred. Si tiene una red virtual sin ninguna puerta de enlace y ejecuta este paso, se mostrará lo siguiente:

	This Virtual Network has no gateway. I will need to create one.
	Your VNET is in the address space 172.16.0.0/16, with the following Subnets:
	default: 172.16.0.0/24
	Please choose a GatewaySubnet address space: 172.16.1.0/26

En este ejemplo se ha creado una puerta de enlace de red virtual con la siguiente configuración:

	Virtual Network Name:         v2pshell2
	Resource Group Name:          vnetdemo-rg
	Gateway Name:                 v2pshell2-gateway
	Vnet IP name:                 v2pshell2-ip
	Vnet IP config name:          v2pshell2-ipconf
	Address Space:                172.16.0.0/16
	Gateway Address Space:        172.16.1.0/26
	Point-To-Site Address Space:  172.16.0.0/12

	Do you wish to change these settings?
	[Y] Yes  [N] No  [?] Help (default is "N"):
	Creating App association to VNET

Si desea cambiar alguna de esas configuraciones, puede hacerlo. De lo contrario, presione Entrar y el script creará la puerta de enlace y adjuntará la aplicación a la red virtual. El tiempo de creación de la puerta de enlace sigue siendo una hora, así que asegúrese de tenerlo en cuenta. Cuando todo se haya completado, el script mostrará **Finished** (Finalizado).

### Desconexión de la aplicación de una red virtual del Administrador de recursos ###

La desconexión de la aplicación de la red virtual no cierra la puerta de enlace ni deshabilita la conectividad de punto a sitio. Después de todo, lo puede seguir usando para alguna otra cosa. Tampoco lo desconecta de otras aplicaciones, excepto de la proporcionada. Para realizar esta acción, seleccione **3) Remove a Virtual Network from an App** (3) Quitar una red virtual de una aplicación). Cuando lo haga, verá algo parecido a esto:

	Currently connected to VNET v2pshell

	Confirm
	Are you sure you want to delete the following resource:
	/subscriptions/edcc99a4-b7f9-4b5e-a9a1-3034c51db496/resourceGroups/hcdemo-rg/providers/Microsoft.Web/sites/v2vnetpowers
	hell/virtualNetworkConnections/v2pshell
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Aunque el script indica eliminar, no se elimina la red virtual. Solo se quita la integración. Después de confirmar que esto es lo que desea hacer, el comando se procesa con bastante rapidez e indica **True** cuando la operación ha terminado.

<!--Links-->
[createvpngateway]: http://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/
[azureportal]: http://portal.azure.com

<!---HONumber=AcomDC_0831_2016-->