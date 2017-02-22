---
title: "Creación de un conjunto de disponibilidad de VM en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo crear un conjunto de disponibilidad administrado o no administrado para sus máquinas virtuales mediante Azure PowerShell o el portal con el modelo de implementación de Resource Manager."
keywords: conjunto de disponibilidad
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a3db8659-ace8-4e78-8b8c-1e75c04c042c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 204fa369dd6db618ec5340317188681b0a2988e3
ms.openlocfilehash: f7562c2bb6ad354ece3aa3c51fdaabad8e878fa9


---
# <a name="create-an-availability-set"></a>Crear un conjunto de disponibilidad
Los conjuntos de disponibilidad proporcionan redundancia en la aplicación. Se recomienda agrupar dos máquinas virtuales o más en un conjunto de disponibilidad. Esta configuración garantiza que durante un evento de mantenimiento planeado o no planeado, al menos una máquina virtual estará disponible y cumplirá el 99,95% de los niveles de servicio contratados de Azure. Para obtener más información, consulte [Acuerdo de Nivel de Servicio para máquinas virtuales](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Las VM deben crearse en el mismo grupo de recursos que el conjunto de disponibilidad.
> 

Si quiere que la VM pase a formar parte de un conjunto de disponibilidad, debe crear el conjunto de disponibilidad primero o mientras crea la primera VM en el conjunto. Si la VM va a utilizar Managed Disks, el conjunto de disponibilidad debe crearse como un conjunto de disponibilidad administrado.

Para más información sobre la creación y el uso de conjuntos de disponibilidad, consulte [Administración de la disponibilidad de las máquinas virtuales](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="use-the-portal-to-create-an-availability-set-before-creating-your-vms"></a>Uso del portal para crear un conjunto de disponibilidad antes de crear las máquinas virtuales
1. En el menú del concentrador, haga clic en **Examinar** y seleccione **Conjuntos de disponibilidad**.
2. En la hoja **Conjuntos de disponibilidad**, haga clic en **Agregar.**.
   
    ![Captura de pantalla que muestra el botón Agregar para crear un nuevo conjunto de disponibilidad.](./media/virtual-machines-windows-create-availability-set/add-availability-set.png)
3. En la hoja **Crear conjunto de disponibilidad** , rellene la información del conjunto.
   
    ![Captura de pantalla que muestra la información que debe especificar para crear el conjunto de disponibilidad.](./media/virtual-machines-windows-create-availability-set/create-availability-set.png)
   
   * **Nombre** : el nombre debe tener entre 1 y 80 caracteres y estar formado por números, letras, puntos, guiones y caracteres de subrayado. El primer carácter deben ser una letra o un número. El último carácter debe ser una letra, un número o un carácter de subrayado.
   * **Dominios de error** : los dominios de error definen un grupo de máquinas virtuales que comparten un origen de alimentación y un interruptor de red comunes. De forma predeterminada, las máquinas virtuales están separadas entre tres dominios de error como máximo y pueden cambiarse a 1, 2 o 3.
   * **Dominios de actualización**: de forma predeterminada se asignan cinco dominios de actualización y este valor se puede establecer en un número entre 1 y 20. Los dominios de actualización indican grupos de máquinas virtuales y hardware físico subyacente que se pueden reiniciar al mismo tiempo. Por ejemplo, si especificamos cinco dominios de actualización, cuando se configuran más de cinco máquinas virtuales dentro de un único conjunto de disponibilidad, la sexta máquina virtual se colocará en el mismo dominio de actualización que la primera, la séptima en el mismo que la segunda y así sucesivamente. El orden de los reinicios puede no ser secuencial, pero solo un dominio de actualización se reiniciará de manera simultánea.
   * **Suscripción** : si tiene varias suscripciones, seleccione la que quiera usar.
   * **Grupo de recursos** : seleccione un grupo de recursos existente; para ello, haga clic en la flecha y seleccione un grupo de recursos de la lista desplegable. Otra manera de crear un nuevo grupo de recursos es escribir un nombre. El nombre puede contener cualquiera de los siguientes caracteres: letras, números, puntos, guiones, caracteres de subrayado y paréntesis de apertura o cierre. El nombre no puede terminar con un punto. Todas las máquinas virtuales del grupo de disponibilidad deben crearse en el mismo grupo de recursos que el conjunto de disponibilidad.
   * **Ubicación** : seleccione una ubicación de la lista desplegable.
   * **Administrado**: seleccione *Sí* para crear un conjunto de disponibilidad administrado para usarlo con las VM que usan Managed Disks para el almacenamiento. Seleccione **No** si las VM que formarán parte del conjunto usan discos no administrados en una cuenta de almacenamiento.
   
4. Cuando haya terminado de especificar la información, haga clic en **Crear**. 

## <a name="use-the-portal-to-create-a-virtual-machine-and-an-availability-set-at-the-same-time"></a>Uso del portal para crear una máquina virtual y un conjunto de disponibilidad al mismo tiempo
Si va a crear una nueva máquina virtual mediante el portal, también puede crear un nuevo conjunto de disponibilidad para la máquina virtual mientras crea la primera máquina virtual del conjunto. Si decide usar Managed Disks para la VM, se creará un conjunto de disponibilidad administrado.

![Captura de pantalla que muestra el proceso para crear un nuevo conjunto de disponibilidad mientras crea la máquina virtual.](./media/virtual-machines-windows-create-availability-set/new-vm-avail-set.png)

## <a name="add-a-new-vm-to-an-existing-availability-set-in-the-portal"></a>Incorporación de una nueva VM a un conjunto de disponibilidad existente en el portal
Para cada máquina virtual adicional que cree que debe pertenecer al conjunto, asegúrese de que la crea en el mismo **grupo de recursos** y luego seleccione el conjunto de disponibilidad existente en el paso 3. 

![Captura de pantalla que muestra cómo seleccionar un conjunto de disponibilidad existente para la máquina virtual.](./media/virtual-machines-windows-create-availability-set/add-vm-to-set.png)

## <a name="use-powershell-to-create-an-availability-set"></a>Uso de PowerShell para crear un conjunto de disponibilidad
En este ejemplo se crea un conjunto de disponibilidad denominado **myAvailabilitySet** en el grupo de recursos **myResourceGroup** en la ubicación **Oeste de EE. UU.** Esto se debe hacer antes de crear la primera máquina virtual que estará en el conjunto.

Antes de comenzar, asegúrese de que tiene la última versión del módulo AzureRM.Compute de PowerShell. Ejecute el siguiente comando para realizar la instalación.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para más información, consulte [Azure PowerShell Versioning](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning) (Control de versiones de Azure PowerShell).


Si usa discos administrados para las VM, escriba:

```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "myResourceGroup" '
    -Name "myAvailabilitySet" -Location "West US" -managed
```

Si usa sus propias cuentas de almacenamiento para las VM, escriba:

```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "myResourceGroup" '
    -Name "myAvailabilitySet" -Location "West US" 
```

Para más información, consulte [New-AzureRmAvailabilitySet](/powershell/new-azurermavailabilityset).

## <a name="troubleshooting"></a>Solución de problemas
* Al crear una máquina virtual, si el conjunto de disponibilidad que quiere no está en la lista desplegable en el portal, puede que lo haya creado en otro grupo de recursos. Si no conoce el grupo de recursos de su conjunto de disponibilidad, vaya al menú del concentrador y haga clic en Examinar > Conjuntos de disponibilidad para ver una lista de los conjuntos de disponibilidad y los grupos de recursos a los que pertenecen.

## <a name="next-steps"></a>Pasos siguientes
Agregue almacenamiento adicional a la máquina virtual mediante la adición de un [disco de datos](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)adicional.




<!--HONumber=Feb17_HO2-->


