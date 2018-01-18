---
title: "Actualizaciones de sistema operativo automáticas con conjuntos de escalado de máquinas virtuales de Azure | Microsoft Docs"
description: "Obtenga información acerca de cómo actualizar automáticamente el sistema operativo en instancias de máquina virtual en un conjunto de escalado."
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: negat
ms.openlocfilehash: 145f4ec92b142a1585ba17bf6e49c7824cc32529
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2018
---
# <a name="azure-virtual-machine-scale-set-automatic-os-upgrades"></a>Actualizaciones de sistema operativo automáticas de un conjunto de escalado de máquinas virtuales de Azure

La actualización automática de la imagen de sistema operativo es una característica en versión preliminar para conjuntos de escalado de máquinas virtuales de Azure que actualiza automáticamente todas las máquinas virtuales a la imagen de sistema operativo más reciente.

La actualización automática del sistema operativo tiene las siguientes características:

- Una vez configurada, la imagen del sistema operativo más reciente publicada por los editores de la imagen se aplica automáticamente al conjunto de escalado sin la intervención del usuario.
- Actualiza lotes de instancias de forma gradual cada vez que el editor publica una nueva imagen de plataforma.
- Se integra con el sondeo de estado de aplicación (opcional, pero muy recomendado por motivos de seguridad).
- Funciona con todos los tamaños de máquina virtual.
- Funciona con imágenes de plataforma de Windows y Linux.
- Puede rechazar las actualizaciones automáticas en cualquier momento (las actualizaciones de sistema operativo también se pueden iniciar manualmente).
- El disco del sistema operativo de una máquina virtual se reemplaza por el nuevo disco de sistema operativo creado con la versión más reciente de la imagen. Las extensiones configuradas y los scripts de datos personalizados se ejecutan, mientras se conservan los discos de datos persistentes.


## <a name="preview-notes"></a>Notas de la versión preliminar 
Mientras la versión se encuentre en estado preliminar, existen las siguientes limitaciones y restricciones:

