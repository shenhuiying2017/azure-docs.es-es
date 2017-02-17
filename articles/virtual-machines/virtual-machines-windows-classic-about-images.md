---
title: "Acerca de las imágenes para máquinas virtuales Windows | Microsoft Docs"
description: "Obtenga información sobre cómo se usan las imágenes con máquinas virtuales con Windows en Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 66ff3fab-8e7f-4dff-b8da-ab1c9c9c9af8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/21/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: c118f9ebb8da496ad33b6e24e80d16ccf6557928


---
# <a name="about-images-for-windows-virtual-machines"></a>Acerca de las imágenes para máquinas virtuales Windows
> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Para más información sobre cómo buscar y usar imágenes en el modelo de Resource Manager, consulte [aquí](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]

## <a name="working-with-images"></a>Trabajo con imágenes
Puede usar el módulo Azure PowerShell para administrar las imágenes disponibles para su suscripción de Azure. También puede usar el Portal de Azure clásico para algunas tareas de imagen, pero la línea de comandos le ofrece más opciones.

* **Obtener todas las imágenes**:`Get-AzureVMImage`devuelve una lista de todas las imágenes disponibles en su suscripción actual (se incluyen sus imágenes y las que proporciona Azure o sus asociados). Esto significa que podría obtener una lista de gran tamaño. Los ejemplos siguientes muestra cómo obtener una lista más corta.
* **Obtener familias de imágenes**: `Get-AzureVMImage | select ImageFamily` obtiene una lista de familias de imágenes mostrando cadenas de propiedad **ImageFamily**.
* **Obtener todas las imágenes de una familia concreta**:`Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
* **Buscar imágenes de máquina virtual**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` esto funciona mediante el filtrado de la propiedad DataDiskConfiguration, que solo se aplica a imágenes de máquina virtual. Este ejemplo también filtra la salida a sólo el nombre de la imagen y la etiqueta.
* **Guardar una imagen generalizada**: `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
* **Guardar una imagen especializada**: `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
  >[Azure.Tip] El parámetro OSState es necesario si desea crear una imagen de máquina virtual que incluya los discos de datos, además del disco del sistema operativo. Si no usa el parámetro, el cmdlet crea una imagen de sistema operativo. El valor del parámetro indica si la imagen es generalizada o especializada, en función de si se ha preparado el disco del sistema operativo para su reutilización.
* **Eliminar una imagen**: `Remove-AzureVMImage –ImageName "MyOldVmImage"`

## <a name="next-steps"></a>Pasos siguientes
También puede [crear una máquina con Windows mediante el portal clásico](virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).




<!--HONumber=Dec16_HO1-->


