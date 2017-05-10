---
title: "Creación y administración de máquinas virtuales Linux con la CLI de Azure | Microsoft Docs"
description: "Tutorial: Creación y administración de máquinas virtuales Linux con la CLI de Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 7a6f255c64a584e29801aacb40c79462751fe535
ms.contentlocale: es-es
ms.lasthandoff: 05/03/2017

---

# <a name="create-and-manage-linux-vms-with-the-azure-cli"></a>Creación y administración de máquinas virtuales Linux con la CLI de Azure

En este tutorial se tratan elementos básicos de la creación de máquinas virtuales de Azure, como la selección de su tamaño, la selección de una imagen de máquina virtual y la implementación de una máquina virtual. También se cubren las operaciones básicas de administración como la administración del estado, la eliminación y el cambio del tamaño de una máquina virtual.

Se pueden completar los pasos de este tutorial con la versión más reciente de la [CLI de Azure 2.0](/cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Creación de un grupo de recursos

Cree un grupo de recursos con el comando [az group create](https://docs.microsoft.com/cli/azure/group#create). 

Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Se debe crear un grupo de recursos antes de una máquina virtual. En este ejemplo, se crea un grupo de recursos denominado *myResourceGroupVM* en la región *westus*. 

```azurecli
az group create --name myResourceGroupVM --location westus
```

Se especifica el grupo de recursos al crear o modificar una máquina virtual, como se ve a lo largo de este tutorial.

## <a name="create-virtual-machine"></a>Create virtual machine

Cree la máquina virtual con el comando [az vm create](https://docs.microsoft.com/cli/azure/vm#create). 

Al crear una máquina virtual, están disponibles varias opciones, como la imagen de sistema operativo, tamaño de disco y credenciales administrativas. En este ejemplo, se crea una máquina virtual llamada *myVM* que se ejecuta en Ubuntu. 

```azurecli
az vm create --resource-group myResourceGroupVM --name myVM --image UbuntuLTS --generate-ssh-keys
```

Una vez creada la máquina virtual, la CLI de Azure ofrece como salida información sobre la máquina virtual. Tome nota de `publicIpAddress`; esta dirección se puede usar para acceder a la máquina virtual. 

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM"
}
```

## <a name="connect-to-vm"></a>Conexión con una máquina virtual

Ahora puede conectarse con la máquina virtual mediante SSH. Reemplace la dirección IP de ejemplo por la dirección `publicIpAddress` que anotó en el paso anterior.

```bash
ssh 52.174.34.95
```

Una vez que termine con la máquina virtual, cierre la sesión de SSH. 

```bash
exit
```

## <a name="understand-vm-images"></a>Descripción de las imágenes de máquina virtual

Azure Marketplace incluye muchas imágenes que pueden usarse para crear VM. En los pasos anteriores, se creó una máquina virtual con una imagen de Ubuntu. En este paso, se usa la CLI de Azure para buscar en Marketplace una imagen de CentOS, que se usa para implementar una segunda máquina virtual.  

Para ver una lista de las imágenes usadas con más frecuencia, use el comando [az vm image list](/cli/azure/vm/image#list).

```azurecli
az vm image list --output table
```

La salida del comando devuelve las imágenes de máquina virtual más populares en Azure.

```bash
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

Para ver una lista completa, agregue el argumento `--all`. También puede filtrar la lista de imágenes por `--publisher` u `–-offer`. En este ejemplo, la lista se ha filtrado para todas las imágenes con una oferta que coincida con *CentOS*. 

```azurecli
az vm image list --offer CentOS --all --output table
```

Salida parcial:

```azurecli
Offer             Publisher         Sku   Urn                                     Version
----------------  ----------------  ----  --------------------------------------  -----------
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201501         6.5.201501
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201503         6.5.201503
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201506         6.5.201506
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20150904       6.5.20150904
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20160309       6.5.20160309
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20170207       6.5.20170207
```

Para implementar una máquina virtual con una imagen específica, tome nota del valor en la columna *Urn*. Al especificar la imagen, se puede reemplazar el número de versión de la imagen por "latest", para que se seleccione la versión más reciente de la distribución. En este ejemplo, se emplea el argumento `--image` para especificar la versión más reciente de una imagen de CentOS 6.5.  

```azurecli
az vm create --resource-group myResourceGroupVM --name myVM2 --image OpenLogic:CentOS:6.5:latest --generate-ssh-keys
```

## <a name="understand-vm-sizes"></a>Comprensión de los tamaños de máquina virtual

El tamaño de la máquina virtual determina la cantidad de recursos de proceso, como memoria, CPU y GPU, que están disponibles para la máquina virtual. Es necesario ajustar adecuadamente el tamaño de las máquinas virtuales para la carga de trabajo esperada. Si aumenta la carga de trabajo, se puede cambiar el tamaño de una máquina virtual existente.

### <a name="vm-sizes"></a>Tamaños de máquina virtual

En la tabla siguiente se clasifican los tamaños en casos de uso.  

| Tipo                     | Tamaños           |    Descripción       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Uso general](sizes-general.md)         |DSv2, Dv2, DS, D, Av2, A0-7| Uso equilibrado de CPU y memoria. Ideal para desarrollo/pruebas, así como soluciones de datos y aplicaciones de tamaño pequeño a mediano.  |
| [Proceso optimizado](sizes-compute.md)   | Fs, F             | Uso elevado de la CPU respecto a la memoria. Adecuado para aplicaciones, dispositivos de red y procesos por lotes con tráfico mediano.        |
| [Memoria optimizada](../virtual-machines-windows-sizes-memory.md)    | GS, G, DSv2, DS, Dv2 y D   | Uso elevado de memoria respecto al núcleo. Excelente para bases de datos relacionales, memorias caché de capacidad de mediana a grande y análisis en memoria.                 |
| [Almacenamiento optimizado](../virtual-machines-windows-sizes-storage.md)      | LS                | Alto rendimiento de disco y E/S. Perfecto para bases de datos SQL y NoSQL y macrodatos.                                                         |
| [GPU](sizes-gpu.md)          | NV, NC            | Máquinas virtuales especializadas específicas para actividades intensas de representación de gráficos y edición de vídeo.       |
| [Alto rendimiento](sizes-hpc.md) | H, A8-11          | Nuestras máquinas virtuales con CPU más eficaces e interfaces de red de alto rendimiento (RDMA) opcionales. 


### <a name="find-available-vm-sizes"></a>Búsqueda de los tamaños de máquina virtual disponibles

Para ver una lista de tamaños de máquina virtual disponibles en una región determinada, use el comando [az vm list-sizes](/cli/azure/vm#list-sizes). 

```azurecli
az vm list-sizes --location westus --output table
```

Salida parcial:

```azurecli
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 2          3584  Standard_DS1                          1           1047552                    7168
                 4          7168  Standard_DS2                          2           1047552                   14336
                 8         14336  Standard_DS3                          4           1047552                   28672
                16         28672  Standard_DS4                          8           1047552                   57344
                 4         14336  Standard_DS11                         2           1047552                   28672
                 8         28672  Standard_DS12                         4           1047552                   57344
                16         57344  Standard_DS13                         8           1047552                  114688
                32        114688  Standard_DS14                        16           1047552                  229376
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
                 4          3584  Standard_A2                           2           1047552                  138240
                 8          7168  Standard_A3                           4           1047552                  291840
                 4         14336  Standard_A5                           2           1047552                  138240
                16         14336  Standard_A4                           8           1047552                  619520
                 8         28672  Standard_A6                           4           1047552                  291840
                16         57344  Standard_A7                           8           1047552                  619520
```

### <a name="create-vm-with-specific-size"></a>Creación de máquinas virtuales con un tamaño específico

En el anterior ejemplo de creación de máquinas virtuales, no se proporcionó ningún tamaño, lo que conlleva el uso de un tamaño predeterminado. Se puede seleccionar un tamaño de máquina virtual al crearla con el comando [az vm create](/cli/azure/vm#create) y el argumento `--size`. 

```azurecli
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM3 \
    --image UbuntuLTS \
    --size Standard_F4s \
    --generate-ssh-keys
```

### <a name="resize-a-vm"></a>Cambiar el tamaño de una máquina virtual

Una vez implementada una máquina virtual, se puede cambiar su tamaño para aumentar o disminuir la asignación de recursos.

Antes de cambiar el tamaño de una máquina virtual, compruebe si el tamaño deseado está disponible en el clúster de Azure actual. El comando [az vm list-vm-resize-options](/cli/azure/vm#list-vm-resize-options) devuelve la lista de tamaños. 

```azurecli
az vm list-vm-resize-options --resource-group myResourceGroupVM --name myVM --query [].name
```
Si el tamaño deseado está disponible, puede cambiarlo con la máquina virtual encendida, aunque se reiniciará durante la operación. Use el comando [az vm resize]( /cli/azure/vm#resize) para realizar el cambio de tamaño.

```azurecli
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_DS4_v2
```

Si el tamaño deseado no está en el clúster actual, se debe desasignar la máquina virtual para que se pueda llevar a cabo la operación de cambio de tamaño. Use el comando [az vm deallocate]( /cli/azure/vm#deallocate) para detener y desasignar la máquina virtual. Tenga en cuenta que, cuando se vuelve a encender la máquina virtual, es posible que se quiten todos los datos del disco temporal. Además, la dirección IP pública cambia a menos que se esté usando una estática. 

```azurecli
az vm deallocate --resource-group myResourceGroupVM --name myVM
```

Una vez desasignada, puede realizarse el cambio de tamaño. 

```azurecli
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_GS1
```

Tras el cambio de tamaño, se puede iniciar la máquina virtual.

```azurecli
az vm start --resource-group myResourceGroupVM --name myVM
```

## <a name="vm-power-states"></a>Estados de la máquina virtual

Una máquina virtual de Azure puede tener uno de muchos estados de energía. Este estado representa el estado actual de la máquina virtual desde el punto de vista del hipervisor. 

### <a name="power-states"></a>Estados de energía

| Estado de energía | Descripción
|----|----|
| Iniciando | Indica que se está iniciando la máquina virtual. |
| Ejecución | Indica que la máquina virtual se está ejecutando. |
| Deteniéndose | Indica que se está deteniendo la máquina virtual. | 
| Stopped | Indica que se ha detenido la máquina virtual. Las máquinas virtuales en el estado detenido siguen acumulando cargos por procesos.  |
| Desasignando | Indica que se está desasignando la máquina virtual. |
| Desasignado | Indica que la máquina virtual se quitó del hipervisor pero sigue estando disponible en el plano de control. Las máquinas virtuales en el estado Desasignado no incurren cargos por procesos. |
| - | Indica que se desconoce el estado de la máquina virtual. |

### <a name="find-power-state"></a>Búsqueda del estado

Para recuperar el estado de una máquina virtual concreta, use el comando [az vm get instance-view](/cli/azure/vm#get-instance-view). Asegúrese de especificar un nombre válido para la máquina virtual y el grupo de recursos. 

```azurecli
az vm get-instance-view \
    --name myVM \
    --resource-group myResourceGroupVM \
    --query instanceView.statuses[1] --output table
```

Salida:

```azurecli
ode                DisplayStatus    Level
------------------  ---------------  -------
PowerState/running  VM running       Info
```

## <a name="management-tasks"></a>Tareas de administración

Durante el ciclo de vida de una máquina virtual, es posible que desee ejecutar tareas de administración como iniciar, detener o eliminar una máquina virtual. Además, puede crear scripts para automatizar tareas repetitivas o complejas. Mediante la CLI de Azure, muchas tareas comunes de administración se pueden ejecutar desde la línea de comandos o en scripts. 

### <a name="get-ip-address"></a>Obtención de dirección IP

Este comando devuelve las direcciones IP públicas y privadas de una máquina virtual.  

```azurecli
az vm list-ip-addresses --resource-group myResourceGroupVM --name myVM --output table
```

### <a name="stop-virtual-machine"></a>Detención de la máquina virtual

```azurecli
az vm stop --resource-group myResourceGroupVM --name myVM
```

### <a name="start-virtual-machine"></a>Inicio de la máquina virtual

```azurecli
az vm start --resource-group myResourceGroupVM --name myVM
```

### <a name="delete-resource-group"></a>Eliminación de un grupo de recursos

Al eliminar un grupo de recursos se eliminan también todos los recursos contenidos en el mismo.

```azurecli
az group delete --name myResourceGroupVM --no-wait --yes
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido conceptos básicos sobre la creación y administración de máquinas virtuales. Prosiga con el siguiente tutorial para aprender sobre los discos de máquina virtual.  

[Creación y administración de discos de máquinas virtuales](./tutorial-manage-disks.md)
