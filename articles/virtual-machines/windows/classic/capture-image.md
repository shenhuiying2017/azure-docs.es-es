---
title: "Captura de una imagen de una máquina virtual Windows de Azure | Microsoft Docs"
description: "Capture una imagen de una máquina virtual Windows de Azure creada con el modelo de implementación clásica."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: a5986eac-4cf3-40bd-9b79-7c811806b880
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 6032263848c469ce2f416306e5c91c29f4cb30e4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Capture una imagen de una máquina virtual Windows de Azure creada con el modelo de implementación clásica.
> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Para obtener información del modelo de Resource Manager, vea [Capturar una imagen administrada de una máquina virtual generalizada en Azure](../capture-image-resource.md).

En este artículo se muestra cómo puede capturar una máquina virtual de Azure con Windows para usarla como imagen en la creación de otras máquinas virtuales. Esta imagen incluye el disco del sistema operativo y los discos de datos que están conectados a la máquina virtual. No incluye configuraciones de red, por lo que deberá establecerlas cuando cree las otras máquinas virtuales que usan la imagen.

Azure almacena la imagen en **Imágenes de la VM (clásico)**, un servicio de **proceso** que se muestra cuando aparecen todos los servicios de Azure. Este es el mismo lugar donde se almacenan las imágenes que se han cargado. Para obtener más información acerca de las imágenes, vea [Acerca de las imágenes para las máquinas virtuales](about-images.md?toc=%2fazure%2fvirtual-machines%2fWindows%2fclassic%2ftoc.json).

## <a name="before-you-begin"></a>Antes de empezar
Para seguir estos pasos se supone que ya ha creado un máquina virtual Azure y ha configurado el sistema operativo, incluida la anexión de cualquier disco de datos. Si no lo ha hecho todavía, consulte los siguientes artículos para obtener información sobre la creación y preparación de la máquina virtual:

* [Cree una máquina virtual desde una imagen](createportal.md)
* [Acoplamiento de un disco de datos a una máquina virtual](attach-disk.md)
* Asegúrese de que los roles de servidor admiten Sysprep. Para más información, consulte [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)(Compatibilidad de Sysprep con roles de servidor).

> [!WARNING]
> Este proceso elimina la máquina virtual original después de capturarla.
>
>

Antes de capturar una imagen de una máquina virtual de Azure, se recomienda hacer una copia de seguridad de la máquina virtual de destino. Esto se puede hacer mediante Copia de seguridad de Azure. Para obtener más información, consulte [Copia de seguridad de máquinas virtuales de Azure](../../../backup/backup-azure-vms.md). Hay otras soluciones disponibles de socios certificados. Para averiguar lo que está actualmente disponible, busque Azure Marketplace.

## <a name="capture-the-virtual-machine"></a>Captura de la máquina virtual
1. En [Azure Portal](http://portal.azure.com), **conecte** con la máquina virtual. Para obtener instrucciones, consulte [Inicio de sesión en una máquina virtual con Windows Server][How to sign in to a virtual machine running Windows Server].
2. Abra una ventana de símbolo del sistema como administrador.
3. Cambie el directorio a `%windir%\system32\sysprep`y después ejecute sysprep.exe.
4. Aparecerá el cuadro de diálogo **Herramienta de preparación del sistema** . Haga lo siguiente:

   * En **Acción de limpieza del sistema**, seleccione **Iniciar la configuración rápida (OOBE) del sistema**  y asegúrese de que la opción **Generalizar** está marcada. Para obtener más información sobre el uso de Sysprep, consulte [Uso de Sysprep: Introducción][How to Use Sysprep: An Introduction].
   * En **Opciones de apagado**, seleccione **Apagar**.
   * Haga clic en **Aceptar**.

   ![Ejecute Sysprep](./media/capture-image/SysprepGeneral.png)
5. Sysprep apaga la máquina virtual, lo que cambia su estado en Azure Portal a **Detenido**.
6. En Azure Portal, haga clic en **Máquinas virtuales (clásico)** y seleccione las máquinas virtuales que desea capturar. El grupo **Imágenes de la VM (clásico)** aparece en **Proceso** en la vista de **Más servicios**.

7. En la barra de comandos, haga clic en **Capturar**.

   ![Capture la máquina virtual](./media/capture-image/CaptureVM.png)

   Aparece el cuadro de diálogo **Capturar la máquina virtual** .

8. En **Nombre de imagen**, escriba un nombre para la nueva imagen. En **Etiqueta de imagen**, escriba una etiqueta para la nueva imagen.

9. Haga clic en **I've run Sysprep on the virtual machine** (He ejecutado Sysprep en la máquina virtual). Esta casilla hace referencia a las acciones realizadas con Sysprep en los pasos 3 a 5. Una imagen _debe_ generalizarse con la ejecución de Sysprep antes de agregar una imagen de Windows Server al conjunto de imágenes personalizadas.

10. Una vez completada la captura, la nueva imagen está disponible en el contenedor **Marketplace**, **Proceso** e **Imágenes de la VM (clásico)**.

    ![Captura correcta de la imagen](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Pasos siguientes
La imagen está lista para usarse para crear máquinas virtuales. En este caso, necesita crear una máquina virtual; para ello, seleccione el elemento de menú **Más servicios** en la parte inferior del menú de servicios y luego seleccione **Imágenes de la VM (clásico)** en el grupo **Proceso**. Para obtener instrucciones, consulte [Creación de una máquina virtual desde una imagen](createportal.md).

[How to sign in to a virtual machine running Windows Server]:connect-logon.md
[How to Use Sysprep: An Introduction]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png
