<properties
    pageTitle="Implementación de una aplicación en conjuntos de escalado de máquinas virtuales| Microsoft Azure"
    description="Implementación de una aplicación en conjuntos de escalado de máquinas virtuales"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="guybo"/>



# <a name="upgrade-a-virtual-machine-scale-set"></a>Actualización de un conjunto de escalado de máquinas virtuales

En este artículo se describe cómo puede implementar una actualización del sistema operativo en un conjunto de escalado de máquinas virtuales de Azure sin tiempos de inactividad. En este contexto, una actualización del SO implica un cambio de versión o de SKU del sistema operativo o del URI de una imagen personalizada. Para actualizar sin tiempos de inactividad, las máquinas virtuales deben actualizarse una a una o en grupos (por ejemplo, un dominio de error a la vez), en lugar de todas a la vez. Al hacerlo, todas las máquinas virtuales que no se estén actualizando siguen funcionando.

Para evitar ambigüedades, vamos a distinguir tres tipos de actualización del sistema operativo que le recomendamos realizar:

- Cambiar la versión o la SKU de una imagen de plataforma. Por ejemplo, cambiar la versión 14.04.2-LTS de Ubuntu de la 14.04.201506100 a la 14.04.201507060, o bien cambiar la SKU más reciente o la 15.10 de Ubuntu a la 16.04.0-LTS o la última versión. Este escenario se describe en este artículo.

- Cambiar la URI que apunta a una nueva versión de imagen personalizada que cree (**properties** > **virtualMachineProfile** > **storageProfile** > **osDisk** > **image** > **uri**). Este escenario se describe en este artículo.

- Aplicar revisiones al sistema operativo desde una máquina virtual (por ejemplo, instalar una revisión de seguridad y ejecutar Windows Update). Este escenario es posible, pero no se trata en este artículo.

Las dos primeras opciones son los requisitos de compatibilidad tratados en este artículo. Debe crear un nuevo conjunto de escalado para ejecutar la tercera opción.

No hablaremos de los conjuntos de escalado de máquinas virtuales que se implementan como parte de un clúster de [Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) .

La secuencia básica para cambiar la versión o la SKU del SO de una imagen de plataforma o el URI de una imagen personalizada es la siguiente:

1. Crear un modelo de conjunto de escalado de máquinas virtuales.

2. Cambiar el valor del URI, la SKU o la versión en el modelo.

3. Actualizar el modelo.

4. Realizar una llamada a *manualUpgrade* en las máquinas virtuales del conjunto de escalado. Este paso solo es pertinente si *upgradePolicy* se establece en **Manual** en el conjunto de escalado. Si se establece en **Automático**, todas las máquinas virtuales se actualizan al mismo tiempo, lo cual provoca tiempos de inactividad.


Tenga en cuenta esta información a la hora de actualizar la versión de un conjunto de escalado en PowerShell y con la API de REST. Aunque estos ejemplos tratan el caso de una imagen de plataforma, en este artículo se proporciona la información suficiente para adaptar este proceso a una imagen personalizada.

## <a name="powershell##"></a>PowerShell##

En este ejemplo se actualiza un conjunto de escalado de máquinas virtuales Windows a la nueva versión 4.0.20160229. Después de actualizar el modelo, se actualiza una instancia de máquina virtual a la vez.

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the virtual machine scale set model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

Si está actualizando el URI de una imagen personalizada en lugar de cambiar la versión de la imagen de plataforma, reemplace la línea para establecer la nueva versión por un texto similar al siguiente:

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```


## <a name="the-rest-api"></a>API de REST

A continuación, se muestran un par de ejemplos de Python que usan la API de REST de Azure para distribuir una actualización de versión del SO. Ambos usan la biblioteca ligera [azurerm](https://pypi.python.org/pypi/azurerm) de funciones de contenedor de la API de REST de Azure para realizar una operación GET en el modelo de conjunto de escalado y una operación PUT con un modelo actualizado. También analizan las vistas de instancias de máquina virtual para identificar las máquinas virtuales por dominio de actualización.

### <a name="vmssupgrade"></a>Vmssupgrade

 [Vmssupgrade](https://github.com/gbowerman/vmsstools) es el script de Python para implementar una actualización del SO en un conjunto de escalado de máquinas virtuales en ejecución (un dominio de actualización a la vez).

![Script vmssupgrade para elegir las máquinas virtuales o un dominio de actualización](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

Este script le permite elegir las máquinas virtuales específicas para actualizar o especificar un dominio de actualización. Admite cambiar una versión de la imagen de plataforma o el URI de una imagen personalizada.

### <a name="vmsseditor"></a>Vmsseditor

[Vmsseditor](https://github.com/gbowerman/vmssdashboard) es un editor general para conjuntos de escalado de máquinas virtuales que muestra el estado estas en un mapa térmico, donde cada fila representa un dominio de actualización. Entre otras características, puede actualizar el modelo de conjunto de escalado de máquinas virtuales con una nueva versión, la SKU o el URI de la imagen personalizada y, después, seleccionar los dominios de error para actualizar. Al hacer esto, todas las máquinas virtuales de ese dominio de actualización se actualizan al nuevo modelo. Como alternativa, puede realizar una actualización sucesiva en función del tamaño de lote de su elección.  

La captura de pantalla siguiente muestra un modelo de conjunto de escalado para la versión 14.04.201507060 de Ubuntu 14.04-2LTS. Desde que se realizó esta captura de pantalla se han agregado muchas más opciones a esta herramienta.

![Modelo de vmsseditor de conjunto de escalado para Ubuntu 14.04-2LTS](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

Tras hacer clic en **Actualizar** y en **Obtener detalles**, las máquinas virtuales de UD 0 comienzan la actualización.

![Vmsseditor con la actualización en curso](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)



<!--HONumber=Oct16_HO2-->


