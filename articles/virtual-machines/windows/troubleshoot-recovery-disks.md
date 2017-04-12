---

title: "Use a Windows troubleshooting VM with Azure PowerShell (Uso de una máquina virtual Windows de solución de problemas con Azure PowerShell) | Microsoft Docs"
description: "Aprenda a solucionar problemas de la máquina virtual Windows en Azure mediante la conexión del disco del sistema operativo a una máquina virtual de recuperación mediante Azure PowerShell."
services: virtual-machines-windows
documentationCenter: 
authors: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/13/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 50e9982fbf33a39b69effed193a4bd137c07a14d
ms.lasthandoff: 03/31/2017


---

# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Solución de problemas de una máquina virtual Windows mediante la conexión del disco del sistema operativo a una máquina virtual de recuperación mediante Azure PowerShell
Si la máquina virtual Windows de Azure se encuentra un error de disco o de arranque, deberá realizar los pasos para solucionar problemas en el propio disco duro virtual. Un ejemplo habitual sería una actualización de aplicación con error que impide que la máquina virtual se pueda arrancar correctamente. En este artículo se detalla cómo utilizar Azure PowerShell para conectar el disco duro virtual a otra máquina virtual Windows para solucionar los errores y, posteriormente, volver a crear la máquina virtual original.


## <a name="recovery-process-overview"></a>Introducción al proceso de recuperación
El proceso de solución de problemas es el siguiente:

1. Elimine la máquina virtual que tiene problemas, conservando los discos duros virtuales.
2. Conecte y monte el disco duro virtual en otra máquina virtual Windows con el fin de solucionar problemas.
3. Conéctese a la máquina virtual de solución de problemas. Edite los archivos o ejecute cualquier herramienta necesaria para solucionar los problemas del disco duro virtual original.
4. Desmonte y desconecte el disco duro virtual de la máquina virtual de solución de problemas.
5. Cree una máquina virtual mediante el disco duro virtual original.

Asegúrese de que tiene [la Azure PowerShell más reciente](/powershell/azureps-cmdlets-docs) instalada y con la sesión iniciada en su suscripción:

```powershell
Login-AzureRMAccount
```

En los ejemplos siguientes, reemplace los nombres de parámetros por los suyos propios. Los nombres de parámetros de ejemplo incluyen `myResourceGroup`, `mystorageaccount` y `myVM`.


## <a name="determine-boot-issues"></a>Determinación de los problemas de arranque
Puede ver una captura de pantalla de la máquina virtual de Azure para ayudar a solucionar problemas de arranque. Esta captura de pantalla puede ayudar a identificar por qué no arranca correctamente una máquina virtual. En el ejemplo siguiente se obtiene la captura de pantalla de la máquina virtual Windows llamada `myVM` en el grupo de recursos `myResourceGroup`:

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Revise la captura de pantalla para determinar por qué la máquina virtual no arranca correctamente. Tenga en cuenta todos los mensajes de error o códigos de error específicos proporcionados.


## <a name="view-existing-virtual-hard-disk-details"></a>Visualización de los detalles del disco duro virtual existente
Antes de poder conectar el disco duro virtual a otra máquina virtual, debe identificar el nombre del disco duro virtual (VHD).

En el ejemplo siguiente se obtiene información de la máquina virtual llamada `myVM` en el grupo de recursos `myResourceGroup`:

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Busque `Vhd URI` en la sección `StorageProfile` de la salida del comando anterior. La siguiente salida de ejemplo truncada muestra `Vhd URI` hacia el final del bloque de código:

```powershell
RequestId                     : 8a134642-2f01-4e08-bb12-d89b5b81a0a0
StatusCode                    : OK
ResourceGroupName             : myResourceGroup
Id                            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
Name                          : myVM
Type                          : Microsoft.Compute/virtualMachines
...
StorageProfile                :
  ImageReference              :
    Publisher                 : MicrosoftWindowsServer
    Offer                     : WindowsServer
    Sku                       : 2016-Datacenter
    Version                   : latest
  OsDisk                      :
    OsType                    : Windows
    Name                      : myVM
    Vhd                       :
      Uri                     : https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
    Caching                   : ReadWrite
    CreateOption              : FromImage
```


## <a name="delete-existing-vm"></a>Eliminación de la VM existente
Los discos duros virtuales y las máquinas virtuales son dos recursos diferentes de Azure. Un disco duro virtual es el recurso donde se almacenan el propio sistema operativo, las aplicaciones y las configuraciones. La propia máquina virtual consiste solo en metadatos que definen el tamaño o la ubicación y hace referencia a recursos como un disco duro virtual o una tarjeta de interfaz de red virtual (NIC). Cada disco duro virtual tiene una concesión que se asigna cuando se conecta a una máquina virtual. Aunque los discos de datos se pueden conectar y desconectar incluso mientras se está ejecutando la máquina virtual, no se puede desasociar el disco del sistema operativo, a menos que se elimine el recurso de máquina virtual. La concesión continúa para asociar el disco del sistema operativo a una máquina virtual incluso cuando esa máquina virtual está en un estado detenido o desasignado.

El primer paso para recuperar la máquina virtual es eliminar el propio recurso de máquina virtual. Al eliminar la máquina virtual, los discos duros virtuales se dejan en su cuenta de almacenamiento. Después de eliminar la máquina virtual, conecte el disco duro virtual a otra máquina virtual para localizar y solucionar los errores.

