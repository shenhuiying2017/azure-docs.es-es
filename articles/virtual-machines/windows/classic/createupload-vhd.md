---
title: "Creación y carga de una imagen de máquina virtual mediante Powershell | Microsoft Docs"
description: "Aprenda a crear y cargar una imagen de Windows Server generalizada (VHD) mediante el modelo de implementación clásica y Azure Powershell."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 8c4a08fe-7714-4bf0-be87-c728a7806d3f
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ms.openlocfilehash: bc75c8cdd98b0ea0fbff6483c0e3c9d4468d3941
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="create-and-upload-a-windows-server-vhd-to-azure"></a>Crear y cargar un VHD de Windows Server a Azure
En este artículo se muestra cómo puede cargar su propia imagen de VM generalizada como un disco duro virtual (VHD) que podrá utilizar para crear máquinas virtuales. Para más información sobre discos y VHD en Microsoft Azure, consulte [Acerca de los discos y los discos duros virtuales para máquinas virtuales](../about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. También puede [cargar](../upload-generalized-managed.md) una máquina virtual mediante el modelo Resource Manager.

## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que ya dispones de:

* **Una suscripción a Azure** : si no tiene una, puede [abrir una cuenta de Azure gratis](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
* **[Microsoft Azure PowerShell](/powershell/azure/overview)**: tiene el módulo Microsoft Azure PowerShell instalado y configurado para usar su suscripción.
* **Un archivo .VHD** : sistema operativo Windows compatible almacenado en un archivo .vhd y conectado a una máquina virtual. Compruebe si los roles de servidor que se ejecutan en el VHD son compatibles con Sysprep. Para más información, consulte [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)(Compatibilidad de Sysprep con roles de servidor).

    > [!IMPORTANT]
    > El formato VHDX no se admite en Microsoft Azure. Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el [cmdlet Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). Para obtener más información, consulta esta [publicación de blog](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## <a name="step-1-prep-the-vhd"></a>Paso 1: Preparar el VHD
Antes de cargar el VHD en Azure, tiene que generalizarse mediante la herramienta Sysprep. Esto prepara el VHD para usarlo como una imagen. Para obtener más información sobre Sysprep, vea [Uso de Sysprep: Introducción](http://technet.microsoft.com/library/bb457073.aspx). Cree una copia de seguridad de la VM antes de ejecutar Sysprep.

Desde la máquina virtual en la que se instaló el sistema operativo, realice el procedimiento siguiente:

1. Inicie sesión en el sistema operativo.
2. Abra una ventana del símbolo del sistema como administrador. Cambie el directorio a **%windir%\system32\sysprep** y, después, ejecute `sysprep.exe`.

    ![Abrir una ventana de símbolo del sistema](./media/createupload-vhd/sysprep_commandprompt.png)
3. Aparecerá el cuadro de diálogo **Herramienta de preparación del sistema** .

   ![Iniciar Sysprep](./media/createupload-vhd/sysprepgeneral.png)
4. En la **Herramienta de preparación del sistema**, seleccione **Iniciar la configuración rápida (OOBE) del sistema** y asegúrese de que la casilla **Generalizar** está seleccionada.
5. En **Opciones de apagado**, seleccione **Apagar**.
6. Haga clic en **Aceptar**.

## <a name="step-2-create-a-storage-account-and-a-container"></a>Paso 2: Crear una cuenta de almacenamiento y un contenedor
Necesitas una cuenta de almacenamiento de Azure para tener un sitio para cargar el archivo .vhd. Este paso te muestra cómo crear una cuenta u obtener la información que necesitas de una cuenta existente. Reemplace las variables entre &lsaquo; corchetes &rsaquo; por su propia información.

1. Inicio de sesión

    ```powershell
    Add-AzureAccount
    ```

2. Establezca su suscripción a Azure.

    ```powershell
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

3. Cree una cuenta de almacenamiento nueva. El nombre de la cuenta de almacenamiento debe ser único y debe tener entre 3 y 24 caracteres. El nombre puede ser cualquier combinación de letras y números. También necesita especificar una ubicación como "Este de EE. UU. "

    ```powershell
    New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>
    ```

4. Establezca la nueva cuenta de almacenamiento como la predeterminada.

    ```powershell
    Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>
    ```

5. Cree un contenedor nuevo.

    ```powershell
    New-AzureStorageContainer -Name <ContainerName> -Permission Off
    ```

## <a name="step-3-upload-the-vhd-file"></a>Paso 3: Cargar el archivo .vhd
Use [Add-AzureVhd](https://docs.microsoft.com/en-us/powershell/module/azure/add-azurevhd) para cargar el VHD.

Desde la ventana de Azure PowerShell que usó en el paso anterior, escriba el siguiente comando y reemplace las variables entre &lsaquo; corchetes &rsaquo; por su propia información.

```powershell
Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>
```

## <a name="step-4-add-the-image-to-your-list-of-custom-images"></a>Paso 4: Agregar la imagen a la lista de imágenes personalizadas
Use el cmdlet [Add-AzureVMImage](https://docs.microsoft.com/en-us/powershell/module/azure/add-azurevmimage) para agregar la imagen a la lista de imágenes personalizadas.

```powershell
Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"
```

## <a name="next-steps"></a>Pasos siguientes
Ahora puede [crear una máquina virtual personalizada](createportal.md) mediante la imagen que cargó.
