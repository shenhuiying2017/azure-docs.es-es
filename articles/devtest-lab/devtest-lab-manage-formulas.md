---
title: "Administración de fórmulas de Azure DevTest Labs para crear máquinas virtuales | Microsoft Docs"
description: "Información sobre cómo actualizar y quitar las fórmulas de Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: tarcher
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: fd334dc8cd22990456888146f41389054f2e8b29
ms.lasthandoff: 03/15/2017


---
# <a name="manage-azure-devtest-labs-formulas"></a>Administración de fórmulas de Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

El artículo [Creación de fórmulas de Azure DevTest Labs](devtest-lab-create-formulas.md#create-a-formula) lo guía a través del proceso de creación de una fórmula en Azure DevTest Labs. Una vez que tenga una fórmula, este artículo lo guía a través de la administración de las fórmulas.

## <a name="modify-a-formula"></a>Modificación de una fórmula
Para modificar una fórmula, siga estos pasos:

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
3. En la lista de laboratorios, seleccione el laboratorio que desee.  
4. En la hoja del laboratorio, seleccione **Fórmulas (bases reutilizables)**.
   
    ![Menú Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. En la hoja **Lab formulas** (Fórmulas de laboratorio), seleccione la fórmula que quiere modificar.
6. En la hoja **Update formula** (Actualizar fórmula), realice las modificaciones deseadas y seleccione **Actualizar**.

## <a name="delete-a-formula"></a>Eliminación de una fórmula
Para eliminar una fórmula, siga estos pasos:

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
3. En la lista de laboratorios, seleccione el laboratorio que desee.  
4. En la hoja **Configuración** del laboratorio, seleccione **Fórmulas**.
   
    ![Menú Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. En la hoja **Lab formulas** (Fórmulas de laboratorio), seleccione los puntos suspensivos a la derecha de la fórmula que desea eliminar.
   
    ![Menú Fórmula](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. En el menú contextual de la fórmula, seleccione **Eliminar**.
   
    ![Menú contextual de fórmula](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Seleccione **Sí** en el cuadro de diálogo de confirmación de eliminación.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Entradas blogs relacionadas
* [Custom images or formulas? (¿Imágenes personalizadas o fórmulas?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Pasos siguientes
Cuando haya creado una fórmula para usarla al crear una máquina virtual, el siguiente paso consiste en [agregar una máquina virtual al laboratorio](devtest-lab-add-vm-with-artifacts.md).


