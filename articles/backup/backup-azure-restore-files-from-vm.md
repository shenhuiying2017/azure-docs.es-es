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
ms.sourcegitcommit: 5c31fcac727a1206cb416cbe7340392a2816729a
ms.openlocfilehash: 93b689c132ec7e391a9107e4e3eb73a48e3ba09d


---
# <a name="recover-files-from-azure-virtual-machine-backup-preview"></a>Recuperación de archivos desde una copia de seguridad de máquina virtual de Azure (versión preliminar)

La copia de seguridad de Azure ofrece la posibilidad de restaurar [discos y máquinas virtuales de Azure](./backup-azure-arm-restore-vms.md) desde copias de seguridad de máquina virtual de Azure. En este artículo explicaremos cómo recuperar elementos como archivos y carpetas desde una copia de seguridad de máquina virtual de Azure.

> [!Note]
> Esta característica está disponible en las máquinas virtuales de Azure implementadas con el modelo de Resource Manager y protegidas en un almacén de Recovery Services.
> Actualmente, la recuperación de archivos desde una copia de seguridad de máquina virtual de Azure en Windows se encuentra en fase de versión preliminar. Próximamente, se podrán recuperar archivos desde una máquina virtual de Azure en Linux.
En estos momentos, no se pueden recuperar archivos desde una copia de seguridad de máquina virtual cifrada.
>

## <a name="mount-the-volume-and-copy-files"></a>Montaje del volumen y copia de archivos

