---
title: "Recuperación de archivos de Azure en Windows Server | Microsoft Docs"
description: "En este tutorial se detallan los elementos de recuperación de Azure en Windows Server."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
keywords: "copia de seguridad de Windows Server; restaurar archivos Windows Server; copia de seguridad y recuperación ante desastres"
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2017
ms.author: saurabhsensharma;markgal;
ms.custom: mvc
ms.openlocfilehash: b5f77ec04ef6d267583a6dc6a4476f118a4d0f74
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Recuperación de archivos de Azure en Windows Server

Azure Backup permite la recuperación de elementos individuales de las copias de seguridad de Windows Server. La recuperación de archivos individuales es útil si necesita restaurar rápidamente archivos que se eliminaron accidentalmente. En este tutorial se explica cómo puede usar al agente Microsoft Azure Recovery Services (MARS) para recuperar elementos de copias de seguridad realizadas previamente en Azure. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Iniciar la recuperación de elementos individuales 
> * Seleccionar un punto de recuperación 
> * Restaurar elementos desde un punto de recuperación

En este tutorial se da por supuesto que ya ha realizado los pasos necesarios para [realizar la copia de seguridad de Windows Server en Azure](backup-configure-vault.md) y que tiene al menos una copia de seguridad de los archivos de Windows Server en Azure.

## <a name="initiate-recovery-of-individual-items"></a>Iniciar la recuperación de elementos individuales

Un práctico asistente de interfaz de usuario denominado Microsoft Azure Backup se instala con el agente de Microsoft Azure Recovery Services (MARS). El asistente Microsoft Azure Backup funciona con el agente de Microsoft Azure Recovery Services (MARS) para recuperar datos de copia de seguridad desde los puntos de recuperación almacenados en Azure. Use el asistente Microsoft Azure Backup para identificar los archivos o las carpetas que quiere restaurar en Windows Server. 

1. Abra el complemento **Microsoft Azure Backup**. Para encontrarlo, busque **Microsoft Azure Backup**en la máquina.

    ![Copia de seguridad pendiente](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. En el asistente, haga clic en **Recuperar datos** en el **panel Acciones** de la consola del agente para iniciar el **Asistente de recuperación de datos**.

    ![Copia de seguridad pendiente](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. En la página **Introducción**, seleccione **Este servidor (nombre del servidor)** y haga clic en **Siguiente**.

4. En la página **Seleccionar modo de recuperación**, seleccione **Archivos y carpetas individuales** y, a continuación, haga clic en **Siguiente** para iniciar el proceso de selección del punto de recuperación.
 
5. En la página **Seleccionar volumen y fecha**, seleccione el volumen que contiene los archivos o las carpetas que desea restaurar y haga clic en **Montar**. Seleccione una fecha y, luego, elija una hora en el menú desplegable correspondiente a un punto de recuperación. Las fechas en **negrita** indican la disponibilidad de al menos un punto de recuperación ese día.

    ![Copia de seguridad pendiente](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)
 
    Al hacer clic en **Montar**, Azure Backup hace que el punto de recuperación esté disponible como un disco. Busque y recupere los archivos del disco.

## <a name="restore-items-from-a-recovery-point"></a>Restaurar elementos desde un punto de recuperación

1. Una vez montado el volumen de recuperación, haga clic en **Examinar** para abrir el Explorador de Windows y buscar los archivos y carpetas que desea. 

    ![Copia de seguridad pendiente](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    Puede abrir los archivos directamente desde el volumen de recuperación, así como comprobarlos.

2. En el Explorador de Windows, copie los archivos y/o carpetas que desea restaurar y péguelos en cualquier ubicación que desee en el servidor.

    ![Copia de seguridad pendiente](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. Cuando termine de restaurar los archivos y/o carpetas, en la página **Examinar y recuperar archivos** del **Asistente de recuperación de datos**, haga clic en **Desmontar**. 

    ![Copia de seguridad pendiente](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4.  Haga clic en **Sí** para confirmar que desea desmontar el volumen.

    Una vez desmontada la instantánea, aparece **Trabajo completado** en el panel **Trabajos** de la consola del agente.

## <a name="next-steps"></a>Pasos siguientes

Con esto finalizan los tutoriales sobre la copia de seguridad y la restauración de datos de Windows Server en Azure. Para obtener más información acerca de Azure Backup, consulte el ejemplo de PowerShell sobre la copia de seguridad de máquinas virtuales cifradas.

> [!div class="nextstepaction"]
> [Copia de seguridad de máquinas virtuales cifradas](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
