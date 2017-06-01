---
title: "Captura de una imagen de máquina virtual de Azure generalizada | Microsoft Docs"
description: "Aprenda a capturar la imagen de una máquina virtual de Azure generalizada creada en el modelo de implementación de Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: afdae4a1-6dfb-47b4-902a-f327f9bfe5b4
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 8f1d488fd8f71bf90c8bf7b7c1544445ffbd7686
ms.contentlocale: es-es
ms.lasthandoff: 05/26/2017


---
# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>Captura de la imagen de una máquina virtual de Azure generalizada
En este artículo se muestra cómo usar Azure PowerShell para crear una imagen de una máquina virtual de Azure generalizada. Después, puede usar la imagen para crear otra máquina virtual. Dicha imagen incluye el disco del SO y los discos de datos conectados a la máquina virtual. La imagen no incluye los recursos de red virtual, por lo que tiene que es preciso configurarlos al crear la máquina virtual nueva. 

## <a name="prerequisites"></a>Requisitos previos
Es preciso tener instalada la versión 1.0.x de Azure PowerShell, o cualquier versión posterior. Si aún no ha instalado PowerShell, lea [Cómo instalar y configurar Azure PowerShell](/powershell/azure/overview) para ver los pasos de instalación.

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalización de VM con Windows mediante Sysprep

Entre otras características, Sysprep elimina toda la información personal de la cuenta y prepara, entre otras cosas, la máquina para usarse como imagen. Para obtener más información sobre Sysprep, vea [Uso de Sysprep: Introducción](http://technet.microsoft.com/library/bb457073.aspx).

Asegúrese de que los roles de servidor que se ejecutan en la máquina sean compatibles con Sysprep. Para más información, consulte [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Si ejecuta Sysprep antes de cargar el VHD en Azure por primera vez, asegúrese de que tiene [preparada la máquina virtual](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de ejecutar Sysprep. 
> 
> 

1. Inicie sesión en la máquina virtual de Windows.
2. Abra una ventana del símbolo del sistema como administrador. Cambie el directorio a **%windir%\system32\sysprep** y, después, ejecute `sysprep.exe`.
3. En **Herramienta de preparación del sistema**, seleccione **Iniciar la Configuración rápida (OOBE)** y asegúrese de que la casilla **Generalizar** está activada.
4. En **Opciones de apagado**, seleccione **Apagar**.
5. Haga clic en **Aceptar**.
   
    ![Iniciar Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Cuando Sysprep finaliza, apaga la máquina virtual. No reinicie la VM.


También puede generalizar una máquina virtual Linux con `sudo waagent -deprovision+user` y, luego, usar PowerShell para capturar la máquina virtual. Para obtener información sobre cómo usar la CLI para capturar una máquina virtual, consulte el artículo sobre [cómo generalizar y capturar una máquina virtual Linux mediante la CLI de Azure](../linux/capture-image.md).

## <a name="log-in-to-azure-powershell"></a>Iniciar sesión en Azure PowerShell
1. Abra Azure PowerShell e inicie sesión en la cuenta de Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
   
    Se abrirá una ventana emergente para que escriba sus credenciales de cuenta de Azure.
2. Obtenga los identificadores de suscripción para las suscripciones disponibles.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Establezca la suscripción correcta con el identificador de suscripción.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Desasignar la máquina virtual y establecer el estado en generalizado
1. Desasigne los recursos de máquina virtual.
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    El *estado* de la máquina virtual en Azure Portal cambia de **Detenido** a **Detenido (desasignado)**.
2. Establezca el estado de la máquina virtual en **Generalizado**. 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Compruebe el estado de la máquina virtual. En la sección **OSState/generalized** de la máquina virtual, **DisplayStatus** debe estar establecido en **VM generalized** (Máquina virtual generalizada).  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Crear la imagen
1. Copie la imagen de máquina virtual en el contenedor de almacenamiento de destino con este comando. La imagen se crea en la misma cuenta de almacenamiento que la de la máquina virtual original. El parámetro `-Path` guarda una copia local de la plantilla JSON. El parámetro `-DestinationContainerName` es el nombre del contenedor que desea que contenga las imágenes. Si el contenedor no existe, se creará.
   
    ```powershell
    Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
        -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
        -Path <C:\local\Filepath\Filename.json>
    ```
   
    Puede obtener la dirección URL de la imagen de la plantilla del archivo JSON. Vaya a la sección **recursos** > **storageProfile** > **osDisk** > **imagen** > **uri** para ver la ruta de acceso completa de la imagen. La dirección URL de la imagen es similar a esta: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
    También puede comprobar el URI en el portal. La imagen se copia en un contenedor denominado **system** de su cuenta de almacenamiento. 

## <a name="next-steps"></a>Pasos siguientes
* Ya puede [crear una máquina virtual a partir de la imagen](create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


