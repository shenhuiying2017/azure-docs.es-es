---
title: "Uso de una máquina virtual Linux de solución de problemas en Azure Portal | Microsoft Docs"
description: "Aprenda a solucionar problemas de la máquina virtual Linux mediante la conexión del disco del sistema operativo a una máquina virtual de recuperación mediante Azure Portal."
services: virtual-machines-linux
documentationCenter: 
authors: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2016
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: c96ff625c3e83f6fc9057f1163c877e8e0aed5e3
ms.contentlocale: es-es
ms.lasthandoff: 05/12/2017

---

# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Solución de problemas de una máquina virtual Linux mediante la conexión del disco del sistema operativo a una máquina virtual de recuperación mediante Azure Portal
Si la máquina virtual Linux se encuentra un error de disco o de arranque, deberá realizar los pasos para solucionar problemas en el propio disco duro virtual. Un ejemplo habitual sería una entrada no válida en `/etc/fstab` que impide que la máquina virtual se pueda arrancar correctamente. En este artículo se detalla cómo utilizar Azure Portal para conectar el disco duro virtual a otra máquina virtual Linux para solucionar los errores y, posteriormente, volver a crear la máquina virtual original.

## <a name="recovery-process-overview"></a>Introducción al proceso de recuperación
El proceso de solución de problemas es el siguiente:

1. Elimine la máquina virtual que tiene problemas, conservando los discos duros virtuales.
2. Conecte y monte el disco duro virtual en otra máquina virtual Linux con el fin de solucionar problemas.
3. Conéctese a la máquina virtual de solución de problemas. Edite los archivos o ejecute cualquier herramienta necesaria para solucionar los problemas del disco duro virtual original.
4. Desmonte y desconecte el disco duro virtual de la máquina virtual de solución de problemas.
5. Cree una máquina virtual mediante el disco duro virtual original.


## <a name="determine-boot-issues"></a>Determinación de los problemas de arranque
Examine el diagnóstico de arranque y la captura de pantalla de la máquina virtual para determinar por qué la máquina virtual no es capaz de arrancar correctamente. Un ejemplo habitual sería una entrada no válida en `/etc/fstab` o un disco duro virtual subyacente que se va a eliminar o mover.

Seleccione la máquina virtual en el portal y, a continuación, desplácese hacia abajo hasta la sección **Soporte técnico y solución de problemas**. Haga clic en **Diagnósticos de arranque** para ver los mensajes de consola transmitidos desde la máquina virtual. Revise los registros de consola para ver si puede determinar por qué la máquina virtual tiene un problema. En el ejemplo siguiente se muestra una máquina virtual atascada en el modo de mantenimiento que requiere intervención manual:

![Visualización de los registros de consola de diagnóstico de arranque](./media/troubleshoot-recovery-disks-portal/boot-diagnostics-error.png)

También puede hacer clic en **Captura de pantalla** en la parte superior del registro de diagnóstico de arranque para descargar una captura de la captura de pantalla de la máquina virtual.


## <a name="view-existing-virtual-hard-disk-details"></a>Visualización de los detalles del disco duro virtual existente
Antes de poder conectar el disco duro virtual a otra máquina virtual, debe identificar el nombre del disco duro virtual (VHD). 

Seleccione el grupo de recursos desde el portal y, a continuación, seleccione la cuenta de almacenamiento. Haga clic en **Blobs**, como en el ejemplo siguiente:

![Selección de blobs de almacenamiento](./media/troubleshoot-recovery-disks-portal/storage-account-overview.png)

Normalmente, suele tener un contenedor denominado **vhds** que almacena los discos duros virtuales. Seleccione el contenedor para ver una lista de los discos duros virtuales. Observe el nombre de su disco duro virtual (el prefijo normalmente es el nombre de la máquina virtual):

![Identificación del disco duro virtual en el contenedor de almacenamiento](./media/troubleshoot-recovery-disks-portal/storage-container.png)

Seleccione el disco duro virtual existente en la lista y copie la dirección URL para su uso en los pasos siguientes:

