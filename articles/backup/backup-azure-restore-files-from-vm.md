---
title: "Azure Backup: Recuperación de archivos y carpetas desde una copia de seguridad de máquina virtual de Azure | Microsoft Docs"
description: "Recuperación de archivos desde un punto de recuperación de máquina virtual de Azure"
services: backup
documentationcenter: dev-center-name
author: pvrk
manager: shivamg
keywords: "recuperación de elementos; recuperación de archivos desde una copia de seguridad de máquina virtual de Azure; restauración de archivos de máquina virtual de Azure"
ms.assetid: f1c067a2-4826-4da4-b97a-c5fd6c189a77
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/27/2017
ms.author: pullabhk;markgal
ms.openlocfilehash: 46cc2737c23b02c6542320e355607f83042bd058
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Recuperación de archivos desde una copia de seguridad de máquina virtual de Azure

Azure Backup ofrece la funcionalidad de restauración de [discos y máquinas virtuales de Azure](./backup-azure-arm-restore-vms.md) desde copias de seguridad de máquina virtual de Azure, también conocidos como puntos de restauración. En este artículo se explica cómo recuperar archivos y carpetas desde una copia de seguridad de máquina virtual de Azure. La restauración de archivos y carpetas está disponible unicamente para las máquinas virtuales de Azure implementadas con el modelo de Resource Manager y protegidas en un almacén de Recovery Services.

> [!Note]
> No se pueden recuperar archivos a partir de una copia de seguridad de máquina virtual cifrada.
>

## <a name="mount-the-volume-and-copy-files"></a>Montaje del volumen y copia de archivos

Para restaurar archivos o carpetas desde el punto de restauración, vaya a la máquina virtual y elíjalo. 

