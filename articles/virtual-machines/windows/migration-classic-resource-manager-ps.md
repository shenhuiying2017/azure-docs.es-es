---
title: "Migración a Resource Manager con PowerShell | Microsoft Docs"
description: "Este artículo lo guía a través de la migración de recursos de IaaS compatible con la plataforma de recursos, como máquinas virtuales, redes virtuales y cuentas de almacenamiento del modelo clásico al de Azure Resource Manager mediante comandos de Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 01bccb2f8d103faf77b39825a1f9ff663329ed7a
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2017
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migración de recursos de IaaS de la implementación clásica a la de Resource Manager con Azure PowerShell
En estos pasos se describe cómo utilizar los comandos de Azure PowerShell para migrar los recursos de infraestructura como servicio (IaaS) desde el modelo de implementación clásica al modelo de implementación de Azure Resource Manager.

Si lo desea, también puede migrar recursos mediante la [interfaz de línea de comandos de Azure (CLI de Azure)](../linux/migration-classic-resource-manager-cli.md).

* Lea el artículo [Migración compatible con la plataforma de recursos de IaaS del modelo clásico a Azure Resource Manager](migration-classic-resource-manager-overview.md)para obtener información sobre los escenarios de migración que se admiten.
* Si quiere obtener instrucciones detalladas y ver un tutorial de migración, consulte [Profundización técnica en la migración compatible con la plataforma de la implementación clásica a la de Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Revisión de los errores más comunes en la migración](migration-classic-resource-manager-errors.md)

<br>
Este es un diagrama de flujo para identificar el orden en que tienen que ejecutarse durante un proceso de migración.

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-plan-for-migration"></a>Paso 1: Planear la migración
Estos son algunos de los procedimientos recomendados a la hora de evaluar la migración de recursos de IaaS desde el modelo clásico a Resource Manager:

* Lea el artículo sobre [qué características y configuraciones se admiten y cuáles no](migration-classic-resource-manager-overview.md). Si tiene máquinas virtuales que usan configuraciones o características no admitidas, se recomienda esperar a que se anuncie dicha compatibilidad. De manera alternativa, si se ajusta a sus necesidades, quite esa característica o salga de esa configuración para habilitar la migración.
* Si tiene actualmente scripts automatizados que implementan la infraestructura y las aplicaciones, intente crear una configuración de prueba similar usando esos scripts para la migración. También puede configurar entornos de ejemplo mediante el Portal de Azure.

