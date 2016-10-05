<properties
	pageTitle="Migración a Resource Manager con PowerShell | Microsoft Azure"
	description="Este artículo lo guía a través de la migración de recursos de IaaS compatible con la plataforma de recursos del modelo clásico al de Azure Resource Manager mediante comandos de Azure PowerShell."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="danlep"/>

# Migración de recursos de IaaS de la implementación clásica a la de Resource Manager con Azure PowerShell

En estos pasos se describe cómo utilizar los comandos de Azure PowerShell para migrar los recursos de infraestructura como servicio (IaaS) desde el modelo de implementación clásica al modelo de implementación de Azure Resource Manager. Estos pasos siguen un enfoque consistente para migrar el entorno personalizado. Tome los comandos y sustitúyalos por sus propios valores de las variables (las líneas que comienzan por "$").

Si lo desea, también puede migrar recursos mediante la [interfaz de línea de comandos de Azure (CLI de Azure)](virtual-machines-linux-cli-migration-classic-resource-manager.md).

Lea el artículo [Migración compatible con la plataforma de recursos de IaaS del modelo clásico a Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md) para obtener información sobre los escenarios de migración que se admiten. Si quiere obtener instrucciones detalladas y ver un tutorial de migración, consulte [Profundización técnica en la migración compatible con la plataforma de la implementación clásica a la de Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md).

## Paso 1: Planear la migración

Estos son algunos de los procedimientos recomendados a la hora de evaluar la migración de recursos de IaaS desde el modelo clásico a Resource Manager:

- Lea el artículo sobre [qué características y configuraciones se admiten y cuáles no](virtual-machines-windows-migration-classic-resource-manager.md). Si tiene máquinas virtuales que usan configuraciones o características no admitidas, se recomienda esperar a que se anuncie dicha compatibilidad. De manera alternativa, si se ajusta a sus necesidades, quite esa característica o salga de esa configuración para habilitar la migración.
-	Si tiene actualmente scripts automatizados que implementan la infraestructura y las aplicaciones, intente crear una configuración de prueba similar usando esos scripts para la migración. También puede configurar entornos de ejemplo mediante el Portal de Azure.

>[AZURE.IMPORTANT]En estos momentos, las puertas de enlace de Virtual Network (sitio a sitio, ExpressRoute de Azure, puerta de enlace de aplicaciones y punto a sitio) no se admiten para realizar migraciones del modelo clásico al de Resource Manager. Para migrar una red virtual clásica con una puerta de enlace, primero debe quitar la puerta de enlace antes de ejecutar una operación de confirmación para mover la red (puede realizar un paso de preparación sin eliminar la puerta de enlace). Después, cuando termine el proceso de migración, vuelva a conectar la puerta de enlace en Azure Resource Manager.

## Paso 2: instalar la versión más reciente de Azure PowerShell

