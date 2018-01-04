---
title: "Creación y administración de VM reclamables en un laboratorio de Azure DevTest Labs | Microsoft Docs"
description: "Obtenga información sobre cómo agregar una máquina virtual reclamable a un laboratorio de Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: v-craic
ms.openlocfilehash: a27423a75cb2b5063156109ea9ee3a45fa036c07
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Creación y administración de VM reclamables en Azure DevTest Labs
Una imagen virtual reclamable se agrega a un laboratorio de forma similar a como se [agrega una máquina virtual estándar](devtest-lab-add-vm.md): desde una *base* que es una [imagen personalizada](devtest-lab-create-template.md), [fórmula](devtest-lab-manage-formulas.md), o [imagen de Marketplace](devtest-lab-configure-marketplace-images.md). Este tutorial le guía por el proceso de utilización de Azure Portal para agregar una máquina virtual reclamable a un laboratorio de DevTest Labs, y muestra los procesos que sigue un usuario para reclamar la VM y anular la reclamación de esta.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Pasos para agregar una máquina virtual reclamable a un laboratorio de Azure DevTest Labs
1. Inicie sesión en el [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
1. En la lista de laboratorios, seleccione aquel en el que desea crear la máquina virtual reclamable.  
1. En el panel **Información general** del laboratorio, seleccione **+ Agregar**.  

    ![Botón Agregar VM](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. En el área **Elegir una base** , seleccione una base para la máquina virtual.
1. En el panel **Máquina virtual**, escriba un nombre para la nueva máquina virtual en el cuadro de texto **Nombre de la máquina virtual**.

    ![Panel de VM del laboratorio](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Escriba un **Nombre de usuario** al que se concederán privilegios de administrador en la máquina virtual.  
1. Si quiere usar una contraseña almacenada en su [almacén secreto](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), seleccione **Use a saved secret** (Usar un secreto guardado) y especifique un valor de clave que corresponda a su secreto (contraseña). De lo contrario, escriba una contraseña en el campo de texto **Escriba un valor**.
1. El valor **Virtual machine disk type** (Tipo de disco de máquina virtual) determina qué tipo de disco de almacenamiento se admite para las máquinas virtuales del laboratorio.
1. Seleccione **Tamaño de máquina virtual** y seleccione uno de los elementos predefinidos que especifican los núcleos del procesador, el tamaño de RAM y el tamaño de la unidad de disco duro de la máquina virtual que se va a crear.
1. Seleccione **Artefactos** y, en la lista de artefactos, seleccione y configure los artefactos que desea agregar a la imagen base. Si no está familiarizado con DevTest Labs o la configuración de artefactos, vaya a la sección [Incorporación de un artefacto existente a una máquina virtual](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) y vuelva aquí cuando haya finalizado.
1. Seleccione **Configuración avanzada** para configurar las opciones de expiración y las opciones de red de la máquina virtual. En **Claim options** (Opciones de reclamación), elija **Sí** para que la máquina sea reclamable.

  ![Elija que la máquina virtual sea reclamable.](./media/devtest-lab-add-vm/devtestlab-claim-VM-option.png)

1. Si desea ver o copiar la plantilla de Azure Resource Manager, vaya a la sección [Almacenamiento de una plantilla de Azure Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) y vuelva aquí cuando haya finalizado.
1. Seleccione **Crear** para agregar la máquina virtual especificada al laboratorio.

   Se muestra el estado de la creación de la VM; primero como **Creating** (En creación) y luego como **Running** (En ejecución), una vez que se haya iniciado la VM.

> [!NOTE]
> Si implementa máquinas virtuales de laboratorio a través de [plantillas de Azure Resource Manager](devtest-lab-create-environment-from-arm.md), puede crear máquinas virtuales reclamables estableciendo la propiedad **allowClaim** en "true" en la sección de propiedades.
>
>

## <a name="using-a-claimable-vm"></a>Uso de una máquina virtual reclamable

Un usuario puede reclamar cualquier máquina virtual de la lista de máquinas virtuales reclamables siguiendo alguno de estos pasos:

* En la lista de máquinas virtuales reclamables, en la parte inferior del panel de información general del laboratorio, haga doble clic en una de las máquinas virtuales en la lista y elija **Claim machine**  (Reclamar máquina).

 ![Solicite una máquina virtual reclamable específica.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* En la parte superior del panel de información general , elija **Claim any** (Reclamar cualquiera). Se asigna una máquina virtual aleatoria de la lista de máquinas virtuales reclamables.

 ![Solicite cualquier máquina virtual reclamable.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Una vez que un usuario reclama una máquina virtual, se mueve a su lista de máquinas virtuales y ya no podrá reclamarla otro usuario.

## <a name="unclaim-a-vm"></a>Anular la reclamación de una VM

Cuando un usuario ha terminado de usar una VM reclamada y desea ponerla a disposición de otra persona, puede devolver la VM reclamada a la lista de máquinas virtuales reclamables siguiendo uno de estos pasos:

- En la lista de "Mis máquinas virtuales", haga clic con el botón derecho en una de las VM de la lista, o seleccione los puntos suspensivos (...) y elija **Anular reclamación**.

  ![Anule la reclamación de una VM en la lista de VM.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- En la lista de "Mis máquinas virtuales", seleccione una VM para abrir el panel de administración y, luego, seleccione **Anular reclamación** desde la barra de menú superior.

  ![Anule la reclamación de VM en el panel de administración de VM.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Cuando un usuario anule la reclamación de VM, ya no tendrá permisos para esa VM de laboratorio específica.

### <a name="transferring-the-data-disk"></a>Transferencia de disco de datos
Si una VM reclamable dispone de un disco de datos conectado a ella y un usuario anula la reclamación de ella, el disco de datos continua con la VM. Cuando otro usuario reclama esa VM, el nuevo usuario reclama el disco de datos, así como la VM.

Esto se conoce como "transferencia del disco de datos". El disco de datos luego pasa a estar disponible en la lista del usuario de **Mis discos de datos** para su administración.

![Anule la reclamación de los discos de datos.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>pasos siguientes
* Una vez que se haya creado, puede conectarse a la VM. Para ello, seleccione **Conectar** en el panel de administración.
* Explore la [galería de plantillas de inicio rápido de Azure Resource Manager de DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
