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
ms.date: 05/30/2017
ms.author: tarcher
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 015782373e59d1aaf10a7b089c84c982031b36b2
ms.contentlocale: es-es
ms.lasthandoff: 05/31/2017


---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Creación de un laboratorio con Laboratorios de desarrollo y pruebas de Azure
En Azure DevTest Labs, un laboratorio es la infraestructura que abarca un grupo de recursos, como máquinas virtuales (VM), que permite una mejor administración de dichos recursos mediante la especificación de límites y cuotas. Este artículo le guiará a través del proceso de creación de un laboratorio mediante Azure Portal.

## <a name="prerequisites"></a>Requisitos previos
Para crear un laboratorio necesitará:

* Una suscripción de Azure. Para información sobre las opciones de compra de Azure, consulte [Instrucciones para contratar Azure](https://azure.microsoft.com/pricing/purchase-options/) o [Evaluación gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/). Debe ser el propietario de la suscripción para crear el laboratorio.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Pasos para crear un laboratorio con Azure DevTest Labs
Los pasos siguientes muestran cómo usar Azure Portal para crear un laboratorio en Azure DevTest Labs. 

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. En el menú principal de la izquierda, seleccione **Más servicios** (en la parte inferior de la lista).

    ![Opción del menú Más servicios](./media/devtest-lab-create-lab/more-services-menu-option.png)

1. En la lista de servicios disponibles, **DevTest Labs**.
1. En la hoja **DevTest Labs**, seleccione **Agregar**.
   
    ![Incorporación de un laboratorio](./media/devtest-lab-create-lab/add-lab-button.png)

1. En la hoja **Crear un laboratorio de desarrollo y pruebas** :
   
    1. Escriba un **Nombre de laboratorio** para el nuevo laboratorio.
    2. Seleccione una **suscripción** para asociar al laboratorio.
    3. Seleccione una **Ubicación** en la que se va a almacenar el laboratorio.
    4. Seleccione **Apagado automático** para especificar si desea habilitar y definir los parámetros para el cierre automático de todas las máquinas virtuales del laboratorio. La característica de apagado automático es principalmente una característica de ahorro de costos por la que puede especificar cuándo desea que la máquina virtual se apague automáticamente. Para cambiar la configuración del apagado automático después de crear el laboratorio, siga los pasos que se describen en el artículo [Administración de todas las directivas para un laboratorio de Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).
    5. Seleccione **Anclar al panel** si desea que un acceso directo al laboratorio aparezca en el panel del portal.
    6. Seleccione **Opciones de automatización** para obtener plantillas de Azure Resource Manager para la automatización de la configuración. 
    7. Seleccione **Crear**. Después de seleccionar **Crear**, se muestra la hoja **DevTest Labs**. Para supervisar el estado del proceso de creación de un laboratorio, inspeccione el área **Notificaciones**. Una vez completado, actualice la página para ver el laboratorio recién creado en la lista de laboratorios.  
    
    ![Creación de una hoja de laboratorio](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Pasos siguientes
Una vez creado el laboratorio, le presentamos algunos pasos que se deben tener en cuenta:

* [Acceso seguro a un laboratorio](devtest-lab-add-devtest-user.md)
* [Definición de directivas de laboratorio](devtest-lab-set-lab-policy.md)
* [Creación de una plantilla de laboratorio](devtest-lab-create-template.md)
* [Creación de artefactos personalizados para máquinas virtuales](devtest-lab-artifact-author.md)
* [Incorporación de una máquina virtual con artefactos a un laboratorio](devtest-lab-add-vm-with-artifacts.md)


