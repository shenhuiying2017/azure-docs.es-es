<properties
	pageTitle="Migración de recursos de IaaS de la implementación clásica a la de Resource Manager con Azure PowerShell | Microsoft Azure"
	description="Este artículo le guía a través de la migración compatible con la plataforma de recursos del modelo clásico al de Azure Resource Manager mediante scripts de PowerShell"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# Migración de recursos de IaaS de la implementación clásica a la de Resource Manager con Azure PowerShell

En estos pasos se describe cómo utilizar los comandos de Azure PowerShell para migrar los recursos de infraestructura como servicio (IaaS) desde el modelo de implementación clásica al modelo de implementación de Azure Resource Manager. Estos pasos siguen un enfoque consistente para migrar el entorno personalizado. Solo tiene que tomar los comandos y sustituir sus propios valores de las variables (las líneas que comienzan por "$").

## Paso 1: Preparación para la migración

Estos son algunos de los procedimientos recomendados a la hora de evaluar la migración de recursos de IaaS desde el modelo clásico a Resource Manager:

- Consulte la [lista de configuraciones o características no admitidas](virtual-machines-windows-migration-classic-resource-manager.md). Si tiene máquinas virtuales que usan configuraciones o características no admitidas, se recomienda esperar a que se anuncie dicha compatibilidad. Como alternativa, puede quitar esa característica o salir de esa configuración para habilitar la migración si se ajusta a sus necesidades.
-	Si tiene actualmente scripts automatizados que implementan la infraestructura y las aplicaciones, intente crear una configuración de prueba similar usando esos scripts para la migración. También puede configurar entornos de ejemplo mediante el Portal de Azure.
- Puesto que el servicio está en versión preliminar pública, asegúrese de que el entorno de prueba para la migración esté aislado de su entorno de producción. No mezcle cuentas de almacenamiento, redes virtuales u otros recursos entre los entornos de prueba y producción.

## Paso 2: instalar la versión más reciente de Azure PowerShell

Hay dos opciones principales para la instalación, [Galería de PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) y [Instalador de plataforma web (WebPI)](http://aka.ms/webpi-azps). WebPI recibirá actualizaciones mensuales. La Galería de PowerShell recibirá actualizaciones de forma continua.

Para más información, vea [Azure PowerShell 1.0](https://azure.microsoft.com//blog/azps-1-0/).

## Paso 3: establecer la suscripción y registrarse para la versión preliminar pública de la migración

En primer lugar, inicie un símbolo del sistema de PowerShell. Para los escenarios de migración, debe configurar el entorno para el modelo clásico y el de Resource Manager.

Inicie sesión en su cuenta para el modelo de Resource Manager.

	Login-AzureRmAccount

Puede encontrar las suscripciones disponibles mediante el siguiente comando.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Establecezca la suscripción de Azure para la sesión actual. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >, por los nombres correctos.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Regístrese para la versión preliminar pública con el siguiente comando.

	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Espere cinco minutos a que finalice el registro. Puede comprobar el estado de la aprobación con el siguiente comando. Asegúrese de que RegistrationState es `Registered` antes de continuar.

	Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Ahora inicie sesión en su cuenta para el modelo clásico.

	Add-AzureAccount

Puede encontrar las suscripciones disponibles mediante el siguiente comando.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Establecezca la suscripción de Azure para la sesión actual. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >, por los nombres correctos.

	$subscr="<subscription name>"
	Get-AzureSubscription –SubscriptionName $subscr | Select-AzureSubscription

## Paso 4: ejecutar comandos para migrar los recursos de IaaS

>[AZURE.NOTE] Todas las operaciones que se describen aquí son idempotentes. Si tiene un problema diferente de una función no admitida o un error de configuración, se recomienda que vuelva a intentar la operación de preparación, anulación o confirmación. De ese modo, la plataforma intentará de nuevo la acción.

### Migración de máquinas virtuales en un servicio en la nube (no en una red virtual)

Obtenga la lista de servicios en la nube mediante el siguiente comando y seleccione luego el servicio en la nube que quiera migrar. Tenga en cuenta que si las máquinas virtuales del servicio en la nube están en una red virtual o tienen roles web o de trabajo, recibirá un mensaje de error.

	Get-AzureService | ft Servicename

Obtenga el nombre de la implementación del servicio en la nube mediante el siguiente comando.

	$serviceName = "<service name>"
	$deployment = Get-AzureDeployment -ServiceName $serviceName
	$deploymentName = $deployment.DeploymentName

Prepare las máquinas virtuales del servicio en la nube para la migración. Tiene dos opciones para elegir.

Si quiere migrar las máquinas virtuales a una red virtual creada en una plataforma, use el siguiente comando.

	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork

Si quiere migrar a una red virtual existente en el modelo de implementación de Resource Manager, utilice el siguiente comando.

	$existingVnetRGName = "<Existing VNET's Resource Group Name>"
	$vnetName = "<Virtual Network Name>"
	$subnetName = "<Subnet name>"
	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName 		-VirtualNetworkName $vnetName -SubnetName $subnetName

Una vez que la operación de preparación finaliza correctamente, puede consultar el estado de migración de las máquinas virtuales y asegurarse de que las máquinas virtuales están en estado `Prepared`.

	$vmName = "<vm-name>"
	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
	$migrationState = $vm.VM.MigrationState

Compruebe la configuración de los recursos preparados mediante PowerShell o el Portal de Azure. Si no está preparado para la migración y desea volver al estado anterior, utilice el siguiente comando.

	Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

Si la configuración preparada parece correcta, puede continuar y confirmar los recursos mediante el siguiente comando.

	Move-AzureService -Commit -ServiceName docmigtest1 -DeploymentName docmigtest1

### Migración de máquinas virtuales en una red virtual

Seleccione la red virtual que quiere migrar. Tenga en cuenta que si la red virtual contiene roles web o de trabajo, o bien máquinas virtuales con configuraciones no admitidas, recibirá un mensaje de error de validación.

Prepare la red virtual para la migración mediante el siguiente comando.

	$vnetName = "VNET-Name"
	Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

Compruebe la configuración de las máquinas virtuales preparadas mediante PowerShell o el Portal de Azure. Si no está preparado para la migración y desea volver al estado anterior, utilice el siguiente comando.

	Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

Si la configuración preparada parece correcta, puede continuar y confirmar los recursos mediante el siguiente comando.

	Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

## Pasos siguientes

- [Migración compatible con la plataforma de recursos de IaaS del modelo clásico al de Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
- [Profundización técnica en la migración compatible con la plataforma de la implementación clásica a la de Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Clonación de una máquina virtual clásica en Azure Resource Manager con scripts de PowerShell](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0601_2016-->