1. Inicie sesión en el [Portal de Azure](http://portal.Azure.com). Busque el almacén de Recovery Services pertinente y el elemento requerido de copia de seguridad.

2. En la hoja Elemento de copia de seguridad, haga clic en **Recuperación de archivos (versión preliminar)**.

    ![Apertura del elemento de copia de seguridad del almacén de Recovery Services](./media/backup-azure-restore-files-from-vm/open-vault-item.png)

    Se abrirá la hoja **Recuperación de archivos**.

    ![Hoja Recuperación de archivos](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

3. En el menú desplegable **Seleccionar punto de recuperación**, elija el punto de recuperación que contiene los archivos que desee. De forma predeterminada, el punto de recuperación más reciente ya está seleccionado.

4. Haga clic en **Download Executable** (Descargar ejecutable) para descargar el software que va a usar para copiar archivos del punto de recuperación.

  El archivo ejecutable es un script que crea una conexión entre el equipo local y el punto de recuperación especificado.

5. En el equipo donde desea recuperar los archivos, ejecute el archivo ejecutable. Debe ejecutar el script con credenciales de administrador. Si lo hace en un equipo con acceso restringido, asegúrese de que hay acceso a los siguientes recursos:

    - go.microsoft.com
    - Puntos de conexión de Azure usados para copias de seguridad de máquina virtual de Azure
    - Puerto de salida 3260

    ![Hoja Recuperación de archivos](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Puede ejecutar el script en cualquier máquina que tenga el mismo sistema operativo (o uno compatible) que el de la máquina usada para generar el punto de recuperación. Consulte la [tabla de sistemas operativos compatibles](backup-azure-restore-files-from-vm.md#compatible-os) para ver cuáles son. Si la máquina virtual de Azure protegida usa espacios de almacenamiento de Windows, no podrá ejecutar el script ejecutable en esta máquina virtual. En su lugar, ejecútelo en otra máquina que use los espacios de almacenamiento de Windows. Se recomienda ejecutar el script ejecutable en un equipo con un sistema operativo compatible.

    ![Hoja Recuperación de archivos](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

### <a name="compatible-os"></a>Sistemas operativos compatibles

En la siguiente tabla se muestra la compatibilidad entre los sistemas operativos de servidor y equipo. Al recuperar archivos, no podrá restaurar archivos entre sistemas operativos incompatibles.

|Sistema operativo de servidor | Sistema operativo de cliente compatible  |
| --------------- | ---- |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |


### <a name="drive-letter-assignments"></a>Asignaciones de letras de unidad

Cuando ejecute el script, el sistema operativo monta nuevos volúmenes y asigna letras de unidad. Puede usar el Explorador de Windows o el Explorador de archivos para buscar esas unidades. Las letras de unidad asignadas a los volúmenes no pueden ser las mismas que las de la máquina virtual original; sin embargo, se conserva el nombre del volumen. Por ejemplo, si el volumen de la máquina virtual original era "Disco de datos (E:\)", ese volumen puede estar conectado como "Disco de datos ('cualquier letra de unidad disponible':\)" en el equipo local. Busque en todos los volúmenes que se mencionan en la salida del script hasta que encuentre sus archivos o carpeta.  


## <a name="closing-the-connection"></a>Cierre de la conexión

Después de identificar los archivos y copiarlos en una ubicación de almacenamiento local, quite (o desmonte) las unidades adicionales. Para desmontar las unidades, en la hoja **Recuperación de archivos** de Azure Portal, haga clic en **Desmontar discos**.

![Desmontar discos](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Cuando los discos estén desmontados, recibirá un mensaje que le informará de que la operación se ha realizado correctamente. Puede tardar unos minutos en actualizarse la conexión para que pueda quitar los discos.

## <a name="windows-storage-spaces"></a>Espacios de almacenamiento de Windows

Los espacios de almacenamiento de Windows son una tecnología de almacenamiento de Windows que permite virtualizar un almacenamiento. Gracias a ellos, puede agrupar discos estándar del sector en grupos de almacenamiento y, luego, crear discos virtuales (denominados "espacios de almacenamiento") en el espacio disponible de esos grupos de almacenamiento.

Si la máquina virtual de Azure de la que se ha realizado una copia de seguridad usa espacios de almacenamiento de Windows, no podrá ejecutar el script ejecutable en esta máquina virtual. En su lugar, ejecútelo en otra máquina que use los espacios de almacenamiento de Windows. Se recomienda ejecutar el script ejecutable en un equipo con un sistema operativo compatible.

## <a name="troubleshooting"></a>Solución de problemas

Si tiene problemas al tratar de recuperar archivos de las máquinas virtuales, compruebe la siguiente tabla para obtener más información.

| Mensaje de error y escenario | Causa probable | Acción recomendada |
| ------------------------ | -------------- | ------------------ |
| Salida del ejecutable: *excepción relacionada con el destino*. |El script no puede acceder al punto de recuperación.    | Compruebe si la máquina cumple los requisitos de acceso mencionados anteriormente.|  
|    Salida del archivo ejecutable: *el destino ya ha iniciado sesión a través de una sesión iSCSI*. |    El script ya se ejecutó en la misma máquina y se ha conectado la unidad. |    Ya se han conectado los volúmenes del punto de recuperación. Es posible que NO se monten con las mismas letras de unidad de la máquina virtual original. Examine todos los volúmenes disponibles en el Explorador de archivos para buscar su archivo. |
| Salida del archivo ejecutable: *este script no es válido porque se han desmontado los discos a través del portal o se ha superado el límite de 12 horas. Descargue un nuevo script del portal.* |    Se han desmontado los discos desde el portal o se ha superado el límite de 12 horas. |    Este archivo ejecutable ahora no es válido y no se puede ejecutar. Si desea acceder a los archivos de esa recuperación en un momento dado, visite el portal para descargar un nuevo archivo ejecutable.|
| En el equipo donde se ejecuta el archivo ejecutable: los nuevos volúmenes no se desmontan después de hacer clic en el botón Desmontar. |    El iniciador iSCSI de la máquina no responde ni actualiza su conexión con el destino ni mantiene la memoria caché. |    Espere algunos minutos después de hacer clic en el botón Desmontar. Si los nuevos volúmenes todavía no se han desmontado, examínelos todos. De este modo, el iniciador actualizará la conexión y se desmontará el volumen con un mensaje de error que indica que el disco no está disponible.|
| Salida del archivo ejecutable: el script se ejecuta correctamente, pero no se muestra en la salida del script el mensaje que indica que se han conectado nuevos volúmenes. |    Se trata de un problema transitorio.    | Los volúmenes ya deberían estar conectados. Abra el Explorador para examinarlos. Si usa siempre la misma máquina para ejecutar scripts, considere la posibilidad de reiniciarla; debería mostrarse la lista en las ejecuciones posteriores del ejecutable. |



<!--HONumber=Feb17_HO1-->


