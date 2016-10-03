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


# Actualización de un conjunto de escalas de máquina virtual

En este artículo se describe cómo puede implementar una actualización del sistema operativo en un conjunto de escalas de máquina virtual de Azure sin tiempos de inactividad. En este contexto, una actualización del SO cambia la versión o la SKU del sistema operativo o el URI de una imagen personalizada. Para actualizar sin tiempos de inactividad las máquinas virtuales deben actualizarse una a una o en grupos (por ejemplo, un dominio de error a la vez), en lugar de todas a la vez. Al hacerlo, todas las máquinas virtuales que no están actualizando pueden actualizarse.

Para evitar ambigüedades, vamos a distinguir tres tipos de actualización del sistema operativo que le recomendamos realizar:

1. Cambiar la versión o la SKU de una imagen de plataforma. Por ejemplo, cambiar la versión 14.04.2-LTS de Ubuntu desde la 14.04.201506100 a la 14.04.201507060, o bien cambiar la SKU más reciente o la 15.10 de Ubuntu a la 16.04.0-LTS o la última versión. Este escenario se describe en este artículo.

2. Crea una nueva versión de una imagen personalizada y quiere cambiar el URI que apunta a la imagen (Propiedades -> perfilMáquinaVirtual - > perfilAlmacenamiento -> discoOS -> imagen -> URI). Este escenario se describe en este artículo.

3. Aplicar revisiones en el SO desde una máquina virtual (por ejemplo, instalar una revisión de seguridad mediante Windows Update, etc.). Este escenario es posible, pero no se trata en este artículo.

Los dos primeros constituyen requisitos de compatibilidad. Para el tercero, al menos por ahora, tendrá que crear un nuevo conjunto de escalas que se encargue de ello. En este artículo se explican las opciones 1 y 2. Nota: No hablaremos de los conjuntos de escalas de máquina virtual que se implementan como parte de un clúster de [Service Fabric](https://azure.microsoft.com/services/service-fabric/).

La secuencia básica para cambiar la versión o la SKU del SO de una imagen de plataforma o el URI de una imagen personalizada es la siguiente:

* Obtener el modelo de VMSS.

* Cambiar el valor del URI, la SKU o la versión en el modelo.

* Actualizar el modelo.

* Realizar una llamada a manualUpgrade en las máquinas virtuales del conjunto de escalas. Este paso solo es pertinente si se establece la propiedad upgradePolicy del conjunto de escalas en Manual. Si se establece en Automático, todas las máquinas virtuales se actualizan al mismo tiempo, lo que provoca tiempos de inactividad.


Tenga en cuenta esta información a la hora de actualizar la versión de un conjunto de escalas en PowerShell y la API de REST. Aunque estos ejemplos tratan el caso de una imagen de plataforma, esperamos que le hayamos proporcionado la información suficiente para adaptar este proceso a una imagen personalizada.

## PowerShell

En este ejemplo se actualiza un conjunto de escalas de máquina virtual Windows a una nueva versión 4.0.20160229. Después de actualizar el modelo, se actualiza una instancia de máquina virtual a la vez.

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the VMSS model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

Si estaba actualizando el URI de una imagen personalizada en lugar de cambiar la versión de la imagen de plataforma, podría reemplazar la línea set the new version (establecer la nueva versión) por un texto similar al siguiente:

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```


## Mediante la API de REST

A continuación, se muestran un par de ejemplos de Python que usan la API de REST de Azure para distribuir una actualización de versión del SO. Ambos usan la biblioteca [azurerm](https://pypi.python.org/pypi/azurerm) ligera de funciones de contenedor de la API de REST de Azure para realizar una operación GET en el modelo de conjuntos de escalas y, después, una operación PUT con un modelo actualizado. También analizan las vistas de instancias de máquina virtual para identificar las máquinas virtuales por dominio de actualización.

### vmssupgrade

vmssupgrade es el script de Python para implementar una actualización del SO en un conjunto de escalas de máquina virtual en ejecución (un dominio de actualización a la vez). Puede encontrarlo [aquí](https://github.com/gbowerman/vmsstools).

![Captura de pantalla de vmssupgrade](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

Este script permite elegir máquinas virtuales específicas para actualizar o especificar un dominio de actualización. Asimismo, permite cambiar una versión de la imagen de plataforma o el URI de una imagen personalizada.

### vmsseditor

Esta herramienta es un editor de fin general para conjuntos de escalas de máquina virtual que muestra el estado de la máquina virtual como un mapa térmico, donde cada fila representa un dominio de actualización. Entre otras características, puede actualizar el modelo de un VMSS con una nueva versión, la SKU o el URI de la imagen personalizada y, después, seleccionar los dominios de error para actualizar. Al hacer esto, todas las máquinas virtuales de ese dominio de actualización se actualizan al nuevo modelo. También podría llevar a cabo una actualización gradual en función del tamaño de lote que elija. Puede encontrar vmsseditor en el siguiente [repositorio de GitHub](https://github.com/gbowerman/vmssdashboard).

Por ejemplo, aquí hemos actualizado el modelo de un conjunto de escalas de la versión 14.04-2LTS a la 14.04.201507060 de Ubuntu. Tenga en cuenta que esta captura de pantalla es antigua; se han agregado muchas más opciones a esta herramienta.

![Captura de pantalla 1 de vmsseditor](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

Después de volver a hacer clic en Actualizar y en Obtener los detalles, las máquinas virtuales del dominio de actualización 0 se empiezan a actualizar.

![captura de pantalla 2 de vmsseditor](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)

<!---HONumber=AcomDC_0921_2016-->