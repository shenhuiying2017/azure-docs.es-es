---
title: "Administración de discos de máquina virtual en Azure Stack | Microsoft Docs"
description: "Aprovisione discos de máquinas virtuales para Azure Stack."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/14/2017
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: 0c36e2eaaf2d266842b2b7de0b0c8dc0ed1e0145
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="virtual-machine-disk-storage-for-azure-stack"></a>Almacenamiento de discos de máquina virtual para Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Azure Stack admite el uso de [discos no administrados](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks) en una máquina virtual, que pueden ser tanto discos del sistema operativo (SO) como disco de datos. Para usar discos no administrados, creará una [cuenta de almacenamiento](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) y luego almacenará en ella los discos como blobs en páginas en contenedores. Estos discos se conocen entonces como discos de máquina virtual.

Para mejorar el rendimiento y reducir el costo de administración del sistema de Azure Stack, es recomendable colocar cada disco de máquina virtual en un contenedor independiente. Un contenedor debe albergar un disco del sistema operativo o un disco de datos, pero no ambos al mismo tiempo. Sin embargo, no hay ninguna restricción que impida colocar ambos en el mismo contenedor.

Si agrega uno o más discos de datos a una máquina virtual, considere usar contenedores adicionales como ubicación para albergar esos discos. Al igual que los discos de datos, los discos del sistema operativo de máquinas virtuales adicionales también deben estar en sus propios contenedores independientes.

Al crear varias máquinas virtuales, puede volver a usar la misma cuenta de almacenamiento con cada nueva máquina. Solo los contenedores que cree deben ser únicos.  

Para agregar discos a una máquina virtual, use el portal de usuarios o PowerShell.

