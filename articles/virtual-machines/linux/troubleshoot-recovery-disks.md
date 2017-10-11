---
title: "Uso de una máquina virtual Linux de solución de problemas con la CLI de Azure 2.0 | Microsoft Docs"
description: "Aprenda a solucionar problemas de la máquina virtual Linux mediante la conexión del disco del sistema operativo a una máquina virtual de recuperación mediante la CLI de Azure 2.0"
services: virtual-machines-linux
documentationCenter: 
authors: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: iainfou
ms.openlocfilehash: 7a28accce1bd328b2b486b588c44d91b03e42122
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli-20"></a>Solución de problemas de una máquina virtual Linux mediante la conexión del disco del sistema operativo a una máquina virtual de recuperación mediante la CLI de Azure 2.0
Si la máquina virtual Linux se encuentra un error de disco o de arranque, deberá realizar los pasos para solucionar problemas en el propio disco duro virtual. Un ejemplo habitual sería una entrada no válida en `/etc/fstab` que impide que la máquina virtual se pueda arrancar correctamente. En este artículo se detalla cómo utilizar la CLI de Azure 2.0 para conectar el disco duro virtual a otra máquina virtual Linux para solucionar los errores y, posteriormente, volver a crear la máquina virtual original. También puede llevar a cabo estos pasos con la [CLI de Azure 1.0](troubleshoot-recovery-disks-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="recovery-process-overview"></a>Introducción al proceso de recuperación
El proceso de solución de problemas es el siguiente:

1. Elimine la máquina virtual que tiene problemas, conservando los discos duros virtuales.
2. Conecte y monte el disco duro virtual en otra máquina virtual Linux con el fin de solucionar problemas.
3. Conéctese a la máquina virtual de solución de problemas. Edite los archivos o ejecute cualquier herramienta necesaria para solucionar los problemas del disco duro virtual original.
4. Desmonte y desconecte el disco duro virtual de la máquina virtual de solución de problemas.
5. Cree una máquina virtual mediante el disco duro virtual original.

Para realizar estos pasos para la solución de problemas, es preciso tener instalada la [CLI de Azure 2.0](/cli/azure/install-az-cli2) más reciente y haber iniciado sesión en una cuenta de Azure mediante [az login](/cli/azure/#login).

En los ejemplos siguientes, reemplace los nombres de parámetros por los suyos propios. Los nombres de parámetros de ejemplo incluyen `myResourceGroup`, `mystorageaccount` y `myVM`.


## <a name="determine-boot-issues"></a>Determinación de los problemas de arranque
Examine la salida de serie para determinar por qué la máquina virtual no es capaz de arrancar correctamente. Un ejemplo habitual es una entrada no válida en `/etc/fstab` o el disco duro virtual subyacente que se va a eliminar o mover.

Obtenga los registros de arranque con [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics#get-boot-log). En el ejemplo siguiente se obtiene la salida de serie de la máquina virtual llamada `myVM` en el grupo de recursos `myResourceGroup`:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Revise la salida de serie para determinar por qué la máquina virtual no arranca correctamente. Si la salida de serie no proporciona ninguna indicación, debe revisar los archivos de registro en `/var/log` una vez que tenga el disco duro virtual conectado a una máquina virtual de solución de problemas.


## <a name="view-existing-virtual-hard-disk-details"></a>Visualización de los detalles del disco duro virtual existente
Para poder conectar el disco duro virtual (VHD) a otra máquina virtual, es preciso que identifique el identificador URI del disco del sistema operativo. 

Vea información acerca de la máquina virtual con [az vm show](/cli/azure/vm#show). Use la marca `--query` para extraer el identificador URI al disco del sistema operativo. En el ejemplo siguiente se obtiene información de los discos de la máquina virtual denominada `myVM` en el grupo de recursos denominado `myResourceGroup`:

```azurecli
az vm show --resource-group myResourceGroup --name myVM \
    --query [storageProfile.osDisk.vhd.uri] --output tsv
```

El identificador URI es similar a **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd**.

## <a name="delete-existing-vm"></a>Eliminación de la VM existente
Los discos duros virtuales y las máquinas virtuales son dos recursos diferentes de Azure. Un disco duro virtual es el recurso donde se almacenan el propio sistema operativo, las aplicaciones y las configuraciones. La propia máquina virtual consiste solo en metadatos que definen el tamaño o la ubicación y hace referencia a recursos como un disco duro virtual o una tarjeta de interfaz de red virtual (NIC). Cada disco duro virtual tiene una concesión que se asigna cuando se conecta a una máquina virtual. Aunque los discos de datos se pueden conectar y desconectar incluso mientras se está ejecutando la máquina virtual, no se puede desasociar el disco del sistema operativo, a menos que se elimine el recurso de máquina virtual. La concesión continúa para asociar el disco del sistema operativo a una máquina virtual incluso cuando esa máquina virtual está en un estado detenido o desasignado.

El primer paso para recuperar la máquina virtual es eliminar el propio recurso de máquina virtual. Al eliminar la máquina virtual, los discos duros virtuales se dejan en su cuenta de almacenamiento. Después de eliminar la máquina virtual, conecte el disco duro virtual a otra máquina virtual para localizar y solucionar los errores.

Elimine la máquina virtual con [az vm delete](/cli/azure/vm#delete). En el ejemplo siguiente se elimina la máquina virtual llamada `myVM` del grupo de recursos `myResourceGroup`:

```azurecli
az vm delete --resource-group myResourceGroup --name myVM 
```

Espere hasta que la máquina virtual haya terminado la eliminación antes de conectar el disco duro virtual a otra máquina virtual. La concesión en el disco duro virtual que lo asocia a la máquina virtual debe liberarse antes de poder conectar el disco duro virtual a otra máquina virtual.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Conexión del disco duro virtual existente a otra máquina virtual
Para los pasos siguientes, se usa otra máquina virtual con el fin de solucionar problemas. Conecte el disco duro virtual existente a esta máquina virtual de solución de problemas para examinar y modificar el contenido del disco. Por ejemplo, este proceso le permite corregir todos los errores de configuración o revisar archivos de registro adicionales de la aplicación o sistema. Elija o cree otra máquina virtual que se usará con fines de solución de problemas.

Conecte el disco duro virtual existente con [az vm unmanaged-disk attach](/cli/azure/vm/unmanaged-disk#attach). Cuando conecte el disco duro virtual existente, especifique el identificador URI en el disco obtenido en el comando `az vm show` anterior. En el ejemplo siguiente se conecta un disco duro virtual existente a la máquina virtual de solución de problemas con el nombre `myVMRecovery` en el grupo de recursos denominado `myResourceGroup`:

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```


## <a name="mount-the-attached-data-disk"></a>Montaje del disco de datos conectado

> [!NOTE]
> Los siguientes ejemplos detallan los pasos necesarios en una máquina virtual Ubuntu. Si usas una distribución de Linux diferente como Red Hat Enterprise Linux o SUSE, el registro de ubicaciones del archivo de registro y los comandos `mount` pueden ser ligeramente diferentes. Consulte la documentación para su distribución específica para los cambios apropiados en los comandos.

1. SSH en la máquina virtual de solución de problemas con las credenciales apropiadas. Si este disco es el primer disco de datos conectado a la máquina virtual de solución de problemas, es probable que el disco se conecte a `/dev/sdc`. Use `dmseg` para ver los discos conectados:

    ```bash
    dmesg | grep SCSI
    ```

    La salida es similar a la del ejemplo siguiente:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    En el ejemplo anterior, el disco del sistema operativo está en `/dev/sda` y el disco temporal que se proporciona para cada máquina virtual está en `/dev/sdb`. Si hubiera varios discos de datos, deben estar en `/dev/sdd`, `/dev/sde`, y así sucesivamente.

2. Cree un directorio para montar el disco duro virtual existente. El ejemplo siguiente permite crear un directorio llamado `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Si tiene varias particiones en el disco duro virtual existente, monte la partición requerida. En el ejemplo siguiente, se monta la primera partición principal en `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > El procedimiento recomendado consiste en montar los discos de datos en máquinas virtuales en Azure con el identificador único universal (UUID) del disco duro virtual. En este breve escenario de solución de problemas, no es necesario montar el disco duro virtual con el UUID. Sin embargo, en circunstancias normales, editar `/etc/fstab` para montar los discos duros virtuales mediante el nombre de dispositivo en lugar del UUID puede provocar que la máquina virtual no se pueda arrancar.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Solución de problemas en el disco duro virtual original
Con el disco duro virtual existente montado, ahora puede realizar todos los pasos de mantenimiento y solución de problemas según sea necesario. Una vez que se han resuelto los problemas, continúe con los pasos siguientes.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Desmontaje y desconexión del disco duro virtual original
Una vez resueltos los errores, desmonte y desconecte el disco duro virtual existente de la máquina virtual de solución de problemas. No se podrá usar el disco duro virtual en ninguna otra máquina virtual hasta que se libere la concesión que conecta el disco duro virtual a la máquina virtual de solución de problemas.

1. Desde la sesión SSH a la máquina virtual de solución de problemas, desmonte el disco duro virtual existente. Cambie primero el directorio primario del punto de montaje:

    ```bash
    cd /
    ```

    Ahora, desmonte el disco duro virtual existente. El siguiente ejemplo desmonta el dispositivo en `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Ahora, desconecte el disco duro virtual de la máquina virtual. Salga de la sesión SSH a la máquina virtual de solución de problemas. Enumere los discos de datos conectados a la máquina virtual de solución de problemas con [az vm unmanaged-disk list](/cli/azure/vm/unmanaged-disk#list). En el ejemplo siguiente se muestran los discos de datos conectados a la máquina virtual denominada `myVMRecovery` en el grupo de recursos denominado `myResourceGroup`:

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    Anote el nombre del disco duro virtual existente. Por ejemplo, el nombre de un disco con el identificador URI de **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** es **myVHD**. 

    Desconecte el disco de datos de la máquina virtual [az vm unmanaged-disk detach](/cli/azure/vm/unmanaged-disk#detach). En el ejemplo siguiente se desconecta el disco llamado `myVHD` de la máquina virtual llamada `myVMRecovery` del grupo de recursos `myResourceGroup`:

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```


## <a name="create-vm-from-original-hard-disk"></a>Creación de máquina virtual a partir del disco duro original
Para crear una máquina virtual a partir del disco duro virtual original, utilice [esta plantilla de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd). La plantilla JSON real está en el siguiente vínculo:

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json

La plantilla implementa una máquina virtual mediante el identificador URI del VHD del comando anterior. Implemente la plantilla con [az group deployment create](/cli/azure/group/deployment#create). Especifique el identificador URI en el VHD original y, después, especifique tanto el tipo de sistema operativo, como el tamaño y nombre de la máquina virtual como se indica a continuación:

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeployment \
  --parameters '{"osDiskVhdUri": {"value": "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"},
    "osType": {"value": "Linux"},
    "vmSize": {"value": "Standard_DS1_v2"},
    "vmName": {"value": "myDeployedVM"}}' \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json
```

## <a name="re-enable-boot-diagnostics"></a>Rehabilitación de los diagnósticos de arranque
Cuando se crea la máquina virtual desde el disco duro virtual existente, puede que no se habilite automáticamente el diagnóstico de arranque. Habilite el diagnóstico de arranque con [az vm boot-diagnostics enable](/cli/azure/vm/boot-diagnostics#enable). En el ejemplo siguiente se habilita la extensión de diagnóstico en la máquina virtual denominada `myDeployedVM` en el grupo de recursos `myResourceGroup`:

```azurecli
az vm boot-diagnostics enable --resource-group myResourceGroup --name myDeployedVM
```

## <a name="next-steps"></a>Pasos siguientes
Si tiene problemas para conectarse a la máquina virtual, consulte [Solución de problemas de conexiones SSH a una máquina virtual Linux de Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Para problemas con el acceso a aplicaciones que se ejecutan en su máquina virtual, consulte [Solucionar problemas de conectividad de aplicaciones en una máquina virtual de Linux en Azure](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).