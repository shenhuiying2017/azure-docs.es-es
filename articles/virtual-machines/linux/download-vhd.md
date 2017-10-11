---
title: Descarga de un VHD de Linux desde Azure | Microsoft Docs
description: Descarga de un VHD de Linux mediante la CLI de Azure y Azure Portal.
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
ms.openlocfilehash: 3eb88478b43f8e3a36ae04bf3703f238e8cb1f3e
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="download-a-linux-vhd-from-azure"></a>Descarga de un VHD de Linux desde Azure

En este artículo aprenderá a descargar un archivo de [disco duro virtual (VHD) de Linux](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) desde Azure mediante la CLI de Azure y Azure Portal. 

Las máquinas virtuales (VM) de Azure usan los [discos](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) como la ubicación en la que almacenar un sistema operativo, aplicaciones y datos. Todas las máquinas virtuales de Azure tienen al menos dos discos: un disco de sistema operativo Windows y un disco temporal. El disco de sistema operativo se crea inicialmente a partir de una imagen, y tanto el disco de sistema operativo como la imagen son VHD almacenados en una cuenta de Azure Storage. Las máquinas virtuales también pueden tener uno o más discos de datos que también se almacenan en discos duros virtuales.

Si aún no lo ha hecho, instale la [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>Parada de la máquina virtual

No se puede descargar un VHD desde Azure si está conectado a una máquina virtual en ejecución. Debe detener la máquina virtual para descargar un VHD. Si quiere usar un VHD como [imagen](tutorial-custom-images.md) para crear otras máquinas virtuales con discos nuevos, debe desaprovisionar y generalizar el sistema operativo incluido en el archivo y detener la máquina virtual. Para usar el VHD como disco para una nueva instancia de una máquina virtual o un disco de datos existente, basta con detener y cancelar la asignación de la máquina virtual.

Para usar el VHD como imagen para crear otras máquinas virtuales, siga estos pasos:

1. Use SSH, el nombre de la cuenta y la dirección IP pública de la máquina virtual para conectarse a ella y desaprovisionarla. El parámetro +user también quita la última cuenta de usuario aprovisionada. Si está creando credenciales de cuenta en la máquina virtual, ignore este parámetro +user. En el ejemplo siguiente se quita la última cuenta de usuario aprovisionada:

    ```bash
    ssh azureuser@40.118.249.235
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Inicie sesión en su cuenta de Azure con [az login](https://docs.microsoft.com/cli/azure/#login).
3. Detenga y desasigne la máquina virtual.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Generalice la máquina virtual. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Para usar el VHD como disco para una nueva instancia de una máquina virtual o un disco de datos existente, realice estos pasos:

1.  Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2.  En el menú del concentrador, haga clic en **Máquinas virtuales**.
3.  Seleccione la máquina virtual en la lista.
4.  En la hoja de la máquina virtual, haga clic en **Detener**.

    ![Detención de la máquina virtual](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generación de dirección URL de SAS

Para descargar el archivo de VHD, debe generar una dirección URL de [firma de acceso compartido (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Una vez generada la dirección URL, se asigna un tiempo de expiración a la dirección URL.

1.  En el menú de la hoja de la máquina virtual, haga clic en **Discos**.
2.  Seleccione el disco de sistema operativo de la máquina virtual y luego haga clic en **Exportar**.
3.  Haga clic en **Generar dirección URL**.

    ![Generar dirección URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Descarga de VHD

1.  En la dirección URL generada, haga clic en Descargar el archivo VHD.

    ![Descarga de VHD](./media/download-vhd/export-download.png)

2.  Es posible que tenga que hacer clic en **Guardar** en el explorador para iniciar la descarga. El nombre predeterminado del archivo de VHD es *abcd*.

    ![Haga clic en Guardar en el explorador](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [cargar y crear una máquina virtual Linux a partir de un disco personalizado mediante la CLI de Azure 2.0](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Administración de discos de Azure con la CLI de Azure](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

