---
title: "Migración a Resource Manager con PowerShell | Microsoft Docs"
description: "Este artículo lo guía a través de la migración de recursos de IaaS compatible con la plataforma de recursos del modelo clásico al de Azure Resource Manager mediante comandos de Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: e90036d97451b271451d0ba5845c788ac05d7abf
ms.openlocfilehash: 4253d60a8a12877a3c5dac073bd06d70d020ccdc


---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migración de recursos de IaaS de la implementación clásica a la de Resource Manager con Azure PowerShell
En estos pasos se describe cómo utilizar los comandos de Azure PowerShell para migrar los recursos de infraestructura como servicio (IaaS) desde el modelo de implementación clásica al modelo de implementación de Azure Resource Manager. 

Si lo desea, también puede migrar recursos mediante la [interfaz de línea de comandos de Azure (CLI de Azure)](virtual-machines-linux-cli-migration-classic-resource-manager.md).

* Lea el artículo [Migración compatible con la plataforma de recursos de IaaS del modelo clásico a Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)para obtener información sobre los escenarios de migración que se admiten. 
* Si quiere obtener instrucciones detalladas y ver un tutorial de migración, consulte [Profundización técnica en la migración compatible con la plataforma de la implementación clásica a la de Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md).
* [Revisión de los errores más comunes en la migración](virtual-machines-migration-errors.md)

## <a name="step-1-plan-for-migration"></a>Paso 1: Planear la migración
Estos son algunos de los procedimientos recomendados a la hora de evaluar la migración de recursos de IaaS desde el modelo clásico a Resource Manager:

* Lea el artículo sobre [qué características y configuraciones se admiten y cuáles no](virtual-machines-windows-migration-classic-resource-manager.md). Si tiene máquinas virtuales que usan configuraciones o características no admitidas, se recomienda esperar a que se anuncie dicha compatibilidad. De manera alternativa, si se ajusta a sus necesidades, quite esa característica o salga de esa configuración para habilitar la migración.
* Si tiene actualmente scripts automatizados que implementan la infraestructura y las aplicaciones, intente crear una configuración de prueba similar usando esos scripts para la migración. También puede configurar entornos de ejemplo mediante el Portal de Azure.

> [!IMPORTANT]
> Las puertas de enlace de aplicaciones no se admiten actualmente para realizar migraciones del modelo clásico al de Resource Manager. Para migrar una red virtual clásica con una puerta de enlace de aplicaciones, debe quitar la puerta de enlace antes de ejecutar una operación de confirmación para mover la red (puede ejecutar el paso de preparación sin eliminar la puerta de enlace de aplicaciones). Después, cuando termine el proceso de migración, vuelva a conectar la puerta de enlace en Azure Resource Manager. Debe ponerse en contacto con el soporte técnico si quiere migrar puertas de enlace de ExpressRoute en casos en los que la puerta de enlace y el circuito de ExpressRoute se encuentren en la misma suscripción. Las puertas de enlace de ExpressRoute que se conectan con circuitos de ExpressRoute en otra suscripción no se pueden migrar. En esos casos, quite la puerta de enlace de ExpressRoute, migre la red virtual y vuelva a crear la puerta de enlace.
> 
> 

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Paso 2: instalar la versión más reciente de Azure PowerShell
Hay dos opciones principales para instalar Azure PowerShell: la [Galería de PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) y el [Instalador de plataforma web (WebPI)](http://aka.ms/webpi-azps). WebPI recibe actualizaciones mensuales. La galería de PowerShell recibe actualizaciones de forma continua. Este artículo se basa en los cmdlets de la versión 2.1.0 de Azure PowerShell.

Para ver las instrucciones de instalación, consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs).

<br>

## <a name="step-3-set-your-subscription-and-sign-up-for-migration"></a>Paso 3: Establecimiento de la suscripción y registro para la migración
En primer lugar, inicie un símbolo del sistema de PowerShell. Para la migración, debe configurar el entorno para el modelo clásico y el de Resource Manager.

