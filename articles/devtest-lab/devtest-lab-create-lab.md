---
title: "Creación de un laboratorio en Azure DevTest Labs | Microsoft Docs"
description: "Creación de un laboratorio en Azure DevTest Labs para máquinas virtuales"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 29c4c2a2818468a2fa8360eebd4b653bdcbbde19
ms.openlocfilehash: 0749d371466226343227c79db544a8e3dca0cca8


---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Creación de un laboratorio con Laboratorios de desarrollo y pruebas de Azure
## <a name="prerequisites"></a>Requisitos previos
Para crear un laboratorio necesitará:

* Una suscripción de Azure. Para información sobre las opciones de compra de Azure, consulte [Instrucciones para contratar Azure](https://azure.microsoft.com/pricing/purchase-options/) o [Evaluación gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/). Debe ser el propietario de la suscripción para crear el laboratorio.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Pasos para crear un laboratorio con Azure DevTest Labs
Los pasos siguientes muestran cómo usar Azure Portal para crear un laboratorio en Azure DevTest Labs. 

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
3. En la hoja **DevTest Labs**, seleccione **Agregar**.
   
    ![Incorporación de un laboratorio](./media/devtest-lab-create-lab/add-lab-button.png)
4. En la hoja **Crear un laboratorio de desarrollo y pruebas** :
   
   1. Escriba un **Nombre de laboratorio** para el nuevo laboratorio.
   2. Seleccione una **suscripción** para asociar al laboratorio.
   3. Seleccione una **Ubicación** en la que se va a almacenar el laboratorio.
   4. Seleccione **Apagado automático** para especificar si desea habilitar y definir los parámetros para el cierre automático de todas las máquinas virtuales del laboratorio. 
   5. Seleccione **Crear**.
      
      ![Creación de una hoja de laboratorio](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Pasos siguientes
Una vez creado el laboratorio, le presentamos algunos pasos que se deben tener en cuenta:

* [Acceso seguro a un laboratorio](devtest-lab-add-devtest-user.md)
* [Definición de directivas de laboratorio](devtest-lab-set-lab-policy.md)
* [Creación de una plantilla de laboratorio](devtest-lab-create-template.md)
* [Creación de artefactos personalizados para máquinas virtuales](devtest-lab-artifact-author.md)
* [Incorporación de una máquina virtual con artefactos a un laboratorio](devtest-lab-add-vm-with-artifacts.md)




<!--HONumber=Jan17_HO1-->