> [!IMPORTANT]
> Las puertas de enlace de aplicaciones no se admiten actualmente para realizar migraciones del modelo clásico al de Resource Manager. Si desea migrar una red virtual clásica con una instancia de Application Gateway, quite la puerta de enlace antes de ejecutar una operación de preparación para mover la red. Después, cuando termine el proceso de migración, vuelva a conectar la puerta de enlace en Azure Resource Manager.
>
>Las puertas de enlace de ExpressRoute que se conectan con circuitos de ExpressRoute en otra suscripción no se pueden migrar automáticamente. En esos casos, quite la puerta de enlace de ExpressRoute, migre la red virtual y vuelva a crear la puerta de enlace. Para obtener más información, consulte [Migración de circuitos de ExpressRoute y las redes virtuales asociadas del modelo de implementación clásica a Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md).
>
>

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Paso 2: instalar la versión más reciente de Azure PowerShell
Hay dos opciones principales para instalar Azure PowerShell: la [Galería de PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) y el [Instalador de plataforma web (WebPI)](http://aka.ms/webpi-azps). WebPI recibe actualizaciones mensuales. La galería de PowerShell recibe actualizaciones de forma continua. Este artículo se basa en los cmdlets de la versión 2.1.0 de Azure PowerShell.

Para ver las instrucciones de instalación, consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>Paso 3: Asegurarse de que es un administrador de la suscripción en Azure Portal
Para realizar esta migración, debe estar agregado como coadministrador de la suscripción en [Azure Portal](https://portal.azure.com).

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En el menú de concentrador, seleccione **Suscripción**. Si no lo ve, haga clic en **Más servicios**.
3. Busque la entrada de la suscripción adecuada y después examine el campo **MI ROL**. Para un coadministrador, el valor debe ser _Administrador de cuenta_.

Si no puede agregar un coadministrador, póngase en contacto con un administrador del servicio o coadministrador de la suscripción para que le agreguen.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Paso 4: Establecimiento de la suscripción y registro para la migración
En primer lugar, inicie un símbolo del sistema de PowerShell. Para la migración, debe configurar el entorno para el modelo clásico y el de Resource Manager.

Inicie sesión en su cuenta para el modelo de Resource Manager.

```powershell
    Login-AzureRmAccount
```

Puede encontrar las suscripciones disponibles ejecutando el siguiente comando:

```powershell
    Get-AzureRMSubscription | Sort Name | Select Name
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

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Paso 5: Verificación para garantizar que dispone de suficientes unidades vCPU de máquina virtual de Azure Resource Manager en la región de Azure de su VNET o implementación actual
Puede usar el siguiente comando de PowerShell para comprobar la cantidad de vCPU que tiene actualmente en Azure Resource Manager. Para obtener más información sobre las cuotas de vCPU, vea [Límites y Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager).

En este ejemplo se comprueba la disponibilidad en la región del **oeste de EE. UU.** Reemplace el nombre de la región de ejemplo por el suyo propio.

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>Paso 6: Ejecución de comandos para migrar los recursos de IaaS
> [!NOTE]
> Todas las operaciones que se describen aquí son idempotentes. Si tiene un problema diferente de una función no admitida o un error de configuración, se recomienda que vuelva a intentar la operación de preparación, anulación o confirmación. La plataforma intenta nuevamente la acción.
>
>

## <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Paso 6.1: Opción 1: Migración de máquinas virtuales en un servicio en la nube (no en una red virtual)
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

    En primer lugar, valide si puede migrar la red virtual con el siguiente comando:

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

## <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Paso 6.1: Opción 2: Migración de máquinas virtuales en una red virtual

Para migrar máquinas virtuales de una red virtual, migre la red virtual. Las máquinas virtuales se migran automáticamente con la red virtual. Seleccione la red virtual que quiere migrar.
> [!NOTE]
> [Migre una sola máquina virtual clásica](migrate-single-classic-to-resource-manager.md) creando una nueva máquina virtual de Resource Manager con discos administrados mediante los archivos de VHD (SO y datos) de la máquina virtual.
<br>

> [!NOTE]
> El nombre de red virtual podría ser diferente del que se muestra en el nuevo Portal. El nuevo Azure Portal muestra el nombre como `[vnet-name]` pero el nombre de red virtual real es de tipo `Group [resource-group-name] [vnet-name]`. Antes de migrar, busque el nombre real de la red virtual con el comando `Get-AzureVnetSite | Select -Property Name` o consúltelo en el antiguo Azure Portal. 

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

## <a name="step-62-migrate-a-storage-account"></a>Paso 6.2: Migración de una cuenta de almacenamiento
Cuando haya terminado de migrar las máquinas virtuales, se recomienda migrar las cuentas de almacenamiento.

Antes de migrar la cuenta de almacenamiento, lleve a cabo las comprobaciones de requisitos previos anteriores:

* **Migración de máquinas virtuales clásicas cuyos discos se almacenan en la cuenta de almacenamiento**

    El comando anterior devuelve las propiedades RoleName y DiskName de todos los discos de máquinas virtuales clásicas en la cuenta de almacenamiento. RoleName es el nombre de la máquina virtual a la que está conectado el disco. Si el comando anterior devuelve discos, asegúrese de que las máquinas virtuales a las que están conectados estos discos se migraron antes de migrar la cuenta de almacenamiento.
    ```powershell
     $storageAccountName = 'yourStorageAccountName'
      Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
      DiskName | Format-List -Property RoleName, DiskName

    ```
* **Eliminación de discos de máquinas virtuales clásicas almacenados en la cuenta de almacenamiento**

    Busque discos de máquinas virtual es clásicas en la cuenta de almacenamiento con el comando siguiente:

    ```powershell
        $storageAccountName = 'yourStorageAccountName'
        Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

    ```
    Si el comando anterior devuelve discos, elimínelos con el comando siguiente:

    ```powershell
       Remove-AzureDisk -DiskName 'yourDiskName'
    ```
* **Eliminación de imágenes de máquinas virtuales almacenadas en la cuenta de almacenamiento**

    El comando anterior devuelve todas las imágenes de máquinas virtuales con el disco de SO almacenado en la cuenta de almacenamiento.
     ```powershell
        Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                } | Select-Object -Property ImageName, ImageLabel
     ```
     El comando anterior devuelve todas las imágenes de máquinas virtuales con discos de datos almacenados en la cuenta de almacenamiento.
     ```powershell

        Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                         -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                        } | Select-Object -Property ImageName, ImageLabel
     ```
    Elimine todas las imágenes de máquinas virtuales que se devolvieron usando el comando anterior:
    ```powershell
    Remove-AzureVMImage -ImageName 'yourImageName'
    ```

Valide cada cuenta de almacenamiento para la migración mediante el siguiente comando. En este ejemplo, el nombre de cuenta de almacenamiento es **myStorageAccount**. Reemplace el nombre de ejemplo por el nombre de su propia cuenta de almacenamiento.

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
```

El paso siguiente consiste en preparar la cuenta de almacenamiento para la migración

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
* [Información general sobre la migración compatible con la plataforma de recursos de IaaS desde el modelo de implementación clásica a Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Profundización técnica en la migración compatible con la plataforma de la implementación clásica a la de Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planning for migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Planificación de la migración de recursos de IaaS del modelo clásico a Azure Resource Manager)
* [Migración de recursos de IaaS de la implementación clásica a Azure Resource Manager con la CLI de Azure](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Herramientas de la comunidad para ayudar con la migración de recursos de IaaS de la versión clásica a Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Revisión de los errores más comunes en la migración](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Revisión de las preguntas más frecuentes acerca de cómo migrar recursos de IaaS de la versión clásica a Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