Inicie sesión en su cuenta para el modelo de Resource Manager.

```powershell
    Login-AzureRmAccount
```

Puede encontrar las suscripciones disponibles ejecutando el siguiente comando:

```powershell
    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName
```

Establezca la suscripción de Azure para la sesión actual. En este ejemplo se establece el nombre de la suscripción predeterminado en **My Azure Subscription** (Mi suscripción de Azure). Reemplace el nombre de la suscripción de ejemplo por el suyo propio. 

```powershell
    Select-AzureRmSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> El registro es un paso que solo se realiza una vez, pero debe hacerlo antes de intentar la migración. Si no se registra, recibirá el siguiente mensaje de error: 
> 
> *BadRequest: Subscription is not registered for migration* (BadRequest: la suscripción no está registrada para la migración) 
> 
> 

Regístrese con el proveedor de recursos de migración ejecutando el comando siguiente:

```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Espere cinco minutos a que finalice el registro. Puede comprobar el estado de la aprobación con el siguiente comando:

```powershell
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Asegúrese de que RegistrationState sea `Registered` antes de continuar. 

Ahora inicie sesión en su cuenta para el modelo clásico.

```powershell
    Add-AzureAccount
```

Puede encontrar las suscripciones disponibles ejecutando el siguiente comando:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Establezca la suscripción de Azure para la sesión actual. En este ejemplo se establece la suscripción predeterminada en **My Azure Subscription** (Mi suscripción de Azure). Reemplace el nombre de la suscripción de ejemplo por el suyo propio. 

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-4-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Paso 4: Verificación para garantizar que dispone de suficientes núcleos de máquina virtual de Azure Resource Manager en la región de Azure de su VNET o implementación actual
Puede utilizar el siguiente comando de PowerShell para comprobar la cantidad de núcleos que tiene actualmente en Azure Resource Manager. Para obtener más información sobre las cuotas de núcleos, consulte [Límites y Azure Resource Manager](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager). 

En este ejemplo se comprueba la disponibilidad en la región del **oeste de EE. UU.** Reemplace el nombre de la región de ejemplo por el suyo propio. 

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Paso 5: Ejecución de comandos para migrar los recursos de IaaS
> [!NOTE]
> Todas las operaciones que se describen aquí son idempotentes. Si tiene un problema diferente de una función no admitida o un error de configuración, se recomienda que vuelva a intentar la operación de preparación, anulación o confirmación. La plataforma intenta nuevamente la acción.
> 
> 

### <a name="migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Migración de máquinas virtuales en un servicio en la nube (no en una red virtual)
Obtenga la lista de servicios en la nube mediante el siguiente comando y seleccione luego el servicio en la nube que quiera migrar. Si las máquinas virtuales del servicio en la nube están en una red virtual o si tienen roles web o de trabajo, el comando devolverá un mensaje de error.

```powershell
    Get-AzureService | ft Servicename
```

Obtenga el nombre de la implementación del servicio en la nube. En este ejemplo, el nombre de servicio es **My Service** (Mi servicio). Reemplace el nombre del servicio de ejemplo por el suyo propio. 

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Prepare las máquinas virtuales del servicio en la nube para la migración. Tiene dos opciones para elegir.

* **Opción 1. Migrar las máquinas virtuales a una red virtual creada en una plataforma**
  
    Primero, valide si puede migrar el servicio en la nube con los siguientes comandos:
  
    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```
  
    El comando anterior muestra cualquier advertencia y error que bloquee la migración. Si la validación se realiza correctamente, podrá pasar al siguiente paso de **preparación**:
  
    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Opción 2. Migrar a una red virtual existente en el modelo de implementación de Resource Manager**
  
    En este ejemplo se establece el nombre del grupo de recursos en **myResourceGroup**, el nombre de red virtual en **myVirtualNetwork** y el nombre de la subred en **mySubNet**. Reemplace los nombres del ejemplo por los nombres de sus propios recursos.
  
    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```
  
    Primero, valide si puede migrar el servicio en la nube con el siguiente comando:
  
    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```
  
    El comando anterior muestra cualquier advertencia y error que bloquee la migración. Si la validación se realiza correctamente, podrá continuar al siguiente paso de preparación:
  
    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Cuando la operación de preparación finalice correctamente con cualquiera de las opciones anteriores, consulte el estado de la migración de las máquinas virtuales. Asegúrese de que tienen el estado `Prepared` .

