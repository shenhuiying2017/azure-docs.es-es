<properties
	pageTitle="Administración de imágenes personalizadas de Azure DevTest Labs para crear máquinas virtuales | Microsoft Azure"
	description="Aprenda a crear una imagen personalizada desde un archivo VHD o desde una máquina virtual existente en Azure DevTest Labs"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="tarcher"/>

# Administración de imágenes personalizadas de Azure DevTest Labs para crear máquinas virtuales

Una vez que haya [creado un laboratorio](devtest-lab-create-lab.md), puede [agregar máquinas virtuales a ese laboratorio](devtest-lab-add-vm-with-artifacts.md). Cuando cree una VM, deberá especificar una *base*, que puede ser una *imagen personalizada* o una *imagen de Marketplace*. En este artículo, aprenderá a [crear una imagen personalizada desde una unidad de disco virtual](#create-a-custom-image-from-a-vhd) de modo que posteriormente pueda crear una máquina virtual a partir de esa imagen personalizada. Además, también puede [crear una imagen personalizada desde una máquina virtual](#create-a-custom-image-from-a-vm) para crear posteriormente máquinas virtuales de forma rápida.

## Creación de una imagen personalizada desde un archivo VHD

En esta sección, aprenderá a crear una imagen personalizada desde un archivo VHD. Tenga en cuenta que necesitará acceso a un archivo VHD válido para realizar todos los pasos de esta sección.


1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Examinar**, y, a continuación, seleccione **DevTest Labs** en la lista.

1. En la lista de laboratorios, seleccione el laboratorio que desee.

1. Se mostrará la hoja **Configuración** del laboratorio seleccionado.

1. En la hoja **Configuración** del laboratorio, seleccione **Imágenes personalizadas**.

    ![Opción de imágenes personalizadas](./media/devtest-lab-create-template/lab-settings-custom-images.png)

1. En la hoja **Imágenes personalizadas**, seleccione **+ Custom Image** (+Imagen personalizada).

    ![Adición de imágenes personalizadas](./media/devtest-lab-create-template/add-custom-image.png)

1. Escriba el nombre de la imagen personalizada. Este nombre se muestra en la lista de imágenes base al crear una nueva máquina virtual.

1. Escriba la descripción de la imagen personalizada. Esta descripción se muestra en la lista de imágenes base al crear una nueva máquina virtual.

1. Seleccione el **archivo VHD**.

1. Si tiene acceso a un archivo VHD que no aparece, agréguelo siguiendo las instrucciones de la sección [Carga de un archivo VHD](#upload-a-vhd-file) y vuelva aquí cuando termine.

1. Seleccione el archivo VHD deseado.

1. Seleccione **Aceptar** para cerrar la hoja **Archivo VHD**.

1. Seleccione la **configuración del sistema operativo**.

1. En la pestaña **Configuración del sistema operativo**, seleccione **Windows** o **Linux**.

1. Si se selecciona **Windows**, especifique a través de la casilla si se ha ejecutado *Sysprep* en el equipo.

1. Seleccione **Aceptar** para cerrar la hoja **onfiguración del sistema operativo**.

1. Seleccione **Aceptar** para crear la imagen personalizada.

1. Vaya a la sección [Pasos siguientes](#next-steps).

###Carga de un archivo VHD

Para agregar una nueva imagen personalizada, deberá tener acceso a un archivo VHD.

1. En la hoja **Archivo VHD**, seleccione **Upload a VHD file using PowerShell** (Cargar un archivo VHD con PowerShell).

    ![Cargar imagen](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. En la siguiente hoja se muestran instrucciones para modificar y ejecutar un script de PowerShell que carga un archivo VHD en su suscripción de Azure. **Nota:** Este proceso puede durar bastante tiempo, en función del tamaño del archivo VHD y de la velocidad de conexión.

## Creación de una imagen personalizada desde una máquina virtual
Si tiene una máquina virtual que ya está configurada, puede crear una imagen personalizada a partir de ella y después usar esa imagen personalizada para crear otras máquinas virtuales idénticas. Los siguientes pasos muestran cómo crear una imagen personalizada desde una máquina virtual:

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Examinar**, y, a continuación, seleccione **DevTest Labs** en la lista.

1. En la lista de laboratorios, seleccione el laboratorio que desee.

1. En la hoja **Información general** del laboratorio, seleccione la máquina virtual desde la que quiere crear la imagen personalizada.

1. En la hoja de la máquina virtual, seleccione **Create custom image (VHD)** (Crear imagen personalizada [VHD]).

	![Crear elemento de menú de imagen personalizada](./media/devtest-lab-create-template/create-custom-image.png)

1. En la hoja **Create image** (Crear imagen), escriba un nombre y una descripción para la imagen personalizada. Esta información se mostrará en la lista de bases cuando cree una máquina virtual.

	![Crear imagen personalizada](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Seleccione si sysprep se ha ejecutado en la máquina virtual. Si no se ha ejecutado sysprep en la máquina virtual, especifique si quiere ejecutar o no sysprep cuando se cree una máquina virtual a partir de esta imagen personalizada.

1. Cuando termine de crear la imagen personalizada, seleccione **Aceptar**.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Entradas blogs relacionadas

- [Custom images or formulas? (¿Imágenes personalizadas o fórmulas?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copying Custom Images between Azure DevTest Labs (Copiar imágenes personalizadas entre instancias de Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##Pasos siguientes

Cuando se haya agregado una imagen personalizada para usarla al crear una máquina virtual, el siguiente paso consiste en [agregar una máquina virtual a su laboratorio](./devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0831_2016-->