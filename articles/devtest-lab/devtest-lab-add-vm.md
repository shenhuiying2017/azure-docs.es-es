---
title: "Incorporación de una máquina virtual a un laboratorio de Azure DevTest Labs | Microsoft Docs"
description: "Obtenga información sobre cómo agregar una máquina virtual a un laboratorio de Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 7bac59810ee3c1da239716b58d41b97088035133
ms.openlocfilehash: e8de45686102cd6c4671484a05568a10dd22fa67
ms.lasthandoff: 02/27/2017


---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Incorporación de una máquina virtual a un laboratorio de Azure DevTest Labs
Puede agregar una máquina virtual a un laboratorio a partir de una *base* que puede ser una [imagen personalizada](devtest-lab-create-template.md), una [fórmula](devtest-lab-manage-formulas.md) o una [imagen de Marketplace](devtest-lab-configure-marketplace-images.md). En este tutorial se explica cómo usar Azure Portal para agregar una máquina virtual a un laboratorio de DevTest Labs.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Pasos para agregar una máquina virtual a un laboratorio de Azure DevTest Labs
1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
1. En la lista de laboratorios, seleccione aquel en el que desea crear la máquina virtual.  
1. En la hoja **Información general** del laboratorio, seleccione **+ Agregar**.  

    ![Botón Agregar VM](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. En la hoja **Elegir una base** , seleccione una base para la máquina virtual.
1. En la hoja **Máquina virtual**, escriba un nombre para la nueva máquina virtual en el cuadro de texto **Nombre de la máquina virtual**.
   
    ![Hoja de la máquina virtual de laboratorio](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Escriba un **Nombre de usuario** al que se concederán privilegios de administrador en la máquina virtual.  
1. Si quiere usar una contraseña almacenada en su [almacén secreto](https://azure.microsoft.com/en-us/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), seleccione **Use a saved secret** (Usar un secreto guardado) y especifique un valor de clave que corresponda a su secreto (contraseña). De lo contrario, escriba una contraseña en el campo de texto **Escriba un valor**.
1. El valor **Virtual machine disk type** (Tipo de disco de máquina virtual) determina qué tipo de disco de almacenamiento se admite para las máquinas virtuales del laboratorio.
1. Seleccione **Tamaño de máquina virtual** y seleccione uno de los elementos predefinidos que especifican los núcleos del procesador, el tamaño de RAM y el tamaño de la unidad de disco duro de la máquina virtual que se va a crear.
1. Seleccione **Artefactos** y, en la lista de artefactos, seleccione y configure los artefactos que desea agregar a la imagen base. 
    **Nota:** Si no está familiarizado con DevTest Labs o la configuración de artefactos, vaya a la sección [Incorporación de un artefacto existente a una máquina virtual](./devtest-lab-add-vm-with-artifacts.md#add-an-existing-artifact-to-a-vm) y vuelva aquí cuando haya finalizado.
1. Seleccione **Configuración avanzada** para configurar las opciones de expiración y las opciones de red de la máquina virtual.
1. Si desea ver o copiar la plantilla de Azure Resource Manager, vaya a la sección [Almacenamiento de una plantilla de Azure Resource Manager](./devtest-lab-add-vm-with-artifacts.md#save-azure-resource-manager-template) y vuelva aquí cuando haya finalizado.
1. Seleccione **Crear** para agregar la máquina virtual especificada al laboratorio.
1. La hoja del laboratorio muestra el estado de creación de la máquina virtual; primero como **Creating** (En creación) y luego como **Running** (En ejecución), una vez que se haya iniciado la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes
* Una vez creada la máquina virtual, puede conectarse a la misma seleccionando **Conectar** en la hoja de la máquina virtual.
* Explore la [galería de plantillas de inicio rápido de Azure Resource Manager de DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates).