1. Inicie sesión en [Azure Portal](http://portal.Azure.com) y, en el menú izquierdo, haga clic en **Máquinas virtuales**. En la lista de máquinas virtuales, seleccione la que desee para abrir su panel. 

2. En el menú de la máquina virtual, haga clic en **Backup** para abrir el panel de Backup.

    ![Apertura del elemento de copia de seguridad del almacén de Recovery Services](./media/backup-azure-restore-files-from-vm/open-vault-from-vm.png)

3. En el menú del panel de Backup, haga clic en **Recuperación de archivos** para abrir el menú.

    ![Menú Recuperación de archivos](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. En el menú desplegable **Seleccionar punto de recuperación**, elija el punto de recuperación que contiene los archivos que desee. De forma predeterminada, el punto de recuperación más reciente ya está seleccionado.

5. Para descargar el software para copiar archivos del punto de recuperación, haga clic en **Download Executable** (Descargar ejecutable) para máquinas virtuales Windows de Azure o **Download Script** (Descargar script) para máquinas virtuales Linux de Azure. 

    ![Contraseña generada](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure descarga el archivo ejecutable o el script en el equipo local.

    ![mensaje de descarga del archivo ejecutable o del script](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Para ejecutar el archivo ejecutable o el script como administrador, se recomienda guardar el archivo descargado en el equipo.

6. El archivo ejecutable o script está protegido con contraseña y es obligatoria. En el menú **Recuperación de archivos**, haga clic en el botón de copia para cargar la contraseña en la memoria.

    ![Contraseña generada](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Desde la ubicación de descarga (normalmente, la carpeta Descargas), haga clic con el botón derecho en el archivo ejecutable o el script y ejecútelo con las credenciales del administrador. Cuando se le solicite, escriba la contraseña o péguela de la memoria y presione Entrar. Una vez que se escriba la contraseña válida, el script se conecta al punto de recuperación.

    ![Menú Recuperación de archivos](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Si lo hace en un equipo con acceso restringido, asegúrese de que hay acceso a los siguientes recursos:

    - download.Microsoft.com
    - Puntos de conexión de Azure usados para copias de seguridad de máquina virtual de Azure
    - Puerto de salida 3260

   En el caso de Linux, el script requiere los componentes "open-iscsi" e "lshw" para conectar con el punto de recuperación. Si los componentes no existen en el equipo donde se ejecuta el script, este solicita permiso para instalarlos. Otorgue el consentimiento para instalar los componentes necesarios.  
         
   Puede ejecutar el script en cualquier máquina que tenga el mismo sistema operativo (o uno compatible) que la máquina virtual de la que se realiza la copia de seguridad. Consulte la [tabla de sistemas operativos compatibles](backup-azure-restore-files-from-vm.md#compatible-os) para ver cuáles son. Si la máquina virtual de Azure protegida usa espacios de almacenamiento de Windows (para máquinas virtuales Windows de Azure) o matrices LVM/RAID (para máquinas virtuales Linux), no puede ejecutar el archivo ejecutable o script en la misma máquina virtual. En su lugar, ejecútelo en otra máquina que tenga un sistema operativo compatible.

### <a name="compatible-os"></a>Sistemas operativos compatibles

#### <a name="for-windows"></a>Para Windows

En la siguiente tabla se muestra la compatibilidad entre los sistemas operativos de servidor y equipo. Al recuperar archivos, no podrá restaurar archivos a una versión anterior o posterior del sistema operativo. Por ejemplo, no puede restaurar un archivo de una máquina virtual con Windows Server 2016 a una con Windows Server 2012 o un equipo con Windows 8. Puede restaurar archivos de una máquina virtual al mismo sistema operativo de servidor o a uno de cliente compatible.   

|Sistema operativo de servidor | Sistema operativo de cliente compatible  |
| --------------- | ---- |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

#### <a name="for-linux"></a>Para Linux

En Linux, el sistema operativo del equipo usado para restaurar archivos debe admitir el sistema de archivos de la máquina virtual protegida. Al seleccionar un equipo para ejecutar el script, asegúrese de que este tiene un sistema operativo compatible y utiliza una de las versiones de la tabla siguiente:

|SO Linux | Versiones  |
| --------------- | ---- |
| Ubuntu | 12.04 y posterior |
| CentOS | 6.5 y posterior  |
| RHEL | 6.7 y posterior |
| Debian | 7 y posterior |
| Oracle Linux | 6.4 y posterior |

El script también requiere los componentes Python y Bash para realizar la ejecución y la conexión al punto de recuperación con seguridad.

|Componente | Versión  |
| --------------- | ---- |
| Bash | 4 y posterior |
| Python | 2.6.6 y posterior  |


### <a name="identifying-volumes"></a>Identificación de volúmenes

#### <a name="for-windows"></a>Para Windows

Al ejecutar el archivo ejecutable, el sistema operativo monta los nuevos volúmenes y asigna letras de unidad. Puede usar el Explorador de Windows o el Explorador de archivos para buscar esas unidades. Las letras de unidad asignadas a los volúmenes no pueden ser las mismas que las de la máquina virtual original; sin embargo, se conserva el nombre del volumen. Por ejemplo, si el volumen de la máquina virtual original era "Data Disk (E:`\`)", ese volumen se puede conectar al equipo local como "Data Disk ('cualquier letra':`\`)". Busque en todos los volúmenes que se mencionan en la salida del script hasta que encuentre sus archivos o carpeta.  
       
   ![Menú Recuperación de archivos](./media/backup-azure-restore-files-from-vm/volumes-attached.png)
           
#### <a name="for-linux"></a>Para Linux

En Linux, los volúmenes del punto de recuperación se montan en la carpeta en que se ejecuta el script. Los discos conectados, los volúmenes y las rutas de acceso de montaje correspondientes se muestran según corresponda. Los usuarios con acceso en el nivel raíz pueden ver estas rutas de acceso de montaje. Examine los volúmenes mencionados en la salida del script.

  ![Menú Recuperación de archivos de Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)
  

## <a name="closing-the-connection"></a>Cierre de la conexión

Después de identificar los archivos y copiarlos en una ubicación de almacenamiento local, quite (o desmonte) las unidades adicionales. Para desmontar las unidades, en el menú **Recuperación de archivos** de Azure Portal, haga clic en **Desmontar discos**.

![Desmontar discos](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Cuando los discos estén desmontados, recibirá un mensaje que le informará de que la operación se ha realizado correctamente. Puede tardar unos minutos en actualizarse la conexión para que pueda quitar los discos.

En Linux, cuando se corta la conexión con el punto de recuperación, el sistema operativo no elimina las rutas de acceso de montaje correspondientes automáticamente. Las rutas de acceso de montaje adoptan la forma de volúmenes "huérfanos" y se pueden ver, pero se genera un error al acceder a los archivos o al escribir en ellos. Se pueden quitar manualmente. Cuando el script se ejecuta, este identifica todos los volúmenes existentes desde todos los puntos de recuperación anteriores y los limpia, aunque con consentimiento previo.

## <a name="special-configurations"></a>Configuraciones especiales

### <a name="dynamic-disks"></a>Discos dinámicos

Si la máquina virtual de Azure protegida tiene volúmenes con una o ambas de las siguientes características, no puede ejecutar el script ejecutable en la misma máquina virtual. 

  - Volúmenes que abarquen varios discos (volúmenes distribuidos y seccionados)
  - Volúmenes que toleren errores (volúmenes reflejados y RAID-5) en discos dinámicos 

En su lugar, ejecútelo en otro equipo que tenga un sistema operativo compatible.

### <a name="windows-storage-spaces"></a>Espacios de almacenamiento de Windows

Los espacios de Windows Storage son una tecnología de Windows que permite virtualizar el almacenamiento. Con los espacios de Windows Storage puede agrupar discos estándar del sector en grupos de almacenamiento. Después, use el espacio disponible en los grupos de almacenamiento para crear discos virtuales, denominados "espacios de almacenamiento".

Si la máquina virtual de Azure usa los espacios de Windows Storage, no podrá ejecutar el script ejecutable en la misma máquina virtual. En su lugar, ejecútelo en otra máquina que tenga un sistema operativo compatible.

### <a name="lvmraid-arrays"></a>Matrices LVM/RAID

En Linux, las matrices Logical Volume Manager (LVM)/RAID del software se usan para administrar volúmenes lógicos en varios discos. En caso de que la máquina virtual Linux protegida use las matrices LVM o RAID, no podrá ejecutar el script en la misma máquina virtual. En su lugar, ejecútelo en cualquier otra máquina que tenga un sistema operativo compatible y que admita el sistema de archivos de la máquina virtual protegida.

En la salida del script siguiente se muestran los volúmenes y los discos de las matrices LVM o RAID con el tipo de partición.

   ![Menú de salida de LVM en Linux](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)
   
Para poner en línea estas particiones, ejecute los comandos de las secciones siguientes. 

**Para las particiones de LVM**

Para enumerar los nombres de grupo de volúmenes en un volumen físico.
```
$ pvs <volume name as shown above in the script output> 
```
Para enumerar todos los volúmenes lógicos, los nombres y sus rutas de acceso en un grupo de volúmenes.

```
$ lvdisplay <volume-group-name from the pvs command’s results> 
```

Para montar los volúmenes lógicos en la ruta de acceso de su elección.

```
$ mount <LV path> </mountpath>
```



**Para las matrices RAID**

El siguiente comando muestra los detalles sobre todos los discos RAID.

```
$ mdadm –detail –scan
```
 El disco RAID pertinente se muestra como `/dev/mdm/<RAID array name in the protected VM>`.

Use el comando Montar si el disco RAID tiene volúmenes físicos.
```
$ mount [RAID Disk Path] [/mountpath]
```

Si el disco RAID tiene otra LVM configurada, utilice el procedimiento anterior para las particiones de LVM, pero con el nombre de volumen en lugar del nombre de disco RAID.

## <a name="troubleshooting"></a>Solución de problemas

Si tiene problemas al tratar de recuperar archivos de las máquinas virtuales, compruebe la siguiente tabla para obtener más información.

| Mensaje de error y escenario | Causa probable | Acción recomendada |
| ------------------------ | -------------- | ------------------ |
| Salida del ejecutable: *excepción relacionada con el destino*. |El script no puede acceder al punto de recuperación. | Compruebe si la máquina cumple los requisitos de acceso anteriores. |  
|   Salida del archivo ejecutable: *el destino ya ha iniciado sesión a través de una sesión iSCSI*. | El script ya se ejecutó en la misma máquina y se ha conectado la unidad. | Ya se han conectado los volúmenes del punto de recuperación. Es posible que NO se monten con las mismas letras de unidad de la máquina virtual original. Examine todos los volúmenes disponibles en el Explorador de archivos para buscar su archivo. |
| Salida del archivo ejecutable: *este script no es válido porque se han desmontado los discos a través del portal o se ha superado el límite de 12 horas. Descargue un nuevo script del portal.* | Se han desmontado los discos desde el portal o se ha superado el límite de 12 horas. |    Este archivo ejecutable ahora no es válido y no se puede ejecutar. Si desea acceder a los archivos de esa recuperación en un momento dado, visite el portal para descargar un nuevo archivo ejecutable.|
| En el equipo donde se ejecuta el archivo ejecutable: los nuevos volúmenes no se desmontan después de hacer clic en el botón Desmontar. |    El iniciador iSCSI de la máquina no responde ni actualiza su conexión con el destino ni mantiene la memoria caché. |    Espere algunos minutos después de hacer clic en el botón Desmontar. Si los nuevos volúmenes todavía no se han desmontado, examínelos todos. De este modo, el iniciador actualizará la conexión y se desmontará el volumen con un mensaje de error que indica que el disco no está disponible.|
| Salida del archivo ejecutable: el script se ejecuta correctamente, pero no se muestra en la salida del script el mensaje que indica que se han conectado nuevos volúmenes. | Se trata de un problema transitorio.   | Los volúmenes ya deberían estar conectados. Abra el Explorador para examinarlos. Si usa siempre la misma máquina para ejecutar scripts, considere la posibilidad de reiniciarla; debería mostrarse la lista en las ejecuciones posteriores del ejecutable. |
| Específico de Linux: no se pueden ver los volúmenes deseados. | El sistema operativo de la máquina en que se ejecuta el script puede no reconocer el sistema de archivos subyacente de la máquina virtual protegida. | Compruebe si el punto de recuperación es coherente frente a bloqueos o coherente con archivo. En caso de coherencia con archivo, ejecute el script en otra máquina cuyo sistema operativo reconozca el sistema de archivos de la máquina virtual protegida. |
| Específico de Windows: no se pueden ver los volúmenes deseados | Se han adjuntado los discos, pero no se han configurado los volúmenes | En la pantalla de administración de discos, identifique los discos adicionales relacionados con el punto de recuperación. Si alguno de estos discos está sin conexión, intente ponerlo en línea haciendo clic con el botón derecho en él y, luego, haciendo clic en 'En línea'|
