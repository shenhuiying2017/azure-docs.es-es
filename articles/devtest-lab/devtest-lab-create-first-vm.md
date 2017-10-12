---
title: "Create your first VM in a lab in Azure DevTest Labs (Creación de su primera máquina virtual en un laboratorio en Azure DevTest Labs) | Microsoft Docs"
description: "Obtenga información sobre cómo crear su primera máquina virtual en un laboratorio de Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: fbc5a438-6e02-4952-b654-b8fa7322ae5f
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2017
ms.author: tarcher
ms.openlocfilehash: aa6b60b799e1e98815cf288d5612f98cd77cc00e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-vm-in-a-lab-in-azure-devtest-labs"></a>Create your first VM in a lab in Azure DevTest Labs (Creación de su primera máquina virtual en un laboratorio en Azure DevTest Labs)

Cuando tenga acceso inicialmente a DevTest Labs y quiera crear su primera máquina virtual, es probable que lo haga con una [imagen de Marketplace base](devtest-lab-configure-marketplace-images.md) precargada. Posteriormente, también podrá elegir entre una [imagen personalizada y una fórmula](devtest-lab-add-vm.md) al crear más máquinas virtuales. 

En este tutorial se explica cómo usar Azure Portal para agregar su primera máquina virtual a un laboratorio de DevTest Labs.

## <a name="steps-to-add-your-first-vm-to-a-lab-in-azure-devtest-labs"></a>Pasos para agregar su primera máquina virtual a un laboratorio de Azure DevTest Labs
1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
1. En la lista de laboratorios, seleccione aquel en el que desea crear la máquina virtual.  
1. En la hoja **Información general** del laboratorio, seleccione **+ Agregar**.  

    ![Botón Agregar VM](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. En la hoja **Elegir una base**, seleccione una imagen de Marketplace para la máquina virtual.
1. En la hoja **Máquina virtual**, escriba un nombre para la nueva máquina virtual en el cuadro de texto **Nombre de la máquina virtual**.

    ![Hoja de la máquina virtual de laboratorio](./media/devtest-lab-add-vm/devtestlab-lab-add-first-vm.png)

1. Escriba un **Nombre de usuario** al que se concederán privilegios de administrador en la máquina virtual.  
1. Escriba una contraseña en el campo de texto **Escriba un valor**.
1. El valor **Virtual machine disk type** (Tipo de disco de máquina virtual) determina qué tipo de disco de almacenamiento se admite para las máquinas virtuales del laboratorio.
1. Seleccione **Tamaño de máquina virtual** y seleccione uno de los elementos predefinidos que especifican los núcleos del procesador, el tamaño de RAM y el tamaño de la unidad de disco duro de la máquina virtual que se va a crear.
1. Seleccione **Artefactos** y, en la lista de artefactos, seleccione y configure los artefactos que desea agregar a la imagen base.
    **Nota:** Si no está familiarizado con DevTest Labs o la configuración de artefactos, vaya a la sección [Incorporación de un artefacto existente a una máquina virtual](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) y vuelva aquí cuando haya finalizado.
1. Seleccione **Crear** para agregar la máquina virtual especificada al laboratorio.

   La hoja del laboratorio muestra el estado de creación de la máquina virtual; primero como **Creating** (En creación) y luego como **Running** (En ejecución), una vez que se haya iniciado la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes
* Una vez creada la máquina virtual, puede conectarse a la misma seleccionando **Conectar** en la hoja de la máquina virtual.
* Consulte [Incorporación de una máquina virtual a un laboratorio](devtest-lab-add-vm.md) para obtener información más completa sobre cómo agregar máquinas virtuales posteriores en su laboratorio.
* Explore la [galería de plantillas de inicio rápido de Azure Resource Manager de DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates).
