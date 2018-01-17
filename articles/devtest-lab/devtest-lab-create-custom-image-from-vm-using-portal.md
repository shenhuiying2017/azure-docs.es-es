---
title: "Creación de una imagen personalizada de Azure DevTest Labs a partir de una máquina virtual | Microsoft Azure"
description: "Aprenda a crear una imagen personalizada en Azure DevTest Labs a partir de una máquina virtual aprovisionada mediante el portal de Azure."
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: v-craic
ms.openlocfilehash: dc315bcc625ea98244bb5804ce6ff1c13d0ec7b1
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="create-a-custom-image-from-a-vm"></a>Crear una imagen personalizada a partir de una máquina virtual

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Instrucciones paso a paso

Puede crear una imagen personalizada a partir de una máquina virtual aprovisionada y luego usar esa imagen personalizada para crear máquinas virtuales idénticas. Los siguientes pasos muestran cómo crear una imagen personalizada desde una máquina virtual:

1. Inicie sesión en el [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Todos los servicios** y, luego, **DevTest Labs** en la lista.

1. En la lista de laboratorios, seleccione el laboratorio que desee.  

1. En el panel principal del laboratorio, seleccione **Mis máquinas virtuales**.
 
1. En el panel **Mis máquinas virtuales**, seleccione la máquina virtual desde la que quiere crear la imagen personalizada.

1. En el panel de administración de la máquina virtual, seleccione **Create custom image (VHD)**(Crear imagen personalizada [VHD]).

    ![Crear elemento de menú de imagen personalizada](./media/devtest-lab-create-template/create-custom-image.png)

1. En el panel **Imagen personalizada**, escriba un nombre y una descripción para la imagen personalizada. Esta información se muestra en la lista de bases cuando se crea una máquina virtual.

    ![Creación de un panel de imagen personalizada](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Seleccione si sysprep se ha ejecutado en la máquina virtual. Si no se ha ejecutado sysprep en la máquina virtual, especifique si quiere ejecutar sysprep cuando se cree una máquina virtual a partir de esta imagen personalizada.

1. Cuando termine de crear la imagen personalizada, seleccione **Aceptar** .

Después de unos minutos, la imagen personalizada se crea y se almacena dentro de la cuenta de almacenamiento del laboratorio. Cuando un usuario de laboratorio quiera crear una nueva máquina virtual, la imagen estará disponible en la lista de imágenes base.

![Imagen personalizada disponible en la lista de imágenes base](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Entradas blogs relacionadas

- [Custom images or formulas? (¿Imágenes personalizadas o fórmulas?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copying Custom Images between Azure DevTest Labs (Copiar imágenes personalizadas entre instancias de Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>pasos siguientes

- [Agregar una máquina virtual al laboratorio](devtest-lab-add-vm.md)
