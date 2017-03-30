---
title: "Copia de seguridad de Azure: Recuperación de archivos y carpetas desde una copia de seguridad de máquina virtual de Azure | Microsoft Docs"
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
ms.date: 2/6/2017
ms.author: pullabhk;markgal
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 873c64dfbd4ad6ced9e5a9eeb80d7ad6dbc558a6
ms.lasthandoff: 03/17/2017


---
# <a name="recover-files-from-azure-virtual-machine-backup-preview"></a>Recuperación de archivos desde una copia de seguridad de máquina virtual de Azure (versión preliminar)

La copia de seguridad de Azure ofrece la posibilidad de restaurar [discos y máquinas virtuales de Azure](./backup-azure-arm-restore-vms.md) desde copias de seguridad de máquina virtual de Azure. En este artículo explicaremos cómo recuperar elementos como archivos y carpetas desde una copia de seguridad de máquina virtual de Azure.

> [!Note]
> Esta característica está disponible en las máquinas virtuales de Azure implementadas con el modelo de Resource Manager y protegidas en un almacén de Recovery Services.
> No se pueden recuperar archivos a partir de una copia de seguridad de máquina virtual cifrada.
>

## <a name="mount-the-volume-and-copy-files"></a>Montaje del volumen y copia de archivos

1. Inicie sesión en el [Portal de Azure](http://portal.Azure.com). Busque el almacén de Recovery Services pertinente y el elemento requerido de copia de seguridad.

2. En la hoja Elemento de copia de seguridad, haga clic en **Recuperación de archivos (versión preliminar)**.

    ![Apertura del elemento de copia de seguridad del almacén de Recovery Services](./media/backup-azure-restore-files-from-vm/open-vault-item.png)

    Se abrirá la hoja **Recuperación de archivos**.

    ![Hoja Recuperación de archivos](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

3. En el menú desplegable **Seleccionar punto de recuperación**, elija el punto de recuperación que contiene los archivos que desee. De forma predeterminada, el punto de recuperación más reciente ya está seleccionado.

4. Haga clic en **Download Executable** (Descargar ejecutable) para máquinas virtuales de Azure con Windows o **Download Script** (Descargar script) para máquinas virtuales de Azure con Linux, a fin de descargar el software que va a usar para copiar archivos del punto de recuperación.

  El archivo ejecutable/script crea una conexión entre el equipo local y el punto de recuperación especificado.

5. En el equipo donde desea recuperar los archivos, ejecute el archivo ejecutable o el script. La ejecución debe realizarla con credenciales de administrador. Si lo hace en un equipo con acceso restringido, asegúrese de que hay acceso a los siguientes recursos:

    - go.microsoft.com
    - Puntos de conexión de Azure usados para copias de seguridad de máquina virtual de Azure
    - Puerto de salida 3260

   En el caso de Linux, el script requiere los componentes "open-iscsi" e "lshw" para conectar con el punto de recuperación. Si no se encuentran en la máquina en que se realiza la ejecución, solicita permiso para instalar los componentes pertinentes, pero no los instala hasta no haber obtenido el consentimiento.
      
    ![Hoja Recuperación de archivos](./media/backup-azure-restore-files-from-vm/executable-output.png)
    
   
   Puede ejecutar el script en cualquier máquina que tenga el mismo sistema operativo (o uno compatible) que la máquina virtual de la que se realiza la copia de seguridad. Consulte la [tabla de sistemas operativos compatibles](backup-azure-restore-files-from-vm.md#compatible-os) para ver cuáles son. Si la máquina virtual de Azure protegida usa espacios de almacenamiento de Windows (para máquinas virtuales de Azure con Windows) o las matrices LVM/RAID (para máquinas virtuales con Linux), no puede ejecutar el archivo ejecutable ni el script en la misma máquina virtual. En su lugar, ejecútelo en otra máquina que tenga un sistema operativo compatible.

### <a name="compatible-os"></a>Sistemas operativos compatibles

#### <a name="for-windows"></a>Para Windows

En la siguiente tabla se muestra la compatibilidad entre los sistemas operativos de servidor y equipo. Al recuperar archivos, no podrá restaurar archivos entre sistemas operativos incompatibles.

|Sistema operativo de servidor | Sistema operativo de cliente compatible  |
| --------------- | ---- |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

#### <a name="for-linux"></a>Para Linux

En Linux, el requisito indispensable es que el sistema operativo de la máquina en que se va a ejecutar el script admita el sistema de archivos de los archivos existentes en la máquina virtual Linux de la que se va a realizar la copia de seguridad. Cuando seleccione una máquina para ejecutar el script, asegúrese de que tenga el sistema operativo y las versiones compatibles, tal y como se menciona en la tabla siguiente.

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

Al ejecutar el archivo ejecutable, el sistema operativo monta los nuevos volúmenes y asigna letras de unidad. Puede usar el Explorador de Windows o el Explorador de archivos para buscar esas unidades. Las letras de unidad asignadas a los volúmenes no pueden ser las mismas que las de la máquina virtual original; sin embargo, se conserva el nombre del volumen. Por ejemplo, si el volumen de la máquina virtual original era "Disco de datos (E:\)", ese volumen puede estar conectado como "Disco de datos ('cualquier letra de unidad disponible':\)" en el equipo local. Busque en todos los volúmenes que se mencionan en la salida del script hasta que encuentre sus archivos o carpeta.  
       
   ![Hoja Recuperación de archivos](./media/backup-azure-restore-files-from-vm/volumes-attached.png)
           
#### <a name="for-linux"></a>Para Linux

En Linux, los volúmenes del punto de recuperación se montan en la carpeta en que se ejecuta el script. Los discos conectados, los volúmenes y las rutas de acceso de montaje correspondientes se muestran según corresponda. Los usuarios con acceso en el nivel raíz pueden ver estas rutas de acceso de montaje. Examine los volúmenes mencionados en la salida del script.

  ![Hoja de recuperación de archivos de Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)
  

## <a name="closing-the-connection"></a>Cierre de la conexión

Después de identificar los archivos y copiarlos en una ubicación de almacenamiento local, quite (o desmonte) las unidades adicionales. Para desmontar las unidades, en la hoja **Recuperación de archivos** de Azure Portal, haga clic en **Desmontar discos**.

![Desmontar discos](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Cuando los discos estén desmontados, recibirá un mensaje que le informará de que la operación se ha realizado correctamente. Puede tardar unos minutos en actualizarse la conexión para que pueda quitar los discos.

En Linux, cuando se corta la conexión con el punto de recuperación, el sistema operativo no elimina las rutas de acceso de montaje correspondientes automáticamente. Adoptan la forma de volúmenes "huérfanos" y se pueden ver, pero se genera un error al acceder a los archivos o al escribir en ellos. Se pueden quitar manualmente. Cuando el script se ejecuta, este identifica todos los volúmenes existentes desde todos los puntos de recuperación anteriores y los limpia, aunque con consentimiento previo.

## <a name="special-configurations"></a>Configuraciones especiales

### <a name="windows-storage-spaces"></a>Espacios de almacenamiento de Windows

Los espacios de almacenamiento de Windows son una tecnología de almacenamiento de Windows que permite virtualizar un almacenamiento. Gracias a ellos, puede agrupar discos estándar del sector en grupos de almacenamiento y, luego, crear discos virtuales (denominados "espacios de almacenamiento") en el espacio disponible de esos grupos de almacenamiento.

Si la máquina virtual de Azure de la que se ha realizado una copia de seguridad usa espacios de almacenamiento de Windows, no podrá ejecutar el script ejecutable en la misma máquina virtual. En su lugar, ejecútelo en otra máquina que tenga un sistema operativo compatible.

### <a name="lvmraid-arrays"></a>Matrices LVM/RAID

En Linux, las matrices Logical Volume Manager (LVM)/RAID del software se usan para administrar volúmenes lógicos en varios discos. En caso de que la máquina virtual con Linux de la que se realiza la copia de seguridad use las matrices LVM o RAID, no puede ejecutar el script en la misma máquina virtual. En su lugar, ejecútelo en cualquier otra máquina que tenga un sistema operativo compatible y que admita el sistema de archivos de la máquina virtual de la que se realiza la copia de seguridad.

En la salida del script se muestran los volúmenes y los discos de las matrices LVM o RAID con el tipo de partición, tal y como se observa a continuación:

   ![Hoja de salida de LVM en Linux](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)
   
El usuario necesita ejecutar los comandos siguientes para colocar estas particiones en línea. 

**Para las particiones de LVM**

```
$ pvs <volume name as shown above in the script output> 
```
Muestra los nombres de grupo de volúmenes en un volumen físico.

```
$ lvdisplay <volume-group-name from the pvs command’s results> 
```
Muestra todos los volúmenes lógicos, los nombres y sus rutas de acceso en un grupo de volúmenes.

```
$ mount <LV path> </mountpath>
```
Para montar los volúmenes lógicos en la ruta de acceso de su elección.


**Para las matrices RAID**

```
$ mdadm –detail –scan
```
Muestra los detalles sobre todos los discos RAID. El disco RAID pertinente se mostrará como `/dev/mdm/<RAID array name in the backed up VM>`.

Use el comando Montar si el disco RAID tiene volúmenes físicos.
```
$ mount [RAID Disk Path] [/mounthpath]
```

Si este disco RAID tiene otra matriz LVM configurada en él, siga el mismo procedimiento descrito anteriormente para las particiones de LVM, donde el nombre de volumen debe coincidir con el nombre del disco RAID.

## <a name="troubleshooting"></a>Solución de problemas

Si tiene problemas al tratar de recuperar archivos de las máquinas virtuales, compruebe la siguiente tabla para obtener más información.

| Mensaje de error y escenario | Causa probable | Acción recomendada |
| ------------------------ | -------------- | ------------------ |
| Salida del ejecutable: *excepción relacionada con el destino*. |El script no puede acceder al punto de recuperación.    | Compruebe si la máquina cumple los requisitos de acceso mencionados anteriormente.|  
|    Salida del archivo ejecutable: *el destino ya ha iniciado sesión a través de una sesión iSCSI*. |    El script ya se ejecutó en la misma máquina y se ha conectado la unidad. |    Ya se han conectado los volúmenes del punto de recuperación. Es posible que NO se monten con las mismas letras de unidad de la máquina virtual original. Examine todos los volúmenes disponibles en el Explorador de archivos para buscar su archivo. |
| Salida del archivo ejecutable: *este script no es válido porque se han desmontado los discos a través del portal o se ha superado el límite de 12 horas. Descargue un nuevo script del portal.* |    Se han desmontado los discos desde el portal o se ha superado el límite de 12 horas. |    Este archivo ejecutable ahora no es válido y no se puede ejecutar. Si desea acceder a los archivos de esa recuperación en un momento dado, visite el portal para descargar un nuevo archivo ejecutable.|
| En el equipo donde se ejecuta el archivo ejecutable: los nuevos volúmenes no se desmontan después de hacer clic en el botón Desmontar. |    El iniciador iSCSI de la máquina no responde ni actualiza su conexión con el destino ni mantiene la memoria caché. |    Espere algunos minutos después de hacer clic en el botón Desmontar. Si los nuevos volúmenes todavía no se han desmontado, examínelos todos. De este modo, el iniciador actualizará la conexión y se desmontará el volumen con un mensaje de error que indica que el disco no está disponible.|
| Salida del archivo ejecutable: el script se ejecuta correctamente, pero no se muestra en la salida del script el mensaje que indica que se han conectado nuevos volúmenes. |    Se trata de un problema transitorio.    | Los volúmenes ya deberían estar conectados. Abra el Explorador para examinarlos. Si usa siempre la misma máquina para ejecutar scripts, considere la posibilidad de reiniciarla; debería mostrarse la lista en las ejecuciones posteriores del ejecutable. |
| Específico de Linux: no se pueden ver los volúmenes deseados. | El sistema operativo de la máquina en que se ejecuta el script puede no reconocer el sistema de archivos subyacente de la máquina virtual de la que se hace la copia de seguridad. | Compruebe si el punto de recuperación es coherente frente a bloqueos o coherente con archivo. En caso de coherencia con archivo, ejecute el script en otra máquina cuyo sistema operativo reconozca el sistema de archivos de la máquina virtual de la que se hace la copia de seguridad. |

