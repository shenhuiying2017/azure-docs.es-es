---
title: Acelerador de escritura de Azure para implementaciones de SAP | Microsoft Docs
description: Guía de operaciones para los sistemas SAP HANA que se implementan en Azure Virtual Machines.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b0cb9b4003faa2ccdd07ccc78c2095472690f0e7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="azure-write-accelerator-for-sap-deployments"></a>Acelerador de escritura de Azure para implementaciones de SAP
Acelerador de escritura de Azure es una funcionalidad que se implementa exclusivamente para las máquinas virtuales de la serie M. El Acelerador de escritura de Azure no está disponible en ninguna otra serie de máquinas virtuales de Azure, solo para la serie M. Como el nombre indica, el propósito de la funcionalidad es mejorar la latencia de E/S de las escrituras en Azure Premium Storage. 

>[!NOTE]
> En este momento, el Acelerador de escritura de Azure está en versión preliminar pública y requiere que su identificador de suscripción de Azure esté en la lista de permitidos.

La funcionalidad del acelerador de escritura de Azure está disponible para implementaciones de la serie M como versión preliminar pública en:

- Oeste de EE. UU. 2
- Europa Occidental
- Sudeste asiático

## <a name="planning-for-using-azure-write-accelerator"></a>Planeación del uso de Acelerador de escritura de Azure
El Acelerador de escritura de Azure se debe usar para los volúmenes, los que contienen el registro de transacciones o los registros de puestas al día de un DBMS. No se recomienda usar el Acelerador de escritura de Azure para los volúmenes de datos de un DBMS. El motivo de esta restricción es que el Acelerador de escritura de Azure requiere que los VHD de Azure Premium Storage se monten sin el almacenamiento en caché de lectura adicional disponible para Premium Storage. Se pueden observar mayores ventajas con este tipo de almacenamiento en caché en las bases de datos tradicionales. Como el Acelerador de escritura solo impacta en las actividades de escritura y no agiliza las lecturas, el diseño compatible para SAP es usar el Acelerador de escritura contra las unidades del registro de transacciones o del registro de puestas al día de las bases de datos compatibles de SAP. 

