---
title: Administración de discos de máquina virtual en Azure Stack | Microsoft Docs
description: Aprovisione discos de máquina virtual en Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/11/2018
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: 8e91b4d83aa90a7e744fb8e73cda788dbf8c58ec
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
---
# <a name="provision-virtual-machine-disk-storage-in-azure-stack"></a>Aprovisionamiento de almacenamiento en discos de máquina virtual en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

En este artículo se describe cómo aprovisionar almacenamiento en disco de máquina virtual mediante el portal de Azure Stack o PowerShell.

## <a name="overview"></a>Información general

Azure Stack admite el uso de [discos no administrados](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks) en máquinas virtuales, que pueden ser tanto discos del sistema operativo (SO) como disco de datos.

Para usar los discos no administrados, cree una [cuenta de almacenamiento](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) para almacenar los discos. Los discos creados se conocen como discos de máquina virtual y se almacenan en los contenedores de la cuenta de almacenamiento.

### <a name="best-practice-guidelines"></a>Guías de procedimientos recomendados

Para mejorar el rendimiento y reducir el costo total, es recomendable colocar cada disco de máquina virtual en un contenedor independiente. Un contenedor debe albergar un disco del sistema operativo o un disco de datos, pero no ambos al mismo tiempo. (Sin embargo, no existe ningún método para evitar la colocación de ambos tipos de discos en el mismo contenedor).

Si agrega uno o más discos de datos a una máquina virtual, use contenedores adicionales como ubicación para almacenar esos discos. Los discos del sistema operativo de máquinas virtuales adicionales también deben estar en sus propios contenedores.

Al crear varias máquinas virtuales, puede volver a usar la misma cuenta de almacenamiento con cada nueva máquina. Solo los contenedores que cree deben ser únicos.

### <a name="adding-new-disks"></a>Adición de nuevos discos

En la tabla siguiente se resume cómo agregar discos en el portal y PowerShell.

| Método | Opciones
|-|-|
|[Portal de usuarios](#use-the-portal-to-add-additional-disks-to-a-vm)|- Agregue discos de datos nuevos a una máquina virtual existente. Azure Stack crea nuevos discos. </br> </br>- Agregue un archivo (.vhd) de un disco existente a una máquina virtual aprovisionada anteriormente. Para ello, primero debe preparar el archivo .vhd y cargarlo luego en Azure Stack. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | -Crear una nueva máquina virtual con un disco del sistema operativo y, al mismo tiempo, agregar uno o más discos de datos a esa máquina virtual. |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>Uso del portal para agregar discos a una máquina virtual

De forma predeterminada, cuando se usa el portal para crear una máquina virtual para la mayoría de los elementos de Marketplace, solo se crea el disco del sistema operativo.

Después de crear una máquina virtual, puede usar el portal para:
* Crear un disco de datos y asociarlo a la máquina virtual.
* Cargar un disco de datos existente y asociarlo a la máquina virtual.

Cada disco no administrado que agregue se debe colocar en un contenedor independiente.

>[!NOTE]
>Los discos creados y administrados por Azure se denominan [discos administrados](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/managed-disks-overview).

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>Uso del portal para crear y asociar un nuevo disco de datos

1.  En el portal, seleccione **Máquinas virtuales**.    
    ![Ejemplo: Panel de la máquina virtual](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  Seleccione una máquina virtual que se haya aprovisionado anteriormente.   
    ![Ejemplo: Selección de una máquina virtual en el panel](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  En la máquina virtual, seleccione **Discos** > **Asociar nuevo**.       
    ![Ejemplo: Asociación de un nuevo disco a la máquina virtual](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  En el panel **Asociar nuevo disco**, seleccione **Ubicación**. De forma predeterminada, la ubicación se establece en el mismo contenedor que alberga el disco del sistema operativo.      
    ![Ejemplo: Establecimiento de la ubicación del disco](media/azure-stack-manage-vm-disks/disk-location.png)

5.  Seleccione la **cuenta de almacenamiento** que se usará. A continuación, seleccione el **contenedor** donde desea colocar el disco de datos. En la página **Contenedores**, puede crear un nuevo contenedor si así lo desea. A continuación, puede cambiar la ubicación del nuevo disco por la de su propio contenedor. Cuando use un contenedor independiente para cada disco, distribuya la colocación del disco de datos que pueda mejorar el rendimiento. Haga clic en **Seleccionar** para guardar la selección.     
    ![Ejemplo: Selección de un contenedor](media/azure-stack-manage-vm-disks/select-container.png)

6.  En la página **Asociar nuevo disco**, actualice los valores de **Nombre**, **Tipo**, **Tamaño** y **Almacenamiento en caché de host** del disco. A continuación, seleccione **Aceptar** para guardar la nueva configuración de disco de la máquina virtual.  
    ![Ejemplo: Finalización de la asociación del disco](media/azure-stack-manage-vm-disks/complete-disk-attach.png)  

7.  Una vez que Azure Stack crea el disco y lo asocia a la máquina virtual, el nuevo disco aparece en la configuración de disco de la máquina virtual en **DISCOS DE DATOS**.   
    ![Ejemplo: Visualización del disco](media/azure-stack-manage-vm-disks/view-data-disk.png)


### <a name="attach-an-existing-data-disk-to-a-vm"></a>Incorporación de un disco de datos existente a una VM

1.  [Prepare un archivo .vhd](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd) para usarlo como disco de datos de una máquina virtual. Cargue ese archivo .vhd en una cuenta de almacenamiento que se use con la máquina virtual a la que quiere asociar al archivo .vhd.

  Considere usar un contenedor diferente para el archivo .vhd en lugar del que alberga el disco del sistema operativo.   
  ![Ejemplo: Carga de un archivo de disco duro virtual](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  Una vez cargado el archivo .vhd, está listo para asociar el disco duro virtual a una máquina virtual. En el menú de la izquierda, haga clic en **Máquinas virtuales**.  
 ![Ejemplo: Selección de una máquina virtual en el panel](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  Seleccione la máquina virtual en la lista.    
  ![Ejemplo: Selección de una máquina virtual en el panel](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  En la página de la máquina virtual, seleccione **Discos** > **Asociar existente**.   
  ![Ejemplo: Asociación de un disco existente](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  En la página **Asociar disco existente**, seleccione **Archivo VHD**. Se abre la página **Cuentas de almacenamiento**.    
  ![Ejemplo: Selección de un archivo de disco duro virtual](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  En **Cuentas de almacenamiento**, seleccione la cuenta que quiere usar y, a continuación, elija un contenedor para hospedar el archivo .vhd cargado anteriormente. Seleccione el archivo .vhd y, a continuación, haga clic en **Seleccionar** para guardar la selección.    
  ![Ejemplo: Selección de un contenedor](media/azure-stack-manage-vm-disks/select-container2.png)

7.  En **Asociar disco existente**, el archivo que seleccionó aparece en **Archivo VHD**. Actualice el valor de **Almacenamiento en caché de host** del disco y luego seleccione **Aceptar** para guardar la configuración del nuevo disco de la máquina virtual.    
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