![Copia de la dirección URL del disco duro virtual existente](./media/troubleshoot-recovery-disks-portal/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Eliminación de la VM existente
Los discos duros virtuales y las máquinas virtuales son dos recursos diferentes de Azure. Un disco duro virtual es el recurso donde se almacenan el propio sistema operativo, las aplicaciones y las configuraciones. La propia máquina virtual consiste solo en metadatos que definen el tamaño o la ubicación y hace referencia a recursos como un disco duro virtual o una tarjeta de interfaz de red virtual (NIC). Cada disco duro virtual tiene una concesión que se asigna cuando se conecta a una máquina virtual. Aunque los discos de datos se pueden conectar y desconectar incluso mientras se está ejecutando la máquina virtual, no se puede desasociar el disco del sistema operativo, a menos que se elimine el recurso de máquina virtual. La concesión continúa para asociar el disco del sistema operativo a una máquina virtual incluso cuando esa máquina virtual está en un estado detenido o desasignado.

El primer paso para recuperar la máquina virtual es eliminar el propio recurso de máquina virtual. Al eliminar la máquina virtual, los discos duros virtuales se dejan en su cuenta de almacenamiento. Después de eliminar la máquina virtual, conecte el disco duro virtual a otra máquina virtual para localizar y solucionar los errores.

Seleccione la máquina virtual en el portal y, a continuación, haga clic en **Eliminar**:

![Captura de pantalla de diagnósticos de arranque de máquina virtual que muestran un error de arranque](./media/troubleshoot-recovery-disks-portal/stop-delete-vm.png)

Espere hasta que la máquina virtual haya terminado la eliminación antes de conectar el disco duro virtual a otra máquina virtual. La concesión en el disco duro virtual que lo asocia a la máquina virtual debe liberarse antes de poder conectar el disco duro virtual a otra máquina virtual.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Conexión del disco duro virtual existente a otra máquina virtual
Para los pasos siguientes, se usa otra máquina virtual con el fin de solucionar problemas. Conecte el disco duro virtual existente a esta máquina virtual de solución de problemas para examinar y modificar el contenido del disco. Por ejemplo, este proceso le permite corregir todos los errores de configuración o revisar archivos de registro adicionales de la aplicación o sistema. Elija o cree otra máquina virtual que se usará con fines de solución de problemas.

1. Seleccione el grupo de recursos desde el portal y, a continuación, seleccione la máquina virtual de solución de problemas. Seleccione **Discos** y, a continuación, haga clic en **Conectar existente**:

    ![Conexión del disco existente en el portal](./media/troubleshoot-recovery-disks-portal/attach-existing-disk.png)

2. Para seleccionar el disco duro virtual existente, haga clic en **Archivo VHD**:

    ![Busque el disco duro virtual existente.](./media/troubleshoot-recovery-disks-portal/select-vhd-location.png)

3. Seleccione la cuenta de almacenamiento y el contenedor y, a continuación, haga clic en el disco duro virtual existente. Haga clic en el botón **Seleccionar** para confirmar su elección:

    ![Seleccione el disco duro virtual existente](./media/troubleshoot-recovery-disks-portal/select-vhd.png)

4. Con el disco duro virtual ya seleccionado, haga clic en **Aceptar** para conectar el disco duro virtual existente:

    ![Confirmación de la conexión del disco duro virtual existente](./media/troubleshoot-recovery-disks-portal/attach-disk-confirm.png)

5. Después de unos segundos, el panel **Discos** mostrará el disco duro virtual existente conectado como un disco de datos:

    ![Disco duro virtual existente conectado como disco de datos](./media/troubleshoot-recovery-disks-portal/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>Montaje del disco de datos conectado

> [!NOTE]
> Los siguientes ejemplos detallan los pasos necesarios en una máquina virtual Ubuntu. Si usas una distribución de Linux diferente como Red Hat Enterprise Linux o SUSE, el registro de ubicaciones del archivo de registro y los comandos `mount` pueden ser ligeramente diferentes. Consulte la documentación para su distribución específica para los cambios apropiados en los comandos.

1. SSH en la máquina virtual de solución de problemas con las credenciales apropiadas. Si este disco es el primer disco de datos conectado a la máquina virtual de solución de problemas, es probable que se conecte a `/dev/sdc`. Use `dmseg` para enumerar los discos conectados:

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
Una vez resueltos los errores, desconecte el disco duro virtual existente de la máquina virtual de solución de problemas. No se podrá usar el disco duro virtual en ninguna otra máquina virtual hasta que se libere la concesión que conecta el disco duro virtual a la máquina virtual de solución de problemas.

1. Desde la sesión SSH a la máquina virtual de solución de problemas, desmonte el disco duro virtual existente. Cambie primero el directorio primario del punto de montaje:

    ```bash
    cd /
    ```

    Ahora, desmonte el disco duro virtual existente. El siguiente ejemplo desmonta el dispositivo en `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Ahora, desconecte el disco duro virtual de la máquina virtual. Seleccione la máquina virtual en el portal y haga clic en **Discos**. Seleccione el disco duro virtual existente y, a continuación, haga clic en **Desasociar**:

    ![Desconecte el disco duro virtual existente](./media/troubleshoot-recovery-disks-portal/detach-disk.png)

    Espere hasta que la máquina virtual haya desconectado correctamente el disco de datos antes de continuar.

## <a name="create-vm-from-original-hard-disk"></a>Creación de máquina virtual a partir del disco duro original
Para crear una máquina virtual a partir del disco duro virtual original, utilice [esta plantilla de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). La plantilla implementa una máquina virtual en una red virtual existente mediante la dirección URL del disco duro virtual del comando anterior. Haga clic en el botón **Implementar en Azure** como se muestra a continuación:

![Implementación de máquina virtual desde una plantilla de GitHub](./media/troubleshoot-recovery-disks-portal/deploy-template-from-github.png)

La plantilla se carga en Azure Portal para su implementación. Escriba los nombres de la nueva máquina virtual y los recursos de Azure existentes y pegue la dirección URL en el disco duro virtual existente. Para comenzar la implementación, haga clic en **Comprar**:

![Implementación de una máquina virtual a partir de una plantilla](./media/troubleshoot-recovery-disks-portal/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Rehabilitación de los diagnósticos de arranque
Cuando se crea la máquina virtual desde el disco duro virtual existente, puede que no se habilite automáticamente el diagnóstico de arranque. Para comprobar el estado del diagnóstico de arranque y activarlo si es necesario, seleccione la máquina virtual en el portal. En **Supervisión**, haga clic en **Configuración de diagnóstico**. Asegúrese de que el estado es **Activado**, y que la marca de verificación situada junto a **Diagnósticos de arranque** está seleccionada. Si realiza cambios, haga clic en **Guardar**:

![Actualización de la configuración de los diagnósticos de arranque](./media/troubleshoot-recovery-disks-portal/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>Pasos siguientes
Si tiene problemas para conectarse a la máquina virtual, consulte [Solución de problemas de conexiones SSH a una máquina virtual Linux de Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Para problemas con el acceso a aplicaciones que se ejecutan en su máquina virtual, consulte [Solucionar problemas de conectividad de aplicaciones en una máquina virtual de Linux en Azure](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para más información sobre el uso de Resource Manager, consulte [Información general de Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

