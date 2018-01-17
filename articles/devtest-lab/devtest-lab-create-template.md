---
title: "Creación de una imagen personalizada de Azure DevTest Labs a partir de un archivo VHD | Microsoft Azure"
description: Aprenda a crear una imagen personalizada en Azure DevTest Labs a partir de un archivo VHD mediante el portal de Azure.
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: v-craic
ms.openlocfilehash: d1f1b9948fb591484c107818a01e141932effbba
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Crear una imagen personalizada a partir de un archivo VHD

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Instrucciones paso a paso

Los siguientes pasos le guían en la creación de una imagen personalizada a partir de un archivo VHD mediante el portal de Azure:

1. Inicie sesión en el [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Todos los servicios** y, luego, **DevTest Labs** en la lista.

1. En la lista de laboratorios, seleccione el laboratorio que desee.  

1. En el panel principal del laboratorio, seleccione **Configuración y directivas**. 

1. En el panel **Configuración y directivas**, seleccione **Imágenes personalizadas**.

1. En el panel **Imágenes personalizadas**, seleccione **+Agregar**.

    ![Adición de imágenes personalizadas](./media/devtest-lab-create-template/add-custom-image.png)

1. Escriba el nombre de la imagen personalizada. Este nombre se muestra en la lista de imágenes base al crear una máquina virtual.

1. Escriba la descripción de la imagen personalizada. Esta descripción se muestra en la lista de imágenes base al crear una máquina virtual.

1. En **Tipo de SO**, seleccione **Windows** o **Linux**.

    - Si selecciona **Windows**, especifique mediante la casilla si se ha ejecutado *sysprep* en la máquina. 
    - Si selecciona **Linux**, especifique mediante la casilla si se ha ejecutado *deprovision* en la máquina. 

1. Seleccione un **VHD** en el menú desplegable. Es el disco duro virtual que se usará para crear la nueva imagen personalizada. Si es necesario, seleccione **Cargar un VHD con PowerShell**.

1. También puede escribir el nombre, la oferta y el publicador del plan si la imagen utilizada para crear la imagen personalizada no la publicó Microsoft.

1. Seleccione **Aceptar** para crear la imagen personalizada.

Después de unos minutos, la imagen personalizada se crea y se almacena dentro de la cuenta de almacenamiento del laboratorio. Cuando un usuario de laboratorio quiera crear una nueva máquina virtual, la imagen estará disponible en la lista de imágenes base.

![Imagen personalizada disponible en la lista de imágenes base](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Entradas blogs relacionadas

- [Custom images or formulas? (¿Imágenes personalizadas o fórmulas?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copying Custom Images between Azure DevTest Labs (Copiar imágenes personalizadas entre instancias de Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>pasos siguientes

- [Agregar una máquina virtual al laboratorio](./devtest-lab-add-vm.md)
