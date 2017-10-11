---
title: "Incorporación de una máquina virtual reclamable a un laboratorio de Azure DevTest Labs | Microsoft Docs"
description: "Obtenga información sobre cómo agregar una máquina virtual reclamable a un laboratorio de Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: tarcher
ms.openlocfilehash: 98950d72e90b0e178bae2fffa7644fd824a25eea
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Incorporación de una máquina virtual reclamable a un laboratorio de Azure DevTest Labs
Una imagen virtual reclamable se agrega a un laboratorio de forma similar a como se [agrega una máquina virtual estándar](devtest-lab-add-vm.md): desde una *base* que es una [imagen personalizada](devtest-lab-create-template.md), [fórmula](devtest-lab-manage-formulas.md), o [imagen de Marketplace](devtest-lab-configure-marketplace-images.md). Este tutorial le guía por el proceso de utilización de Azure Portal para agregar una máquina virtual reclamable a un laboratorio de DevTest Labs, y muestra el proceso que sigue un usuario para reclamar la máquina virtual.

> [!NOTE]
> Si implementa máquinas virtuales de laboratorio a través de [plantillas de Azure Resource Manager](devtest-lab-create-environment-from-arm.md), puede crear máquinas virtuales reclamables estableciendo la propiedad **allowClaim** en "true" en la sección de propiedades.
>
>

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Pasos para agregar una máquina virtual reclamable a un laboratorio de Azure DevTest Labs
1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
1. En la lista de laboratorios, seleccione aquel en el que desea crear la máquina virtual reclamable.  
1. En la hoja **Información general** del laboratorio, seleccione **+ Agregar**.  

    ![Botón Agregar VM](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. En la hoja **Elegir una base** , seleccione una base para la máquina virtual.
1. En la hoja **Máquina virtual**, escriba un nombre para la nueva máquina virtual en el cuadro de texto **Nombre de la máquina virtual**.

    ![Hoja de la máquina virtual de laboratorio](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Escriba un **Nombre de usuario** al que se concederán privilegios de administrador en la máquina virtual.  
1. Si quiere usar una contraseña almacenada en su [almacén secreto](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), seleccione **Use a saved secret** (Usar un secreto guardado) y especifique un valor de clave que corresponda a su secreto (contraseña). De lo contrario, escriba una contraseña en el campo de texto **Escriba un valor**.
1. El valor **Virtual machine disk type** (Tipo de disco de máquina virtual) determina qué tipo de disco de almacenamiento se admite para las máquinas virtuales del laboratorio.
1. Seleccione **Tamaño de máquina virtual** y seleccione uno de los elementos predefinidos que especifican los núcleos del procesador, el tamaño de RAM y el tamaño de la unidad de disco duro de la máquina virtual que se va a crear.
1. Seleccione **Artefactos** y, en la lista de artefactos, seleccione y configure los artefactos que desea agregar a la imagen base. Si no está familiarizado con DevTest Labs o la configuración de artefactos, vaya a la sección [Incorporación de un artefacto existente a una máquina virtual](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) y vuelva aquí cuando haya finalizado.
1. Seleccione **Configuración avanzada** para configurar las opciones de expiración y las opciones de red de la máquina virtual. En **Claim options** (Opciones de reclamación), elija **Sí** para que la máquina sea reclamable.

  ![Elija que la máquina virtual sea reclamable.](./media/devtest-lab-add-vm/devtestlab-claim-VM-option.png)

1. Si desea ver o copiar la plantilla de Azure Resource Manager, vaya a la sección [Almacenamiento de una plantilla de Azure Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) y vuelva aquí cuando haya finalizado.
1. Seleccione **Crear** para agregar la máquina virtual especificada al laboratorio.
1. La hoja del laboratorio muestra el estado de creación de la máquina virtual; primero como **Creating** (En creación) y luego como **Running** (En ejecución), una vez que se haya iniciado la máquina virtual.


## <a name="using-a-claimable-vm"></a>Uso de una máquina virtual reclamable

Un usuario puede reclamar cualquier máquina virtual de la lista de máquinas virtuales reclamables siguiendo alguno de estos pasos:

* En la lista de máquinas virtuales reclamables, en la parte inferior de la hoja de información general del laboratorio, haga doble clic en una de las máquinas virtuales en la lista y elija **Claim machine**  (Reclamar máquina).

 ![Solicite una máquina virtual reclamable específica.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* En la parte superior de la hoja **Información general** , elija **Claim any** (Reclamar cualquiera). Se asigna una máquina virtual aleatoria de la lista de máquinas virtuales reclamables.

 ![Solicite cualquier máquina virtual reclamable.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Una vez que un usuario reclama una máquina virtual, se mueve a su lista de máquinas virtuales y ya no podrá reclamarla otro usuario.

## <a name="next-steps"></a>Pasos siguientes
* Una vez creada la máquina virtual, puede conectarse a la misma seleccionando **Conectar** en la hoja de la máquina virtual.
* Explore la [galería de plantillas de inicio rápido de Azure Resource Manager de DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates).