| Método | Opciones
|-|-|
|[Portal de usuarios](#use-the-portal-to-add-additional-disks-to-a-vm)|-Agregar nuevos discos de datos a una máquina virtual que se aprovisionó anteriormente. Azure Stack crea nuevos discos. </br> </br>-Agregar un archivo .vhd existente como disco a una máquina virtual que se aprovisionó anteriormente. Para ello, primero debe preparar y cargar el archivo .vhd en Azure Stack. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | -Crear una nueva máquina virtual con un disco del sistema operativo y, al mismo tiempo, agregar uno o más discos de datos a esa máquina virtual. |


## <a name="use-the-portal-to-add-additional-disks-to-a-vm"></a>Uso del portal para agregar discos adicionales a una máquina virtual
De forma predeterminada, cuando se usa el portal para crear una máquina virtual para la mayoría de los elementos de Marketplace, solo se crea un disco del sistema operativo. Los discos creados por Azure se denominan discos administrados.

Después de aprovisionar una máquina virtual, puede usar el portal para agregar un nuevo disco de datos o un disco de datos existente a esa máquina virtual. Cada disco adicional debe colocarse en un contenedor independiente. Los discos que se agregan a una máquina virtual se denominan discos no administrados.

### <a name="use-the-portal-to-attach-a-new-data-disk-to-a-vm"></a>Uso del portal para asociar un nuevo disco de datos a una máquina virtual

1.  En el portal, haga clic en **Máquinas virtuales**.    
    ![Ejemplo: Panel de la máquina virtual](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  Seleccione una máquina virtual que se haya aprovisionado anteriormente.   
    ![Ejemplo: Selección de una máquina virtual en el panel](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  En la máquina virtual, haga clic en **Discos** > **Asociar nuevo**.       
    ![Ejemplo: Asociación de un nuevo disco a la máquina virtual](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  En el panel **Asociar nuevo disco**, haga clic en **Ubicación**. De forma predeterminada, la ubicación se establece en el mismo contenedor que alberga el disco del sistema operativo.      
    ![Ejemplo: Establecimiento de la ubicación del disco](media/azure-stack-manage-vm-disks/disk-location.png)

5.  Seleccione la **cuenta de almacenamiento** que se usará. A continuación, seleccione el **contenedor** donde desea colocar el disco de datos. En la página **Contenedores**, puede crear un nuevo contenedor si así lo desea. A continuación, puede cambiar la ubicación del nuevo disco por la de su propio contenedor. Cuando use un contenedor independiente para cada disco, distribuya la colocación del disco de datos que pueda mejorar el rendimiento. Haga clic en **Seleccionar** para guardar la selección.     
    ![Ejemplo: Selección de un contenedor](media/azure-stack-manage-vm-disks/select-container.png)

6.  En la página **Asociar nuevo disco**, actualice los valores de **Nombre**, **Tipo**, **Tamaño** y **Almacenamiento en caché de host** del disco. A continuación, haga clic en **Aceptar** para guardar la nueva configuración de disco de la máquina virtual.  
    ![Ejemplo: Finalización de la asociación del disco](media/azure-stack-manage-vm-disks/complete-disk-attach.png)  

7.  Una vez que Azure Stack crea el disco y lo asocia a la máquina virtual, el nuevo disco aparece en la configuración de disco de la máquina virtual en **DISCOS DE DATOS**.   
    ![Ejemplo: Visualización del disco](media/azure-stack-manage-vm-disks/view-data-disk.png)


### <a name="attach-an-existing-data-disk-to-a-vm"></a>Incorporación de un disco de datos existente a una VM
1.  [Prepare un archivo .vhd](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd) para usarlo como disco de datos de una máquina virtual. Cargue ese archivo .vhd en una cuenta de almacenamiento que se use con la máquina virtual a la que quiere asociar al archivo .vhd.

  Considere usar un contenedor diferente para el archivo .vhd en lugar del que alberga el disco del sistema operativo.   
  ![Ejemplo: Carga de un archivo de disco duro virtual](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  Una vez cargado el archivo .vhd, está listo para asociar el disco duro virtual a una máquina virtual. En el menú izquierdo, haga clic en **Máquinas virtuales**.  
 ![Ejemplo: Selección de una máquina virtual en el panel](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  Seleccione la máquina virtual en la lista.    
  ![Ejemplo: Selección de una máquina virtual en el panel](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  En la página de la máquina virtual, haga clic en **Discos** > **Asociar existente**.   
  ![Ejemplo: Asociación de un disco existente](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  En la página **Asociar disco existente**, haga clic en **Archivo VHD**. Se abre la página **Cuentas de almacenamiento**.    
  ![Ejemplo: Selección de un archivo de disco duro virtual](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  En **Cuentas de almacenamiento**, seleccione la cuenta que quiere usar y, a continuación, seleccione un contenedor para albergar el archivo .vhd cargado anteriormente. Seleccione el archivo .vhd y, a continuación, haga clic en **Seleccionar** para guardar la selección.    
  ![Ejemplo: Selección de un contenedor](media/azure-stack-manage-vm-disks/select-container2.png)

7.  En **Asociar disco existente**, el archivo que seleccionó aparece en **Archivo VHD**. Actualice el valor de **Almacenamiento en caché de host** del disco y luego haga clic en **Aceptar** para guardar la configuración del nuevo disco de la máquina virtual.    
  ![Ejemplo: Asociación del archivo VHD](media/azure-stack-manage-vm-disks/attach-vhd.png)

8.  Una vez que Azure Stack crea el disco y lo asocia a la máquina virtual, el nuevo disco aparece en la configuración de disco de la máquina virtual en **Discos de datos**.   
  ![Ejemplo: Finalización de la asociación del disco](media/azure-stack-manage-vm-disks/complete-disk-attach.png)


## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>Uso de PowerShell para agregar varios discos no administrados a una máquina virtual
Puede usar PowerShell para aprovisionar una máquina virtual y agregar un nuevo disco de datos o asociar un archivo **.vhd** existente como disco de datos.

El cmdlet **Add-AzureRmVMDataDisk** agrega un disco de datos a una máquina virtual. Puede agregar un disco de datos al crear una máquina virtual o lo puede agregar a una máquina virtual existente. Especifique el parámetro **VhdUri** para distribuir los discos a distintos contenedores.

### <a name="add-data-disks-to-a-new-virtual-machine"></a>Adición de discos de datos a una nueva máquina virtual
En los ejemplos siguientes se usan comandos de PowerShell para crear una máquina virtual con tres discos de datos, cada uno de ellos colocado en un contenedor diferente.

El primer comando crea un objeto de máquina virtual y, a continuación, lo almacena en la variable *$VirtualMachine*. El comando asigna un nombre y un tamaño a la máquina virtual.
  ```
  $VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"
  ```

Los tres siguientes comandos asignan rutas de acceso de tres discos de datos a las variables *$DataDiskVhdUri01*, *$DataDiskVhdUri02* y *$DataDiskVhdUri03*. Defina un nombre de ruta de acceso diferente en la dirección URL para distribuir los discos a diferentes contenedores.     
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```

  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```

  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```

Los tres comandos finales agregan discos de datos a la máquina virtual almacenada en *$VirtualMachine*. Cada comando especifica el nombre, la ubicación y propiedades adicionales del disco. El identificador URI de cada disco se almacena en *$DataDiskVhdUri01*, *$DataDiskVhdUri02* y *DataDiskVhdUri03 $*.
  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                  -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                  -VhdUri $DataDiskVhdUri01 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                 -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                 -VhdUri $DataDiskVhdUri02 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                  -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                  -VhdUri $DataDiskVhdUri03 -CreateOption Empty
  ```

Use los siguientes comandos de PowerShell para agregar la configuración de red y de disco del sistema operativo a la máquina virtual y, a continuación, inicie la nueva máquina virtual.
  ```
  #set variables
  $rgName = "myResourceGroup"
  $location = "local"
  #Set OS Disk
  $osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
  $osDiskName = "osDisk"

  $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
      -CreateOption FromImage -Windows

  # Create a subnet configuration
  $subnetName = "mySubNet"
  $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

  # Create a vnet configuration
  $vnetName = "myVnetName"
  $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
      -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

  # Create a public IP
  $ipName = "myIP"
  $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
      -AllocationMethod Dynamic

  # Create a network security group cnfiguration
  $nsgName = "myNsg"
  $rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
      -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
      -SourceAddressPrefix Internet -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
      -Name $nsgName -SecurityRules $rdpRule

  # Create a NIC configuration
  $nicName = "myNicName"
  $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
  -Location $location -SubnetId $vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

  #Create the new VM
  $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine | `
      Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
      -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
  New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
  ```



### <a name="add-data-disks-to-an-existing-virtual-machine"></a>Adición de discos de datos a una máquina virtual existente
En los ejemplos siguientes se usan comandos de PowerShell para agregar tres discos de datos a una máquina virtual existente.
El primer comando obtiene la máquina virtual llamada VirtualMachine mediante el cmdlet **Get-AzureRmVM**. El comando almacena la máquina virtual en la variable *$VirtualMachine* .
  ```
  $VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                  -Name "VirtualMachine"
  ```
Los tres siguientes comandos asignan rutas de acceso de tres discos de datos a las variables $DataDiskVhdUri01, $DataDiskVhdUri02 y $DataDiskVhdUri03.  Los diferentes nombres de ruta de acceso de vhduri indican contenedores diferentes para la colocación de los discos.
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```
  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```
  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```


  Los tres siguientes comandos agregan los discos de datos a la máquina virtual almacenada en la variable *$VirtualMachine*. Cada comando especifica el nombre, la ubicación y propiedades adicionales del disco. El identificador URI de cada disco se almacena en *$DataDiskVhdUri01*, *$DataDiskVhdUri02* y *DataDiskVhdUri03 $*.
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                        -VhdUri $DataDiskVhdUri01 -LUN 0 `
                        -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                        -VhdUri $DataDiskVhdUri02 -LUN 1 `
                        -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                        -VhdUri $DataDiskVhdUri03 -LUN 2 `
                        -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
  ```


  El comando final actualiza el estado de la máquina virtual almacenada en *$VirtualMachine* en *ResourceGroupName*.
  ```
  Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
  ```
<!-- Pending scripts  

## Distribute the data disks of an existing VM
If you have a VM with more than one disk in the same container, the service operator of the Azure Stack deployment might ask you to redistribute the disks into individual containers.

To do so, use the scripts from the following location in GitHub. These scripts can be used to move the data disks to different containers.
-->

## <a name="next-steps"></a>Pasos siguientes
Para aprender más sobre las máquina virtuales de Azure Stack, continúe con el artículo [Consideraciones sobre máquinas virtuales en Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations).
