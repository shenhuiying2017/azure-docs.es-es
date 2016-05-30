<properties
	pageTitle="Creación de una imagen personalizada de DevTest Labs desde un archivo de VHD | Microsoft Azure"
	description="Aprenda a crear una imagen personalizada desde un archivo de VHD, que puede utilizarse para crear VM en DevTest Labs"
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
	ms.date="05/08/2016"
	ms.author="tarcher"/>

# Creación de una imagen personalizada de DevTest Labs desde un archivo de VHD

## Información general

Una vez que haya [creado un laboratorio](devtest-lab-create-lab.md), puede [agregar máquinas virtuales a ese laboratorio](devtest-lab-add-vm-with-artifacts.md). Cuando cree una VM, deberá especificar una *base*, que puede ser una *imagen personalizada* o una *imagen de Marketplace*. En este artículo, aprenderá a crear una imagen personalizada desde un archivo VHD. Tenga en cuenta que necesitará acceso a un archivo VHD válido para realizar todos los pasos de este artículo.

## Creación de una imagen personalizada

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Pulse **Examinar** y luego pulse **Laboratorios de desarrollo y pruebas** en la lista.

1. En la lista de laboratorios, pulse el laboratorio que desee.

1. Se mostrará la hoja **Configuración** del laboratorio seleccionado.

1. En la hoja **Configuración** del laboratorio, pulse **Imágenes personalizadas**.

    ![Opción de imágenes personalizadas](./media/devtest-lab-create-template/lab-settings-custom-images.png)

1. En la hoja **Imágenes personalizadas**, pulse **+ Custom Image** (+ Imagen personalizada).

    ![Adición de imágenes personalizadas](./media/devtest-lab-create-template/add-custom-image.png)

1. Escriba el nombre de la imagen personalizada. Este nombre se muestra en la lista de imágenes base al crear una nueva máquina virtual.

1. Escriba la descripción de la imagen personalizada. Esta descripción se muestra en la lista de imágenes base al crear una nueva máquina virtual.

1. Pulse **Archivo VHD**.

1. Si tiene acceso a un archivo VHD que no aparece, agréguelo siguiendo las instrucciones en la sección [Carga de un archivo VHD](#upload-a-vhd-file) y vuelva aquí cuando termine.

1. Seleccione el archivo VHD deseado.

1. Pulse **Aceptar** para cerrar la hoja **Archivo VHD**.

1. Pulse en **Configuración del sistema operativo**.

1. En la pestaña **Configuración del sistema operativo**, seleccione **Windows** o **Linux**.

1. Si se selecciona **Windows**, especifique a través de la casilla si se ha ejecutado *Sysprep* en el equipo.

1. Pulse en **Aceptar** para cerrar la hoja **Configuración del sistema operativo**.

1. Pulse **Aceptar** para crear la imagen personalizada.

1. Vaya a la sección [Pasos siguientes](#next-steps).

##Carga de un archivo VHD

Para agregar una nueva imagen personalizada, deberá tener acceso a un archivo VHD.

1. En la hoja **Archivo VHD**, pulse **Cargar un archivo VHD con PowerShell**.

    ![Cargar imagen](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. En la siguiente hoja se muestran instrucciones para modificar y ejecutar un script de PowerShell que carga en su suscripción de Azure un archivo VHD. **Nota:** este proceso puede durar bastante tiempo, en función del tamaño del archivo VHD y de la velocidad de conexión.

##Pasos siguientes

Una vez que haya agregado una imagen personalizada para usarla al crear una máquina virtual, el siguiente paso consiste en [agregar una máquina virtual a su laboratorio](./devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0518_2016-->