El Acelerador de escritura de Azure solo funciona en conjunto con los [discos administrados de Azure](https://azure.microsoft.com/services/managed-disks/). Por tanto, debe planear en consecuencia. 

>[!NOTE]
> Como la funcionalidad del Acelerador de escritura de Azure todavía está en versión preliminar pública, la funcionalidad todavía no admite ninguna implementación de escenario de producción.

Hay límites en los VHD de Azure Premium Storage por máquina virtual que el Acelerador de escritura de Azure puede admitir. Los límites actuales son:

- 16 VHD para una máquina virtual M128xx
- 8 VHD para una máquina virtual M64xx

> [!IMPORTANT]
> Si desea habilitar o deshabilitar el Acelerador de escritura de Azure para un volumen existente que consta de varios discos de Azure Premium Storage y se segmenta con administradores de volúmenes o discos de Windows, espacios de almacenamiento de Windows, servidor de archivos de escalabilidad horizontal (SOFS) de Windows, LVM o MDADM de Linux, todos los discos que componen el volumen se deben habilitar o deshabilitar para el Acelerador de escritura en pasos independientes. **Antes de habilitar o deshabilitar el Acelerador de escritura en este tipo de configuración, apague la máquina virtual de Azure**. 


> [!IMPORTANT]
> Para habilitar el Acelerador de escritura de Azure en un disco existente de Azure que NO forma parte de un volumen compuesto de varios discos con administradores de volúmenes o discos de Windows, espacios de almacenamiento de Windows, servidor de archivos de escalabilidad horizontal (SOFS) de Windows, LVM o MDADM de Linux, la carga de trabajo que accede al disco de Azure debe estar apagada. Las aplicaciones de base de datos que usan el disco de Azure DEBEN estar apagadas.

> [!IMPORTANT]
> La habilitación del acelerador de escritura para el disco del sistema operativo de la máquina virtual de Azure hará que esta se reinicie. 

La habilitación del Acelerador de escritura de Azure para los discos de sistema operativo no debería ser necesaria para las configuraciones de máquinas virtuales relacionadas con SAP.

### <a name="restrictions-when-using-azure-write-accelerator"></a>Restricciones del uso del Acelerador de escritura de Azure
Estas restricciones se aplican al usar el Acelerador de escritura de Azure para un VHD o disco de Azure:

- El almacenamiento en caché de discos Premium debe establecerse en "Ninguno" o "Solo lectura". No se admite ningún otro modo de almacenamiento en caché.
- Todavía no se admite la instantánea en el disco habilitado para el Acelerador de escritura. Esta restricción bloquea la capacidad que el servicio de Azure Backup tiene para realizar una instantánea coherente con la aplicación de todos los discos de la máquina virtual.
- Solo los tamaños de E/S más pequeños toman la ruta de acceso acelerada. En situaciones de carga de trabajo donde los datos se cargan de forma masiva o donde los búferes de registros de transacción de los diferentes sistemas de administración de bases de datos (DBMS) se llenan hasta un mayor grado antes de conservarse en el almacenamiento, existe la probabilidad de que la E/S escrita en el disco no tome la ruta de acceso acelerada.


## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Habilitación del Acelerador de escritura en un disco específico
En las secciones siguientes se describe cómo el Acelerador de escritura de Azure se pueden habilitar en los VHD de Azure Premium Storage.

En este momento, los únicos métodos son habilitar el Acelerador de escritura a través de la API de REST de Azure y PowerShell. Durante las próximas semanas, se informará de otros métodos que pronto serán compatibles en Azure Portal.

### <a name="prerequisites"></a>requisitos previos
En este momento, los requisitos previos siguientes se aplican al uso del Acelerador de escritura de Azure:

- El identificador de suscripción que se usó para implementar la máquina virtual y el almacenamiento de la máquina virtual debe estar en la lista de permitidos. Póngase en contacto con el administrador de cuentas, GBB o CSA de Microsoft para que su identificador de suscripción se agregue a la lista de permitidos. 
- Los discos en los que desea aplicar el Acelerador de escritura de Azure deben ser [discos administrados de Azure](https://azure.microsoft.com/services/managed-disks/).

### <a name="enabling-through-power-shell"></a>Habilitación a través de PowerShell
El módulo de Azure PowerShell de la versión 5.5.0 incluye los cambios en los cmdlets pertinentes para habilitar o deshabilitar el Acelerador de escritura de Azure para discos específicos de Azure Premium Storage.
Con el fin de habilitar o implementar los discos compatibles con el Acelerador de escritura, se modificaron los comandos de PowerShell siguientes y se extendieron para que aceptaran un parámetro para el Acelerador de escritura.

Se agregó "WriteAccelerator", un nuevo parámetro de modificador, a los cmdlets siguientes: 

- Set-AzureRmVMOsDisk
- Add-AzureRmVMDataDisk
- Set-AzureRmVMDataDisk
- Add-AzureRmVmssDataDisk

Si no se proporciona el parámetro, la propiedad se establece en false y se implementarán los discos no compatibles con el Acelerador de escritura de Azure.

Se agregó "OsDiskWriteAccelerator", un nuevo parámetro de modificador, a los cmdlets siguientes: 

- Set-AzureRmVmssStorageProfile

Si no se proporciona el parámetro, la propiedad se establece en false y se suministrarán discos que no usan el Acelerador de escritura de Azure.

Se agregó "OsDiskWriteAccelerator", un nuevo parámetro booleano opcional (que no admite valores NULL), a los cmdlets siguientes: 

- Update-AzureRmVM
- Update-AzureRmVmss

Especifique $true o $false para controlar la compatibilidad del Acelerador de escritura con los discos.

Algunos ejemplos de los comandos podrían ser:

```

New-AzureRmVMConfig | Set-AzureRmVMOsDisk | Add-AzureRmVMDataDisk -Name "datadisk1" | Add-AzureRmVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVM

Get-AzureRmVM | Update-AzureRmVM -OsDiskWriteAccelerator $true

New-AzureRmVmssConfig | Set-AzureRmVmssStorageProfile -OsDiskWriteAccelerator | Add-AzureRmVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzureRmVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVmss

Get-AzureRmVmss | Update-AzureRmVmss -OsDiskWriteAccelerator:$false 

```

Se pueden ejecutar dos escenarios principales mediante scripts, tal como se muestra en las secciones siguientes.

#### <a name="adding--new-disk-supported-by-azure-write-accelerator"></a>Incorporación de un disco nuevo compatible con el Acelerador de escritura de Azure
Puede usar este script para agregar un disco nuevo a la máquina virtual. El disco que se crea con este script usará el Acelerador de escritura de Azure.

```

# Specify your VM Name
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzureRmVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```
Debe adaptar los nombres de la máquina virtual, el disco, el grupo de recursos, el tamaño del disco y el LunID del disco para la implementación específica.


#### <a name="enabling-azure-write-accelerator-on-an-existing-azure-disk"></a>Habilitación del Acelerador de escritura en un disco existente de Azure
Si tiene que habilitar el Acelerador de escritura en un disco existente, puede usar este script para llevar a cabo la tarea:

```

#Specify your VM Name
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
#data disk name
$datadiskname = "testsap-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzureRmVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```

Debe adaptar los nombres de la máquina virtual, el disco y el grupo de recursos. El script anterior agrega el Acelerador de escritura a un disco existente si el valor de $newstatus está establecido en "$true". Si usa el valor "$false", el Acelerador de escritura se deshabilitará en un disco determinado.

> [!Note]
> Si ejecuta el script anterior, desconectará el disco especificado, habilitará el Acelerador de escritura en el disco y, luego, lo volverá a adjuntar.




### <a name="enabling-through-rest-apis"></a>Habilitación a través de las API de REST
Para implementar a través de la API de REST de Azure, necesita instalar ARMclient de Azure.

#### <a name="install-armclient"></a>Instalación de ARMclient

Para ejecutar ARMclient, debe instalarlo a través de Chocolatey. Puede instalarlo a través de cmd.exe o PowerShell. Use derechos elevados para estos comandos ("Ejecutar como administrador").

Con cmd.exe, ejecute este comando:

```
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

Si usa PowerShell, debe usar:

```
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

Ahora puede instalar ARMclient con el comando siguiente en cmd.exe o PowerShell.

```
choco install armclient
```

#### <a name="getting-your-current-vm-configuration"></a>Obtención de la configuración de la máquina virtual actual
Para cambiar los atributos de la configuración de disco, primero debe obtener la configuración actual en un archivo JSON. Puede obtener la configuración actual mediante la ejecución del comando siguiente:

```
armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>
```
Reemplace los términos dentro de "<<   >>" con los datos, incluido el nombre que debe tener el archivo JSON.

La salida debería parecerse a esta:

```
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

El paso siguiente es actualizar el archivo JSON y habilitar el Acelerador de escritura en el disco llamado "log1". Para ello, agregue este atributo en el archivo JSON después de la entrada de caché del disco. 

```
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Luego, actualice la implementación existente con este comando:

```
armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>

```

La salida debe parecerse a la que aparece a continuación. Puede ver que el Acelerador de escritura está habilitado para un disco.

```
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

Desde el momento del cambio, el Acelerador de escritura debe admitir la unidad.

 