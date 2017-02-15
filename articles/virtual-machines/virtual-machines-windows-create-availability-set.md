---
title: "Creación de un conjunto de disponibilidad de máquina virtual | Microsoft Docs"
description: "Aprenda a crear un conjunto de disponibilidad para sus máquinas virtuales mediante el Portal de Azure o PowerShell con el modelo de implementación de Resource Manager."
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
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 19f22b9e38e472b56fc9abecc6c14b63b521a58b


---
# <a name="create-an-availability-set"></a>Crear un conjunto de disponibilidad
Mediante el portal, si quiere que la máquina virtual pase a formar parte de un conjunto de disponibilidad, debe crear primero el conjunto de disponibilidad.

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
4. Cuando haya terminado de especificar la información, haga clic en **Crear**. Después de crear el grupo de disponibilidad, puede verlo en la lista después de actualizar el portal.

## <a name="use-the-portal-to-create-a-virtual-machine-and-an-availability-set-at-the-same-time"></a>Uso del portal para crear una máquina virtual y un conjunto de disponibilidad al mismo tiempo
Si va a crear una nueva máquina virtual mediante el portal, también puede crear un nuevo conjunto de disponibilidad para la máquina virtual mientras crea la primera máquina virtual del conjunto.

![Captura de pantalla que muestra el proceso para crear un nuevo conjunto de disponibilidad mientras crea la máquina virtual.](./media/virtual-machines-windows-create-availability-set/new-vm-avail-set.png)

## <a name="add-a-new-vm-to-an-existing-availability-set"></a>Adición de una nueva máquina virtual a un conjunto de disponibilidad existente
Para cada máquina virtual adicional que cree que debe pertenecer al conjunto, asegúrese de que la crea en el mismo **grupo de recursos** y luego seleccione el conjunto de disponibilidad existente en el paso 3. 

![Captura de pantalla que muestra cómo seleccionar un conjunto de disponibilidad existente para la máquina virtual.](./media/virtual-machines-windows-create-availability-set/add-vm-to-set.png)

## <a name="use-powershell-to-create-an-availability-set"></a>Uso de PowerShell para crear un conjunto de disponibilidad
En este ejemplo se crea un conjunto de disponibilidad en el grupo de recursos **RMResGroup** en la ubicación **oeste de EE. UU.**. Esto se debe hacer antes de crear la primera máquina virtual que estará en el conjunto.

    New-AzureRmAvailabilitySet -ResourceGroupName "RMResGroup" -Name "AvailabilitySet03" -Location "West US"

Para más información, consulte [New-AzureRmAvailabilitySet](https://msdn.microsoft.com/library/mt619453.aspx).

## <a name="troubleshooting"></a>Solución de problemas
* Al crear una máquina virtual, si el conjunto de disponibilidad que quiere no está en la lista desplegable en el portal, puede que lo haya creado en otro grupo de recursos. Si no conoce el grupo de recursos de su conjunto de disponibilidad, vaya al menú del concentrador y haga clic en Examinar > Conjuntos de disponibilidad para ver una lista de los conjuntos de disponibilidad y los grupos de recursos a los que pertenecen.

## <a name="next-steps"></a>Pasos siguientes
Agregue almacenamiento adicional a la máquina virtual mediante la adición de un [disco de datos](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)adicional.




<!--HONumber=Nov16_HO3-->