En este ejemplo se establece el nombre de la máquina virtual en **myVM**. Reemplace el nombre del ejemplo por su propio nombre de la máquina virtual.

    ```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
    ```

Compruebe la configuración de los recursos preparados mediante PowerShell o el Portal de Azure. Si no está preparado para la migración y desea volver al estado anterior, utilice el siguiente comando:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Si la configuración preparada parece correcta, puede continuar y confirmar los recursos mediante el siguiente comando:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="migrate-virtual-machines-in-a-virtual-network"></a>Migración de máquinas virtuales en una red virtual
Para migrar máquinas virtuales de una red virtual, migre la red. Las máquinas virtuales se migran automáticamente con la red. Seleccione la red virtual que quiere migrar. 

En este ejemplo se establece el nombre de red virtual en **myVnet**. Reemplace el nombre de la red virtual de ejemplo por el suyo propio. 

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Si la red virtual contiene roles web o de trabajo, o bien máquinas virtuales con configuraciones no admitidas, recibe un mensaje de error de validación.
> 
> 

En primer lugar, valide si puede migrar la red virtual con el siguiente comando:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

El comando anterior muestra cualquier advertencia y error que bloquee la migración. Si la validación se realiza correctamente, podrá continuar al siguiente paso de preparación:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Compruebe la configuración de las máquinas virtuales preparadas mediante Azure PowerShell o Azure Portal. Si no está preparado para la migración y desea volver al estado anterior, utilice el siguiente comando:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Si la configuración preparada parece correcta, puede continuar y confirmar los recursos mediante el siguiente comando:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="migrate-a-storage-account"></a>Migración de una cuenta de almacenamiento
Cuando haya terminado de migrar las máquinas virtuales, se recomienda migrar las cuentas de almacenamiento.

Prepare cada cuenta de almacenamiento para la migración mediante el siguiente comando: En este ejemplo, el nombre de cuenta de almacenamiento es **myStorageAccount**. Reemplace el nombre de ejemplo por el nombre de su propia cuenta de almacenamiento. 

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
```

Compruebe la configuración de la cuenta de almacenamiento preparada mediante Azure PowerShell o Azure Portal. Si no está preparado para la migración y desea volver al estado anterior, utilice el siguiente comando:

```powershell
    Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
```

Si la configuración preparada parece correcta, puede continuar y confirmar los recursos mediante el siguiente comando:

```powershell
    Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
```

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información sobre el proceso de migración, lea [Migración compatible con la plataforma de recursos de IaaS del modelo clásico al de Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md).
* Para migrar otros recursos de red a Resource Manager mediante Azure PowerShell, siga los mismos pasos con [Move-AzureNetworkSecurityGroup](https://msdn.microsoft.com/library/mt786729.aspx), [Move-AzureReservedIP](https://msdn.microsoft.com/library/mt786752.aspx) y [Move-AzureRouteTable](https://msdn.microsoft.com/library/mt786718.aspx).
* Para los scripts de código abierto que puede usar para migrar recursos de Azure del modelo clásico al de Resource Manager, consulte [Herramientas de la comunidad para la migración de Azure Service Management a Azure Resource Manager](virtual-machines-windows-migration-scripts.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Feb17_HO2-->