Hay dos opciones principales para instalar Azure PowerShell: la [Galería de PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) y el [Instalador de plataforma web (WebPI)](http://aka.ms/webpi-azps). WebPI recibe actualizaciones mensuales. La galería de PowerShell recibe actualizaciones de forma continua. Este artículo se basa en los cmdlets de la versión 2.1.0 de Azure PowerShell.

Para ver las instrucciones de instalación, consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

## Paso 3: Establecimiento de la suscripción y registro para la migración

En primer lugar, inicie un símbolo del sistema de PowerShell. Para los escenarios de migración, debe configurar el entorno para el modelo clásico y el de Resource Manager.

Inicie sesión en su cuenta para el modelo de Resource Manager.

	Login-AzureRmAccount

Puede encontrar las suscripciones disponibles ejecutando el siguiente comando:

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Establezca la suscripción de Azure para la sesión actual. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < y >, por los nombres correctos.

	Select-AzureRmSubscription –SubscriptionName "<subscription name>"

>[AZURE.NOTE] El registro es un paso que solo se realiza una vez, pero debe hacerlo antes de intentar la migración. Si no se registra, recibirá el siguiente mensaje de error:

>	*BadRequest : Subscription is not registered for migration.* 

Regístrese con el proveedor de recursos de migración ejecutando el comando siguiente:
	
	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Espere cinco minutos a que finalice el registro. Puede comprobar el estado de la aprobación con el siguiente comando:

	Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Asegúrese de que RegistrationState sea `Registered` antes de continuar.

Ahora inicie sesión en su cuenta para el modelo clásico.

	Add-AzureAccount

Puede encontrar las suscripciones disponibles ejecutando el siguiente comando:

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Establezca la suscripción de Azure para la sesión actual. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < y >, por los nombres correctos.

	Select-AzureSubscription –SubscriptionName "<subscription name>"

## Paso 4: Verificación para garantizar que dispone de suficientes núcleos de máquina virtual de Azure Resource Manager en la región de Azure de su VNET o implementación actual

Puede utilizar el siguiente comando de PowerShell para comprobar la cantidad de núcleos que tiene actualmente en Azure Resource Manager. Para obtener más información sobre las cuotas de núcleos, consulte [Límites y Azure Resource Manager](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager).

```
Get-AzureRmVMUsage -Location "<Your VNET or Deployment's Azure region"
```

## Paso 5: Ejecución de comandos para migrar los recursos de IaaS

>[AZURE.NOTE] Todas las operaciones que se describen aquí son idempotentes. Si tiene un problema diferente de una función no admitida o un error de configuración, se recomienda que vuelva a intentar la operación de preparación, anulación o confirmación. La plataforma intenta nuevamente la acción.

### Migración de máquinas virtuales en un servicio en la nube (no en una red virtual)

Obtenga la lista de servicios en la nube mediante el siguiente comando y seleccione luego el servicio en la nube que quiera migrar. Si las máquinas virtuales del servicio en la nube están en una red virtual o si tienen roles web o de trabajo, el comando devolverá un mensaje de error.

	Get-AzureService | ft Servicename

Obtenga el nombre de la implementación del servicio en la nube mediante el siguiente comando:

	$serviceName = "<service name>"
	$deployment = Get-AzureDeployment -ServiceName $serviceName
	$deploymentName = $deployment.DeploymentName

Prepare las máquinas virtuales del servicio en la nube para la migración. Tiene dos opciones para elegir.

* **Opción 1. Migrar las máquinas virtuales a una red virtual creada en una plataforma**

	Primero, valide si puede migrar el servicio en la nube con los siguientes comandos:

		$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
		$validate.ValidationMessages

	El comando anterior muestra cualquier advertencia y error que bloquee la migración. Si la validación se realiza correctamente, podrá continuar al siguiente paso de preparación:

		Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork

* **Opción 2. Migrar a una red virtual existente en el modelo de implementación de Resource Manager**

		$existingVnetRGName = "<Existing VNET's Resource Group Name>"
		$vnetName = "<Virtual Network Name>"
		$subnetName = "<Subnet name>"

	Primero, valide si puede migrar el servicio en la nube con el siguiente comando:

		$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
		$validate.ValidationMessages

	El comando anterior muestra cualquier advertencia y error que bloquee la migración. Si la validación se realiza correctamente, podrá continuar al siguiente paso de preparación:

		Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName

Cuando la operación de preparación finalice correctamente con cualquiera de las opciones anteriores, consulte el estado de la migración de las máquinas virtuales. Asegúrese de que tienen el estado `Prepared`.

	$vmName = "<vm-name>"
	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
	$migrationState = $vm.VM.MigrationState

Compruebe la configuración de los recursos preparados mediante PowerShell o el Portal de Azure. Si no está preparado para la migración y desea volver al estado anterior, utilice el siguiente comando:

	Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

Si la configuración preparada parece correcta, puede continuar y confirmar los recursos mediante el siguiente comando:

	Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName

### Migración de máquinas virtuales en una red virtual

Para migrar máquinas virtuales de una red virtual, migre la red. Las máquinas virtuales se migran automáticamente con la red. Seleccione la red virtual que quiere migrar.

	$vnetName = "<Virtual Network Name>"

>[AZURE.NOTE] Si la red virtual contiene roles web o de trabajo, o bien máquinas virtuales con configuraciones no admitidas, recibe un mensaje de error de validación.

En primer lugar, valide si puede migrar la red virtual con el siguiente comando:

	Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName

El comando anterior muestra cualquier advertencia y error que bloquee la migración. Si la validación se realiza correctamente, podrá continuar al siguiente paso de preparación:
	
	Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

Compruebe la configuración de las máquinas virtuales preparadas mediante Azure PowerShell o Azure Portal. Si no está preparado para la migración y desea volver al estado anterior, utilice el siguiente comando:

	Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

Si la configuración preparada parece correcta, puede continuar y confirmar los recursos mediante el siguiente comando:

	Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

### Migración de una cuenta de almacenamiento

Cuando haya terminado de migrar las máquinas virtuales, se recomienda migrar las cuentas de almacenamiento.

Prepare cada cuenta de almacenamiento para la migración mediante el siguiente comando:

	$storageAccountName = "<storage account name>"
	Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName

Compruebe la configuración de la cuenta de almacenamiento preparada mediante Azure PowerShell o Azure Portal. Si no está preparado para la migración y desea volver al estado anterior, utilice el siguiente comando:

	Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName

Si la configuración preparada parece correcta, puede continuar y confirmar los recursos mediante el siguiente comando:

	Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName

## Pasos siguientes

- Para obtener más información sobre el proceso de migración, lea [Migración compatible con la plataforma de recursos de IaaS del modelo clásico al de Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md).
- Para migrar otros recursos de red a Resource Manager mediante Azure PowerShell, siga los mismos pasos con **Move-AzureNetworkSecurityGroup**, **Move-AzureReservedIP** y **Move-AzureRouteTable**.
- Para los scripts de código abierto que puede usar para migrar recursos de Azure del modelo clásico al de Resource Manager, consulte [Herramientas de la comunidad para la migración de Azure Service Management a Azure Resource Manager](virtual-machines-windows-migration-scripts.md).

<!---HONumber=AcomDC_0921_2016-->