- Las actualizaciones automáticas del sistema operativo solo admiten [cuatro SKU de sistema operativo](#supported-os-images). No hay ningún contrato de nivel de servicio ni garantías. Se recomienda que no utilice las actualizaciones automáticas en cargas de trabajo críticas de producción mientras la versión se encuentre en estado preliminar.
- La compatibilidad con conjuntos de escalado en clústeres de Service Fabric estará disponible próximamente.
- El cifrado de disco de Azure (actualmente en versión preliminar) **no** es compatible actualmente con la actualización del sistema operativo automática del conjunto de escalado de máquinas virtuales.
- Una experiencia de portal estará disponible próximamente.


## <a name="register-to-use-automatic-os-upgrade"></a>Registro para usar la actualización automática del sistema operativo
Para usar la característica de actualización de sistema operativo automatizada, registre el proveedor de la versión preliminar con [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) como se indica a continuación:

```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName AutoOSUpgradePreview
```

El estado de registro tarda unos 10 minutos en aparecer como *Registrado*. Puede comprobar el estado de registro actual con [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Una vez completado el proceso de registro, asegúrese de que el proveedor *Microsoft.Compute* está registrado con [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) como se indica a continuación:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

Se recomienda que las aplicaciones utilicen sondeos de estado. Para registrar la característica de proveedor para los sondeos de estado, utilice [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) como se indica a continuación:

```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVmssHealthProbe
```

De nuevo, el estado de registro tarda unos 10 minutos en aparecer como *Registrado*. Puede comprobar el estado de registro actual con [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Una vez completado el proceso de registro, asegúrese de que el proveedor *Microsoft.Network* está registrado con [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) como se indica a continuación:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```


## <a name="supported-os-images"></a>Imágenes de sistema operativo compatibles
Actualmente se admiten solo determinadas imágenes de plataforma del sistema operativo. Actualmente no puede usar imágenes personalizadas que haya creado usted mismo. La propiedad *versión* de la imagen de plataforma debe establecerse en *más reciente*.

Actualmente se admiten las siguientes SKU (se agregarán más):
    
| Publicador               | Oferta         |  SKU               | Versión  |
|-------------------------|---------------|--------------------|----------|
| Canonical               | UbuntuServer  | 16.04-LTS          | más reciente   |
| Microsoft Windows Server  | Windows Server | Centro de datos de 2012-R2 | más reciente   |
| Microsoft Windows Server  | Windows Server | 2016-Datacenter    | más reciente   |
| Microsoft Windows Server  | Windows Server | 2016-Datacenter-Smalldisk | más reciente   |



## <a name="application-health"></a>Estado de la aplicación
Durante una actualización del sistema operativo, las instancias de máquina virtual de un conjunto de escalado se actualizan en lote de uno en uno. La actualización debe continuar solo si la aplicación cliente se encuentra en buen estado en las instancias de máquina virtual actualizadas. Se recomienda que la aplicación proporcione señales de estado al motor de actualización de sistema operativo del conjunto de escalado. De forma predeterminada, durante las actualizaciones del sistema operativo, la plataforma se fija en el estado de energía de la máquina virtual y el estado de aprovisionamiento de la extensión para determinar si una instancia de máquina virtual está en buen estado después de una actualización. Durante la actualización del sistema operativo de una instancia de máquina virtual, se reemplaza el disco del sistema operativo en una instancia de máquina virtual por un nuevo disco basado en la versión más reciente de la imagen. Una vez finalizada la actualización del sistema operativo, las extensiones configuradas se ejecutan en estas máquinas virtuales. Solo cuando todas las extensiones en una máquina virtual se han aprovisionado correctamente, la aplicación se considera en buen estado. 

Un conjunto de escalado puede configurarse opcionalmente con sondeos de estado de aplicación para proporcionar a la plataforma información precisa sobre el estado actual de la aplicación. Los sondeos de estado de aplicación son sondeos de Load Balancer personalizados que se usan como una señal de estado. La aplicación se ejecuta en una instancia VM del conjunto de escala puede responder a solicitudes HTTP o TCP externas que indica si es correcto. Para obtener más información sobre cómo funcionan los sondeos de Load Balancer personalizados, consulte [Descripción de los sondeos del equilibrador de carga](../load-balancer/load-balancer-custom-probe-overview.md). Un sondeo de estado de aplicación no es necesario para las actualizaciones automáticas del sistema operativo, pero su uso está muy recomendado.

Si el conjunto de escalado está configurado para usar varios grupos de selección de ubicación, es necesario utilizar sondeos con una instancia de [Load Balancer estándar](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

### <a name="important-keep-credentials-up-to-date"></a>Importante: mantenga actualizadas las credenciales
Si el conjunto de escala usa las credenciales para acceder a recursos externos, por ejemplo, si se configura una extensión de máquina virtual que usa un token SAS para la cuenta de almacenamiento, debe asegurarse de que las credenciales se mantengan actualizadas. Si las credenciales, incluidos los certificados y los tokens, han expirado, se producirá un error en la actualización y el primer lote de máquinas virtuales se quedará en estado de error.

Los pasos recomendados para recuperar las máquinas virtuales y volver a habilitar la actualización automática del sistema operativo si se produce un error de autenticación de recursos son:

* Volver a generar el token (o cualquier otra credencial) pasada en las extensiones.
* Asegurarse de que cualquier credencial usada desde dentro de la máquina virtual para comunicarse con las entidades externas está actualizada.
* Actualizar las extensiones en el modelo de conjunto de escala con los tokens nuevos.
* Implementar el conjunto de escala actualizada, lo que actualizará todas las instancias de máquina virtual, incluyendo las que dieran error. 

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Configuración de un sondeo de Load Balancer personalizado como sondeo de estado de aplicación en un conjunto de escalado
Como práctica recomendada, cree un sondeo del equilibrador de carga explícitamente para el estado del conjunto de escalado. Puede utilizarse el mismo punto de conexión para un sondeo HTTP o un sondeo TCP existente, pero un sondeo de estado puede requerir un comportamiento diferente por parte de un sondeo de equilibrador de carga tradicional. Por ejemplo, un sondeo de equilibrador de carga tradicional puede devolver un estado incorrecto si la carga en la instancia es demasiado alta, mientras que puede no ser adecuado para determinar el estado de la instancia durante una actualización automática del sistema operativo. Configure el sondeo para que tenga una tasa de sondeo elevada de menos de dos minutos.

Se puede hacer referencia al sondeo de equilibrador de carga en el valor *networkProfile* del conjunto de escalado, y se puede asociar con un equilibrador de carga interno o público del modo siguiente:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```


## <a name="enforce-an-os-image-upgrade-policy-across-your-subscription"></a>Aplicación de una directiva de actualización de imagen de sistema operativo a través de su suscripción
Para las actualizaciones de seguridad, es muy recomendable exigir una directiva de actualización. Esta directiva puede requerir sondeos de estado de la aplicación a través de su suscripción. La siguiente directiva de Azure Resource Manager rechaza las implementaciones que no tienen configurados los parámetros de la actualización automatizada de la imagen del sistema operativo:

1. Obtenga la definición de directiva de Azure Resource Manager integrada con [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) como se indica a continuación:

    ```powershell
    $policyDefinition = Get-AzureRmPolicyDefinition -Id "/providers/Microsoft.Authorization/policyDefinitions/465f0161-0087-490a-9ad9-ad6217f4f43a"
    ```

2. Asigne una directiva a una suscripción con [New-AzureRmPolicyAssignment](/powershell/module/AzureRM.Resources/New-AzureRmPolicyAssignment) como se indica a continuación:

    ```powershell
    New-AzureRmPolicyAssignment `
        -Name "Enforce automatic OS upgrades with app health checks" `
        -Scope "/subscriptions/<SubscriptionId>" `
        -PolicyDefinition $policyDefinition
    ```


## <a name="configure-auto-updates"></a>Configuración de actualizaciones automáticas
Para configurar las actualizaciones automáticas, asegúrese de que la propiedad *automaticOSUpgrade* está configurada en *true* en la definición del modelo del conjunto de escalado. Puede configurar esta propiedad con Azure PowerShell o la CLI de Azure 2.0.

En el ejemplo siguiente se usa Azure PowerShell (4.4.1 o posterior) para configurar las actualizaciones automáticas del conjunto de escalado denominado *myVMSS* en el grupo de recursos denominado *myResourceGroup*:

```powershell
$rgname = myResourceGroup
$vmssname = myVMSS
$vmss = Get-AzureRmVMss -ResourceGroupName $rgname -VmScaleSetName $vmssname
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true
Update-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname -VirtualMachineScaleSet $vmss
```


En el ejemplo siguiente se usa la CLI de Azure (2.0.20 o posterior) para configurar las actualizaciones automáticas del conjunto de escalado denominado *myVMSS* en el grupo de recursos denominado *myResourceGroup*:

```azurecli
rgname="myResourceGroup"
vmssname="myVMSS"
az vmss update --name $vmssname --resource-group $rgname --set upgradePolicy.AutomaticOSUpgrade=true
```


## <a name="check-the-status-of-an-automatic-os-upgrade"></a>Comprobación del estado de una actualización automática del sistema operativo
Puede comprobar el estado de la actualización del sistema operativo más reciente realizada en el conjunto de escalado con Azure PowerShell, la CLI de Azure 2.0 o las API de REST.

### <a name="azure-powershell"></a>Azure PowerShell
En el ejemplo siguiente se usa Azure PowerShell (4.4.1 o posterior) para comprobar el estado del conjunto de escalado denominado *myVMSS* en el grupo de recursos denominado *myResourceGroup*:

```powershell
Get-AzureRmVmssRollingUpgrade -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS
```

### <a name="azure-cli-20"></a>CLI de Azure 2.0
En el ejemplo siguiente se usa la CLI de Azure (2.0.20 o posterior) para comprobar el estado del conjunto de escalado denominado *myVMSS* en el grupo de recursos denominado *myResourceGroup*:

```azurecli
az vmss rolling-upgrade get-latest --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>API DE REST
En el ejemplo siguiente se usa la API de REST para comprobar el estado del conjunto de escalado denominado *myVMSS* en el grupo de recursos denominado *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/rollingUpgrades/latest?api-version=2017-03-30`
```

La llamada GET devuelve propiedades similares a la salida del ejemplo siguiente:

```json
{
  "properties": {
    "policy": {
      "maxBatchInstancePercent": 20,
      "maxUnhealthyInstancePercent": 5,
      "maxUnhealthyUpgradedInstancePercent": 5,
      "pauseTimeBetweenBatches": "PT0S"
    },
    "runningStatus": {
      "code": "Completed",
      "startTime": "2017-06-16T03:40:14.0924763+00:00",
      "lastAction": "Start",
      "lastActionTime": "2017-06-22T08:45:43.1838042+00:00"
    },
    "progress": {
      "successfulInstanceCount": 3,
      "failedInstanceCount": 0,
      "inprogressInstanceCount": 0,
      "pendingInstanceCount": 0
    }
  },
  "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
  "location": "southcentralus"
}
```


## <a name="automatic-os-upgrade-execution"></a>Ejecución de actualización automática del sistema operativo
Para ampliar el uso de sondeos de estado de aplicación, las actualizaciones del sistema operativo del conjunto de escalado ejecutan los pasos siguientes:

1. Si hay más de un 20 % de instancias con estado incorrecto, detenga la actualización; en caso contrario, continúe.
2. Identifique el siguiente lote de instancias de máquina virtual para actualizar, donde un lote puede tener como máximo el 20% de la cantidad total de instancias.
3. Actualice el sistema operativo del siguiente lote de instancias de máquina virtual.
4. Si más de un 20 % de las instancias actualizadas tienen estado incorrecto, detenga la actualización; en caso contrario, continúe.
5. Si el cliente ha configurado los sondeos de estado de aplicación, la actualización espera hasta 5 minutos para que los sondeos devuelvan un estado correcto y después continúa inmediatamente con el siguiente lote; de lo contrario, espera 30 minutos antes de continuar con el siguiente lote.
6. Si quedan instancias por actualizar, vaya al paso 1) para el siguiente lote; en caso contrario, la actualización está completa.

El motor de actualización del sistema operativo del conjunto de escalado comprueba el estado global de la instancia de la máquina virtual antes de actualizar cada lote. Mientras se actualiza un lote, es posible que estén teniendo lugar de manera simultánea otras tareas de mantenimiento planeadas o no planeadas en los centros de datos de Azure, lo cual podría afectar a la disponibilidad de sus máquinas virtuales. Por lo tanto, es posible que más del 20% de instancias estén temporalmente fuera de servicio. En tales casos, la actualización del conjunto de escalado se detiene al final del lote actual.


## <a name="deploy-with-a-template"></a>Implementación con una plantilla

Puede usar la plantilla siguiente para implementar un conjunto de escalado que usa las actualizaciones automáticas <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>Actualizaciones graduales automáticas - Ubuntu 16.04-LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## <a name="next-steps"></a>pasos siguientes
Para obtener más ejemplos sobre cómo usar las actualizaciones automáticas de sistema operativo con conjuntos de escalado, consulte el [repositorio de GitHub para las características en versión preliminar](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
