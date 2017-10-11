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
ms.openlocfilehash: 449bffb040dafc8edd0b8b0afd80dbea35cd28ac
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Incorporación de una máquina virtual a un laboratorio de Azure DevTest Labs
Si ya ha [creado su primera máquina virtual](devtest-lab-create-first-vm.md), es probable que lo hiciera desde una [imagen de Marketplace](devtest-lab-configure-marketplace-images.md) precargada. Ahora, si desea agregar máquinas virtuales posteriores al laboratorio, también puede elegir una *base* que sea una [imagen personalizada](devtest-lab-create-template.md) o una [fórmula](devtest-lab-manage-formulas.md). En este tutorial se explica cómo usar Azure Portal para agregar una máquina virtual a un laboratorio de DevTest Labs.

En este artículo se explica cómo administrar los artefactos de una máquina virtual de un laboratorio.

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
1. Si quiere usar una contraseña almacenada en su [almacén secreto](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), seleccione **Use a saved secret** (Usar un secreto guardado) y especifique un valor de clave que corresponda a su secreto (contraseña). De lo contrario, escriba una contraseña en el campo de texto **Escriba un valor**.
1. El valor **Virtual machine disk type** (Tipo de disco de máquina virtual) determina qué tipo de disco de almacenamiento se admite para las máquinas virtuales del laboratorio.
1. Seleccione **Tamaño de máquina virtual** y seleccione uno de los elementos predefinidos que especifican los núcleos del procesador, el tamaño de RAM y el tamaño de la unidad de disco duro de la máquina virtual que se va a crear.
1. Seleccione **Artefactos** y, en la lista de artefactos, seleccione y configure los artefactos que desea agregar a la imagen base.
    **Nota:** Si no está familiarizado con DevTest Labs o la configuración de artefactos, vaya a la sección [Incorporación de un artefacto existente a una máquina virtual](#add-an-existing-artifact-to-a-vm) y vuelva aquí cuando haya finalizado.
1. Seleccione **Configuración avanzada** para configurar las opciones de expiración y las opciones de red de la máquina virtual. 

   Para establecer una opción de expiración, elija el icono del calendario para especificar una fecha en la que la VM se eliminará automáticamente.  De forma predeterminada, la VM nunca expirará. 
1. Si desea ver o copiar la plantilla de Azure Resource Manager, vaya a la sección [Almacenamiento de una plantilla de Azure Resource Manager](#save-azure-resource-manager-template) y vuelva aquí cuando haya finalizado.
1. Seleccione **Crear** para agregar la máquina virtual especificada al laboratorio.
1. La hoja del laboratorio muestra el estado de creación de la máquina virtual; primero como **Creating** (En creación) y luego como **Running** (En ejecución), una vez que se haya iniciado la máquina virtual.

> [!NOTE]
> [Agregar una máquina virtual reclamable](devtest-lab-add-claimable-vm.md) muestra cómo convertir una máquina virtual en reclamable para que esté disponible con el fin de que pueda usarlo cualquier usuario del laboratorio.
>
>

## <a name="add-an-existing-artifact-to-a-vm"></a>Incorporación de un artefacto existente a una máquina virtual
Al crear una máquina virtual, puede agregar artefactos existentes. Cada laboratorio incluye artefactos procedentes del repositorio de artefactos de DevTest Labs público, así como los artefactos que ha creado y agregado a su propio repositorio de artefactos.

* Los *artefactos* de Azure DevTest Labs permiten especificar las *acciones* que se realizan al aprovisionarse la máquina virtual, como, por ejemplo, la ejecución de scripts de Windows PowerShell, la ejecución de comandos Bash y la instalación de software.
* Los *parámetros* del artefacto le permiten personalizar el artefacto para su escenario en particular.

Para descubrir cómo crear artefactos, consulte el artículo [Creación de artefactos personalizados para la máquina virtual de DevTest Labs](devtest-lab-artifact-author.md).

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
1. En la lista de laboratorios, seleccione el que contiene la máquina virtual con la que desea trabajar.  
1. Seleccione **My virtual machines** (Mis máquinas virtuales).
1. Seleccione la máquina virtual deseada.
1. Seleccione **Artifacts** (Artefactos). 
1. Seleccione **Apply artifacts** (Aplicar artefactos).
1. En la hoja **Apply artifacts** (Aplicar artefactos), seleccione el artefacto que desea agregar a la máquina virtual.
1. En la hoja **Agregar artefacto**, especifique los valores de parámetros necesarios y cualquier otro parámetro opcional que precise.  
1. Seleccione **Agregar** para agregar el artefacto y vuelva a la hoja **Apply artifacts** (Aplicar artefactos).
1. Siga agregando los artefactos que necesite para la máquina virtual.
1. Cuando haya agregado los artefactos, puede [cambiar el orden en que se ejecutan](#change-the-order-in-which-artifacts-are-run). También puede volver atrás para [ver o modificar un artefacto](#view-or-modify-an-artifact).
1. Cuando haya terminado de agregar artefactos, seleccione **Aplicar**.

## <a name="change-the-order-in-which-artifacts-are-run"></a>Cambio del orden en que se ejecutan los artefactos
De forma predeterminada, las acciones de los artefactos se ejecutan en el orden en que se agregan a la máquina virtual. Los siguientes pasos muestran cómo cambiar el orden en que se ejecutan los artefactos.

1. En la parte superior de la hoja **Apply artifacts** (Aplicar artefactos) , seleccione el vínculo que indica cuántos artefactos se han agregado a la máquina virtual.
   
    ![Número de artefactos que se agregan a la máquina virtual](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. En la hoja **Selected artifacts** (Artefactos seleccionados), arrastre los artefactos y colóquelos en el orden deseado. **Nota:** Si tiene problemas para arrastrar el artefacto, asegúrese de que lo arrastra desde el lado izquierdo del artefacto. 
1. Seleccione **Aceptar** cuando haya terminado.  

## <a name="view-or-modify-an-artifact"></a>ver o modificar un artefacto
Los siguientes pasos muestran cómo ver o modificar los parámetros de un artefacto:

1. En la parte superior de la hoja **Apply artifacts** (Aplicar artefactos) , seleccione el vínculo que indica cuántos artefactos se han agregado a la máquina virtual.
   
    ![Número de artefactos que se agregan a la máquina virtual](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. En la hoja **Selected artifacts** (Artefactos seleccionados), seleccione el artefacto que desea ver o modificar.  
1. En la hoja **Agregar artefacto**, realice los cambios necesarios y seleccione **Aceptar** para cerrar la hoja **Agregar artefacto**.
1. Seleccione **Aceptar** para cerrar la hoja **Selected Artifacts** (Artefactos seleccionados).

## <a name="save-azure-resource-manager-template"></a>Almacenamiento de una plantilla de Azure Resource Manager
Una plantilla de Azure Resource Manager proporciona una manera declarativa de definir una implementación repetible. Los siguientes pasos explican cómo guardar la plantilla de Azure Resource Manager para la máquina virtual que se va a crear.
Una vez guardada, puede utilizar la plantilla de Azure Resource Manager para [implementar nuevas máquinas virtuales con Azure PowerShell](../azure-resource-manager/resource-group-overview.md#template-deployment).

1. En la hoja **Máquina virtual**, seleccione **View ARM Template** (Ver plantilla de ARM).
2. En la hoja **View Azure Resource Manager Template** (Ver plantilla de Azure Resource Manager), seleccione todo el texto de la plantilla.
3. Copie el texto seleccionado en el Portapapeles.
4. Seleccione **Aceptar** para cerrar la hoja **View Azure Resource Manager Template** (Ver plantilla de Azure Resource Manager).
5. Abra un editor de texto.
6. Pegue el texto de la plantilla desde el Portapapeles.
7. Guarde el archivo para su uso posterior.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Pasos siguientes
* Una vez creada la máquina virtual, puede conectarse a la misma seleccionando **Conectar** en la hoja de la máquina virtual.
* Aprenda a [crear artefactos personalizados para la máquina virtual de DevTest Labs](devtest-lab-artifact-author.md).
* Explore la [galería de plantillas de inicio rápido de Azure Resource Manager de DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
