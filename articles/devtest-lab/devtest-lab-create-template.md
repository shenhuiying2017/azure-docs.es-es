---
title: "Administración de imágenes personalizadas de Azure DevTest Labs para crear máquinas virtuales | Microsoft Docs"
description: "Aprenda a crear una imagen personalizada desde un archivo VHD o desde una máquina virtual existente en Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 62d64f5a59a003b2ca15d424c8844dee716bf147


---
# <a name="manage-azure-devtest-labs-custom-images-to-create-vms"></a>Administración de imágenes personalizadas de Azure DevTest Labs para crear máquinas virtuales
En Azure DevTest Labs, las imágenes personalizadas le permiten crear máquinas virtuales rápidamente sin tener que esperar a que todo el software necesario esté instalado en la máquina de destino. Las imágenes personalizadas le permiten instalar previamente todo el software que necesita en un archivo VHD y luego usar dicho archivo para crear la máquina virtual. Dado que el software ya está instalado, la máquina virtual se tarda mucho menos tiempo en crear. Además, las imágenes personalizadas se usan para clonar máquinas virtuales mediante la creación de una imagen personalizada a partir de una máquina virtual y la posterior creación de máquinas virtuales a partir de esa imagen personalizada.

En este artículo, aprenderá a:

* [Crear una imagen personalizada a partir de un archivo VHD](#create-a-custom-image-from-a-vhd-file) para poder crear luego una máquina virtual con esa imagen personalizada. 
* [Crear una imagen personalizada a partir de una máquina virtual](#create-a-custom-image-from-a-vm) para la clonación rápida de máquinas virtuales.

## <a name="create-a-custom-image-from-a-vhd-file"></a>Crear una imagen personalizada a partir de un archivo VHD
En esta sección, aprenderá a crear una imagen personalizada a partir de un archivo VHD.
Necesitará acceso a un archivo VHD válido para realizar todos los pasos de esta sección.   

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
3. En la lista de laboratorios, seleccione el laboratorio que desee.  
4. En la hoja del laboratorio, seleccione **Configuración**. 
5. En la hoja **Configuración** del laboratorio, seleccione **Imágenes personalizadas**.
6. En la hoja **Imágenes personalizadas**, seleccione **+ Custom Image** (+Imagen personalizada).
   
    ![Adición de imágenes personalizadas](./media/devtest-lab-create-template/add-custom-image.png)
7. Escriba el nombre de la imagen personalizada. Este nombre se muestra en la lista de imágenes base al crear una máquina virtual.
8. Escriba la descripción de la imagen personalizada. Esta descripción se muestra en la lista de imágenes base al crear una máquina virtual.
9. Seleccione el **archivo VHD**.
10. Si tiene acceso a un archivo VHD que no aparece, agréguelo siguiendo las instrucciones de la sección [Carga de un archivo VHD](#upload-a-vhd-file) y vuelva aquí cuando termine.
11. Seleccione el archivo VHD deseado.
12. Seleccione **Aceptar** para cerrar la hoja **Archivo VHD**.
13. Seleccione la **configuración del sistema operativo**.
14. En la pestaña **Configuración del sistema operativo**, seleccione **Windows** o **Linux**.
15. Si se selecciona **Windows** , especifique mediante la casilla si se ha ejecutado *Sysprep* en la máquina.
16. Seleccione **Aceptar** para cerrar la hoja **Configuración del sistema operativo**.
17. Seleccione **Aceptar** para crear la imagen personalizada.
18. Vaya a la sección [Pasos siguientes](#next-steps) .

### <a name="upload-a-vhd-file"></a>Carga de un archivo VHD
Para agregar una imagen personalizada, debe tener acceso a un archivo VHD.

1. En la hoja **Archivo VHD**, seleccione **Upload a VHD file using PowerShell** (Cargar un archivo VHD con PowerShell).
   
    ![Cargar imagen](./media/devtest-lab-create-template/upload-image-using-psh.png)
2. En la siguiente hoja se muestran instrucciones para modificar y ejecutar un script de PowerShell que carga un archivo VHD en su suscripción de Azure. 
   **Nota:** Este proceso puede durar bastante tiempo, en función del tamaño del archivo VHD y de la velocidad de conexión.

## <a name="create-a-custom-image-from-a-vm"></a>Crear una imagen personalizada a partir de una máquina virtual
Si tiene una máquina virtual que ya está configurada, puede crear una imagen personalizada a partir de ella y después usar esa imagen personalizada para crear otras máquinas virtuales idénticas. Los siguientes pasos muestran cómo crear una imagen personalizada desde una máquina virtual:

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
3. En la lista de laboratorios, seleccione el laboratorio que desee.  
4. En la hoja del laboratorio, seleccione **Mis máquinas virtuales**.
5. En la hoja **Mis máquinas virtuales** , seleccione la máquina virtual desde la que quiere crear la imagen personalizada.
6. En la hoja de la máquina virtual, seleccione **Create custom image (VHD)**(Crear imagen personalizada [VHD]).
   
    ![Crear elemento de menú de imagen personalizada](./media/devtest-lab-create-template/create-custom-image.png)
7. En la hoja **Create image** (Crear imagen), escriba un nombre y una descripción para la imagen personalizada. Esta información se muestra en la lista de bases cuando se crea una máquina virtual.
   
    ![Crear imagen personalizada](./media/devtest-lab-create-template/create-custom-image-blade.png)
8. Seleccione si sysprep se ha ejecutado en la máquina virtual. Si no se ha ejecutado sysprep en la máquina virtual, especifique si quiere ejecutar sysprep cuando se cree una máquina virtual a partir de esta imagen personalizada.
9. Cuando termine de crear la imagen personalizada, seleccione **Aceptar** .

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Entradas blogs relacionadas
* [Custom images or formulas? (¿Imágenes personalizadas o fórmulas?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
* [Copying Custom Images between Azure DevTest Labs (Copiar imágenes personalizadas entre instancias de Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Pasos siguientes
Cuando se haya agregado una imagen personalizada para usarla al crear una máquina virtual, el siguiente paso consiste en [agregar una máquina virtual a su laboratorio](devtest-lab-add-vm-with-artifacts.md).




<!--HONumber=Nov16_HO3-->


