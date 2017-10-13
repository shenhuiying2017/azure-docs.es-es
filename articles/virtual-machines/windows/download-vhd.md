---
title: Descargar un VHD desde Azure | Microsoft Docs
description: Descargue un VHD de Windows mediante Azure Portal.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: davidmu
ms.openlocfilehash: d8bf89a4b7c2a158302f9ba09a182a3d8d062adc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="download-a-windows-vhd-from-azure"></a>Descargar un VHD de Windows desde Azure

En este artículo aprenderá a descargar un archivo de [disco duro virtual (VHD) de Windows](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) desde Azure mediante Azure Portal. 

Las máquinas virtuales (VM) de Azure usan los [discos](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) como la ubicación en la que almacenar un sistema operativo, aplicaciones y datos. Todas las máquinas virtuales de Azure tienen al menos dos discos: un disco de sistema operativo Windows y un disco temporal. El disco de sistema operativo se crea inicialmente a partir de una imagen, y tanto el disco de sistema operativo como la imagen son VHD almacenados en una cuenta de Azure Storage. Las máquinas virtuales también pueden tener uno o más discos de datos que también se almacenan en discos duros virtuales.

## <a name="stop-the-vm"></a>Parada de la máquina virtual

No se puede descargar un VHD desde Azure si está conectado a una máquina virtual en ejecución. Debe detener la máquina virtual para descargar un VHD. Si quiere usar un VHD como [imagen](tutorial-custom-images.md) para crear otras máquinas virtuales con discos nuevos, use [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) para generalizar el sistema operativo incluido en el archivo y luego detenga la máquina virtual. Para usar el VHD como disco para una nueva instancia de una máquina virtual o un disco de datos existente, basta con detener y cancelar la asignación de la máquina virtual.

Para usar el VHD como imagen para crear otras máquinas virtuales, siga estos pasos:

1.  Si aún no lo ha hecho, inicie sesión en el [Azure Portal](https://portal.azure.com/).
2.  [Conecte a la máquina virtual](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  En la máquina virtual, abra la ventana del símbolo del sistema como administrador.
4.  Cambie el directorio a *%windir%\system32\sysprep* y ejecute sysprep.exe.
5.  En el cuadro de diálogo Herramienta de preparación del sistema, seleccione **Iniciar la configuración rápida (OOBE) del sistema** y asegúrese de que **Generalizar** está activado.
6.  En Opciones de apagado, seleccione **Apagar** y luego haga clic en **Aceptar**. 

Para usar el VHD como disco para una nueva instancia de una máquina virtual o un disco de datos existente, realice estos pasos:

1.  En el menú Concentrador de Azure Portal, haga clic en **Máquinas virtuales**.
2.  Seleccione la máquina virtual en la lista.
3.  En la hoja de la máquina virtual, haga clic en **Detener**.

    ![Detención de la máquina virtual](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generación de dirección URL de SAS

Para descargar el archivo de VHD, debe generar una dirección URL de [firma de acceso compartido (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Una vez generada la dirección URL, se asigna un tiempo de expiración a la dirección URL.

1.  En el menú de la hoja de la máquina virtual, haga clic en **Discos**.
2.  Seleccione el disco de sistema operativo de la máquina virtual y luego haga clic en **Exportar**.
3.  Establezca el tiempo de expiración de la dirección URL en *36000*.
4.  Haga clic en **Generar dirección URL**.

    ![Generar dirección URL](./media/download-vhd/export-generate.png)

> [!NOTE]
> El tiempo de expiración se aumenta con respecto al valor predeterminado para proporcionar tiempo suficiente para descargar el archivo de VHD grande de un sistema operativo Windows Server. Es de esperar que un archivo de VHD que contenga el sistema operativo Windows Server tarde varias horas en descargarse según la conexión. Si va a descargar un VHD de un disco de datos, el tiempo predeterminado es suficiente. 
> 
> 

## <a name="download-vhd"></a>Descargar VHD

1.  En la dirección URL generada, haga clic en Descargar el archivo VHD.

    ![Descarga de VHD](./media/download-vhd/export-download.png)

2.  Es posible que tenga que hacer clic en **Guardar** en el explorador para iniciar la descarga. El nombre predeterminado del archivo de VHD es *abcd*.

    ![Haga clic en Guardar en el explorador](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre cómo [cargar un archivo de VHD en Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Create managed disks from unmanaged disks in a storage account](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Creación de discos administrados a partir de discos no administrados en una cuenta de almacenamiento).
- [Manage Azure disks with PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Administrar discos de Azure con PowerShell).

