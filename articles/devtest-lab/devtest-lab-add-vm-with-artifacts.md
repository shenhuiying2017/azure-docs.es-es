---
title: "Administración de artefactos de máquina virtual en Azure DevTest Labs | Microsoft Docs"
description: "Obtenga información sobre cómo administrar artefactos de máquina virtual en Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 576509ce-6a33-4c26-87c7-de8b40271efa
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2d298c210fa46cc4473fc7e8809e55bfc102481d
ms.openlocfilehash: c970bca8f6ab9e97741370f74efc5ee66e592468
ms.lasthandoff: 02/27/2017


---
# <a name="manage-vm-artifacts-in-azure-devtest-labs"></a>Administración de artefactos de máquina virtual en Azure DevTest Labs
Los *artefactos* de Azure DevTest Labs permiten especificar las *acciones* que se realizan cuando se aprovisiona la máquina virtual. 

Las acciones del artefacto pueden realizar procedimientos como la ejecución de scripts de Windows Powershell y comandos de Bash, así como la instalación de software. 

Los *parámetros* del artefacto le permiten personalizar el artefacto para su escenario en particular.

En este artículo se explica cómo administrar los artefactos de una máquina virtual de un laboratorio.

## <a name="add-an-existing-artifact-to-a-vm"></a>Incorporación de un artefacto existente a una máquina virtual
Al crear una máquina virtual, puede agregar artefactos existentes. Cada laboratorio incluye artefactos procedentes del repositorio de artefactos de DevTest Labs público, así como los artefactos que ha creado y agregado a su propio repositorio de artefactos.
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

## <a name="next-steps"></a>Pasos siguientes
* Aprenda a [crear artefactos personalizados para la máquina virtual de DevTest Labs](devtest-lab-artifact-author.md).

