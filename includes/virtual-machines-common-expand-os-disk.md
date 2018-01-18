## <a name="overview"></a>Información general
Cuando se crea una nueva máquina virtual (VM) en un grupo de recursos mediante la implementación de una imagen de [Azure Marketplace](https://azure.microsoft.com/marketplace/), la unidad del sistema operativo predeterminada suele tener 127 GB (algunas imágenes son más pequeñas de manera predeterminada). Aunque es posible agregar discos de datos a la máquina virtual (la cantidad depende de la SKU que haya elegido) y, además, se recomienda instalar aplicaciones y cargas de trabajo intensivas de CPU en estos discos de anexo, a menudo los clientes necesitan expandir la unidad del sistema operativo para admitir determinados escenarios, como los siguientes:

1. Compatibilidad con aplicaciones heredadas que instalan componentes en la unidad del sistema operativo.
2. Migración de una máquina virtual o un equipo físico desde local con una unidad del sistema operativo más grande.

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: Resource Manager y el clásico. Este artículo trata sobre el uso del modelo de Resource Manager. Microsoft recomienda que las implementaciones más recientes usen el modelo de Resource Manager.
> 
> 

## <a name="resize-the-os-drive"></a>Cambio del tamaño de la unidad del sistema operativo
En este artículo se va a realizar la tarea de cambiar el tamaño de la unidad del sistema operativo con módulos de Resource Manager de [Azure Powershell](/powershell/azureps-cmdlets-docs). Le vamos a mostrar cómo cambiar el tamaño de la unidad del sistema operativo tanto para los discos no administrados como para los administrados porque el método para cambiar el tamaño de los discos es diferente en los dos tipos.

### <a name="for-resizing-unmanaged-disks"></a>Para cambiar de tamaño los discos no administrados:

Abra la ventana de Powershell o Powershell ISE en el modo administrativo y siga estos pasos:

1. Inicie sesión en su cuenta de Microsoft Azure en el modo de administración de recursos y seleccione su suscripción de la siguiente manera:
   
   ```Powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Configure el nombre de grupo de recursos y el nombre de la máquina virtual de la siguiente forma:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Obtenga una referencia a la máquina virtual de la siguiente manera:
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Detenga la máquina virtual antes de cambiar el tamaño del disco de la siguiente forma:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Este es el momento que hemos estado esperando. Configure el tamaño del disco del sistema operativo no administrado en el valor deseado y actualice la máquina virtual de la siguiente manera:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > El nuevo tamaño debe ser mayor que el tamaño de disco existente. El máximo permitido es 2048 GB para discos del sistema operativo. (el blob de VHD se puede expandir más, pero el sistema operativo solo podrá trabajar con los primeros 2048 GB).
   > 
   > 
6. La actualización de la máquina virtual puede tardar unos segundos. Una vez que el comando acabe de ejecutarse, reinicie la máquina virtual de la siguiente forma:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

### <a name="for-resizing-managed-disks"></a>Para cambiar de tamaño los discos administrados:

Abra la ventana de Powershell o Powershell ISE en el modo administrativo y siga estos pasos:

1. Inicie sesión en su cuenta de Microsoft Azure en el modo de administración de recursos y seleccione su suscripción de la siguiente manera:
   
   ```Powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Configure el nombre de grupo de recursos y el nombre de la máquina virtual de la siguiente forma:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Obtenga una referencia a la máquina virtual de la siguiente manera:
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Detenga la máquina virtual antes de cambiar el tamaño del disco de la siguiente forma:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Obtenga una referencia al disco del sistema operativo administrado. Configure el tamaño del disco del sistema operativo en el valor deseado y actualice el disco de la siguiente manera:
   
   ```Powershell
   $disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > El nuevo tamaño debe ser mayor que el tamaño de disco existente. El máximo permitido es 2048 GB para discos del sistema operativo. (el blob de VHD se puede expandir más, pero el sistema operativo solo podrá trabajar con los primeros 2048 GB).
   > 
   > 
6. La actualización de la máquina virtual puede tardar unos segundos. Una vez que el comando acabe de ejecutarse, reinicie la máquina virtual de la siguiente forma:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

Eso es todo. Ahora RDP en la máquina virtual, abra Administración de equipos (o Administración de discos) y expanda la unidad utilizando el espacio recién asignado.

## <a name="summary"></a>Resumen
En este artículo, hemos usado los módulos de Azure Resource Manager de Powershell para expandir la unidad del sistema operativo de una máquina virtual de IaaS. El script completo se muestra a continuación para su referencia tanto para discos administrados como no administrados:

Discos no administrados:

```Powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
Discos administrados:

```Powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRMVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="next-steps"></a>Pasos siguientes
Aunque en este artículo nos centramos principalmente en expandir el disco del sistema operativo administrado/no administrado de la máquina virtual, también puede utilizarse el script desarrollado para expandir los discos de datos conectados a la máquina virtual. Por ejemplo, para expandir el primer disco de datos conectado a la máquina virtual, reemplace el objeto ```OSDisk``` de ```StorageProfile``` por la matriz ```DataDisks``` y utilice un índice numérico para obtener una referencia al primer disco de datos conectado, como se muestra a continuación:

Disco no administrado:
```Powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```
Disco administrado:
```Powershell
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

Del mismo modo, puede hacer referencia a otros discos de datos conectados a la máquina virtual, ya sea mediante un índice, como se muestra arriba o con la propiedad del disco ```Name``` , como se muestra a continuación:

Disco no administrado:
```Powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```
Disco administrado:
```Powershell
(Get-AzureRmDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

Si desea averiguar cómo conectar discos a una máquina virtual de Azure Resource Manager, consulte este [artículo](../articles/virtual-machines/windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