En el ejemplo siguiente se elimina la máquina virtual llamada `myVM` del grupo de recursos `myResourceGroup`:

```powershell
Remove-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Espere hasta que la máquina virtual haya terminado la eliminación antes de conectar el disco duro virtual a otra máquina virtual. La concesión en el disco duro virtual que lo asocia a la máquina virtual debe liberarse antes de poder conectar el disco duro virtual a otra máquina virtual.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Conexión del disco duro virtual existente a otra máquina virtual
Para los pasos siguientes, se usa otra máquina virtual con el fin de solucionar problemas. Conecte el disco duro virtual existente a esta máquina virtual de solución de problemas para examinar y modificar el contenido del disco. Por ejemplo, este proceso le permite corregir todos los errores de configuración o revisar archivos de registro adicionales de la aplicación o sistema. Elija o cree otra máquina virtual que se usará con fines de solución de problemas.

Cuando conecte el disco duro virtual existente, especifique la dirección URL en el disco obtenido en el comando `Get-AzureRmVM` anterior. En el ejemplo siguiente se conecta un disco duro virtual existente a la máquina virtual de solución de problemas con el nombre `myVMRecovery` en el grupo de recursos denominado `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
Add-AzureRmVMDataDisk -VM $myVM -CreateOption "Attach" -Name "DataDisk" -DiskSizeInGB $null `
    -VhdUri "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

> [!NOTE]
> Para agregar un disco, es necesario que especifique su tamaño. A medida que conectamos un disco existente, `-DiskSizeInGB` se especifica como `$null`. Este valor garantiza la correcta conexión del disco de datos, sin necesidad de determinar el tamaño real del disco de datos.


## <a name="mount-the-attached-data-disk"></a>Montaje del disco de datos conectado

1. RDP en la máquina virtual de solución de problemas con las credenciales apropiadas. En el siguiente ejemplo se descarga el archivo de conexión RDP de la máquina virtual `myVMRecovery` del grupo de recursos `myResourceGroup`, y lo hace en `C:\Users\ops\Documents`"

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "myVMRecovery" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. El disco de datos se detecta y conecta automáticamente. Consulte la lista de volúmenes conectados para determinar la letra de unidad de la manera siguiente:

    ```powershell
    Get-Disk
    ```

    La siguiente salida de ejemplo muestra el disco duro virtual conectado a un disco **2** (también puede usar `Get-Volume` para ver la letra de unidad):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Online       127 GB MBR
    ```

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Solución de problemas en el disco duro virtual original
Con el disco duro virtual existente montado, ahora puede realizar todos los pasos de mantenimiento y solución de problemas según sea necesario. Una vez que se han resuelto los problemas, continúe con los pasos siguientes.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Desmontaje y desconexión del disco duro virtual original
Una vez resueltos los errores, desmonte y desconecte el disco duro virtual existente de la máquina virtual de solución de problemas. No se podrá usar el disco duro virtual en ninguna otra máquina virtual hasta que se libere la concesión que conecta el disco duro virtual a la máquina virtual de solución de problemas.

1. Desde dentro de la sesión RDP, desmonte el disco de datos de la máquina virtual de recuperación. Necesita el número de disco del cmdlet `Get-Disk` anterior. A continuación, use `Set-Disk` para establecer el disco como sin conexión:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Confirme que el disco está actualmente establecido como sin conexión de nuevo con `Get-Disk`. La siguiente salida de ejemplo muestra que el disco está actualmente establecido como sin conexión:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Salga de la sesión RDP. En la sesión de Azure PowerShell, quite el disco duro virtual de la máquina virtual de solución de problemas.

    ```powershell
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "DataDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```


## <a name="create-vm-from-original-hard-disk"></a>Creación de máquina virtual a partir del disco duro original
Para crear una máquina virtual a partir del disco duro virtual original, utilice [esta plantilla de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). La plantilla JSON real está en el siguiente vínculo:

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json

La plantilla implementa una máquina virtual en una red virtual existente mediante la dirección URL del disco duro virtual del comando anterior. En el ejemplo siguiente se implementa la plantilla en el grupo de recursos denominado `myResourceGroup`:

```powershell
New-AzureRmResourceGroupDeployment -Name myDeployment -ResourceGroupName myResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json
```

Responda a las indicaciones de la plantilla como, por ejemplo, las relacionadas con el nombre de la máquina virtual, el tipo de sistema operativo y el tamaño de la máquina virtual. `osDiskVhdUri` es el mismo que se usó anteriormente al conectar el disco duro virtual existente a la máquina virtual de solución de problemas.


## <a name="re-enable-boot-diagnostics"></a>Rehabilitación de los diagnósticos de arranque

Cuando se crea la máquina virtual desde el disco duro virtual existente, puede que no se habilite automáticamente el diagnóstico de arranque. En el ejemplo siguiente se habilita la extensión de diagnóstico en la máquina virtual denominada `myVMDeployed` en el grupo de recursos `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Pasos siguientes
Si tiene problemas para conectarse a la máquina virtual, consulte [Solución de problemas de conexiones RDP a una máquina virtual de Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para problemas con el acceso a aplicaciones que se ejecutan en su máquina virtual, consulte [Solucionar problemas de conectividad de aplicaciones en una máquina virtual Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para más información sobre el uso de Resource Manager, consulte [Información general de Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
