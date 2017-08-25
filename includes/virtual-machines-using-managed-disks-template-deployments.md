# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Uso de discos administrados en plantillas de Azure Resource Manager

En este documento se describen las diferencias entre discos administrados y discos no administrados cuando se usan plantillas de Azure Resource Manager para aprovisionar máquinas virtuales. Esto le ayudará a actualizar las plantillas existentes que usan discos no administrados a discos administrados. Como referencia, usamos la plantilla [101-vm-simple-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) como guía. Puede ver la plantilla usando tanto [discos administrados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) como una versión usando [discos no administrados](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) si desea compararlas directamente.

## <a name="unmanaged-disks-template-formatting"></a>Formato de plantilla de discos no administrados

Para comenzar, veremos cómo se implementan los discos no administrados. Cuando se crean discos no administrados, se necesita una cuenta de almacenamiento para contener los archivos VHD. Puede crear una cuenta de almacenamiento o usar una ya existente. En este artículo aprenderá a crear una cuenta de almacenamiento. Para ello, necesita un recurso de cuenta de almacenamiento en el bloque de recursos, como se muestra a continuación.

```
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2016-01-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

Dentro del objeto de máquina virtual, necesitamos una dependencia de la cuenta de almacenamiento para garantizar que se cree antes que la máquina virtual. En la sección `storageProfile`, se especifica el URI completo de la ubicación de VHD, que hace referencia a la cuenta de almacenamiento y se necesita para el disco de OS y cualquier disco de datos. 

```
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Formato de plantilla de discos administrados

Con Azure Managed Disks, el disco se convierte en un recurso de nivel superior y ya no es necesario que el usuario cree una cuenta de almacenamiento. Los discos de Managed Disks se publicaron primero en la versión de API `2016-04-30-preview`; están disponibles en todas las versiones posteriores de API y, ahora, son el tipo de disco predeterminado. En las secciones siguientes se describe la configuración predeterminada y se detalla cómo personalizar aún más los discos.

> [!NOTE]
> Se recomienda usar una versión de API posterior a `2016-04-30-preview`, ya que se produjeron cambios importantes entre `2016-04-30-preview` y `2017-03-30`.
>
>

### <a name="default-managed-disk-settings"></a>Configuración predeterminada de discos administrados

Para crear una máquina virtual con discos administrados, ya no es necesario crear el recurso de cuenta de almacenamiento y es posible actualizar el recurso de máquina virtual como se indica a continuación. Observe específicamente que `apiVersion` refleja `2017-03-30` y ni `osDisk` ni `dataDisks` hacen referencia ya a un URI específico para el VHD. Cuando la implementación se realiza sin especificar propiedades adicionales, el disco usará el [almacenamiento LRS estándar](../articles/storage/common/storage-redundancy.md). Si no se especifica ningún nombre, toma el formato de `<VMName>_OsDisk_1_<randomstring>` para el disco de SO y `<VMName>_disk<#>_<randomstring>` para cada disco de datos. De manera predeterminada, Azure Disk Encryption está deshabilitado; el almacenamiento en caché es Lectura/escritura en el caso del disco de SO y Ninguno para los discos de datos. En el ejemplo siguiente, puede observar que todavía existe una dependencia de cuenta de almacenamiento, pero es solo para el almacenamiento de diagnósticos y no es necesaria para almacenamiento en disco.

```
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>Uso de un recurso de disco administrado de nivel superior

Como alternativa para especificar la configuración de disco en el objeto de máquina virtual, puede crear un recurso de disco de nivel superior y adjuntarlo como parte de la creación de la máquina virtual. Por ejemplo, podemos crear un recurso de disco de la manera siguiente para usarlo como un disco de datos.

```
{
    "type": "Microsoft.Compute/disks",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "apiVersion": "2017-03-30",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

Dentro del objeto de máquina virtual, podemos hacer referencia a este objeto de disco para adjuntarlo. Especificar el identificador de recurso del disco administrado que creamos en la propiedad `managedDisk` permite adjuntar el disco cuando se crea la máquina virtual. Tenga en cuenta que el valor de la propiedad `apiVersion` del recurso de máquina virtual está establecido en `2017-03-30`. Además, tenga en cuenta que creamos una dependencia del recurso de disco para garantizar que se cree correctamente antes de la creación de la máquina virtual. 

```
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Creación de conjuntos de disponibilidad administrados con máquinas virtuales con discos administrados

Para crear conjuntos de disponibilidad administrados con máquinas virtuales con discos administrados, agregue el objeto `sku` al recurso de conjunto de disponibilidad y establezca la propiedad `name` en `Aligned`. Esto garantiza que los discos de cada máquina virtual estén suficientemente aislados entre sí para evitar puntos únicos de error. Tenga en cuenta también que el valor de la propiedad `apiVersion` del recurso del conjunto de disponibilidad está establecido en `2017-03-30`.

```
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/availabilitySets",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 3,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="additional-scenarios-and-customizations"></a>Personalizaciones y escenarios adicionales

Para información completa sobre las especificaciones de API de REST, revise la [documentación sobre cómo crear una API de REST de disco administrado](/rest/api/manageddisks/disks/disks-create-or-update). Encontrará escenarios adicionales, además de los valores predeterminados y aceptables que se pueden enviar a la API a través de implementaciones de plantilla. 

## <a name="next-steps"></a>Pasos siguientes

* Para conocer las plantillas completas que usan discos administrados, viste los vínculos siguientes al repositorio de inicio rápido de Azure.
    * [Máquina virtual Windows con disco administrado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Máquina virtual Linux con disco administrado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
    * [Lista completa de plantillas de discos administrados](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* Visite el documento [Introducción a Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md) para más información sobre los discos administrados.
* Revise la documentación de referencia de plantilla de los recursos de máquina virtual en el documento de [referencia de plantilla Microsoft.Compute/virtualMachines](/templates/microsoft.compute/virtualmachines).
* Revise la documentación de referencia de plantilla de los recursos de disco en el documento de [referencia de plantilla Microsoft.Compute/disks](/templates/microsoft.compute/disks).
 
