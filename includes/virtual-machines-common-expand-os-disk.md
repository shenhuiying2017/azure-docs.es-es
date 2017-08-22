## <a name="overview"></a>Información general
Cuando cree una nueva máquina virtual (VM) en un grupo de recursos mediante la implementación de una imagen desde [Azure Marketplace](https://azure.microsoft.com/marketplace/), la unidad del sistema operativo predeterminada es de 127 GB. Aunque es posible agregar discos de datos a la máquina virtual (la cantidad depende de la SKU que haya elegido) y, además, se recomienda instalar aplicaciones y cargas de trabajo intensivas de CPU en estos discos de anexo, a menudo los clientes necesitan expandir la unidad del sistema operativo para admitir determinados escenarios, como los siguientes:

1. Compatibilidad con aplicaciones heredadas que instalan componentes en la unidad del sistema operativo.
2. Migración de una máquina virtual o un equipo físico desde local con una unidad del sistema operativo más grande.

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: Resource Manager y el clásico. Este artículo trata sobre el uso del modelo de Resource Manager. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos.
> 
> 

## <a name="resize-the-os-drive"></a>Cambio del tamaño de la unidad del sistema operativo
En este artículo se va a realizar la tarea de cambiar el tamaño de la unidad del sistema operativo con módulos de Resource Manager de [Azure Powershell](/powershell/azureps-cmdlets-docs). Abra la ventana de Powershell o Powershell ISE en el modo administrativo y siga estos pasos:

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
5. Este es el momento que hemos estado esperando. Configure el tamaño del disco del sistema operativo en el valor deseado y actualice la máquina virtual de la siguiente manera:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > El nuevo tamaño debe ser mayor que el tamaño de disco existente. El máximo permitido es de 1023 GB.
   > 
   > 
6. La actualización de la máquina virtual puede tardar unos segundos. Una vez que el comando acabe de ejecutarse, reinicie la máquina virtual de la siguiente forma:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

Eso es todo. Ahora RDP en la máquina virtual, abra Administración de equipos (o Administración de discos) y expanda la unidad utilizando el espacio recién asignado.

## <a name="summary"></a>Resumen
En este artículo, hemos usado los módulos de Azure Resource Manager de Powershell para expandir la unidad del sistema operativo de una máquina virtual de IaaS. El script completo se muestra a continuación para su referencia:

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

## <a name="next-steps"></a>Pasos siguientes
Aunque en este artículo nos centramos principalmente en expandir el disco del sistema operativo de la máquina virtual, también puede utilizarse el script desarrollado para expandir los discos de datos conectados a la máquina virtual mediante el cambio de una sola línea de código. Por ejemplo, para expandir el primer disco de datos conectado a la máquina virtual, reemplace el objeto ```OSDisk``` de ```StorageProfile``` por la matriz ```DataDisks``` y utilice un índice numérico para obtener una referencia al primer disco de datos conectado, como se muestra a continuación:

```Powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```
Del mismo modo, puede hacer referencia a otros discos de datos conectados a la máquina virtual, ya sea mediante un índice, como se muestra arriba o con la propiedad del disco ```Name``` , como se muestra a continuación:

```Powershell
($vm.StorageProfile.DataDisks | Where {$_.Name -eq 'my-second-data-disk'})[0].DiskSizeGB = 1023
```

Si desea averiguar cómo conectar discos a una máquina virtual de Azure Resource Manager, consulte este [artículo](../articles/virtual-machines/windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

