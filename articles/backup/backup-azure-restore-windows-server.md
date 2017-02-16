---
title: "Restauración de datos de Azure en un equipo de Windows Server o Windows | Microsoft Docs"
description: "Obtenga información sobre cómo restaurar los datos almacenados en Azure en un equipo de Windows Server o Windows."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 742f4b9e-c0ab-4eeb-8e22-ee29b83c22c4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/30/2017
ms.author: trinadhk;jimpark;markgal;
translationtype: Human Translation
ms.sourcegitcommit: bb07ff13e3e4023e334c706d5f9bd95196ee6051
ms.openlocfilehash: 47c5789fc3379b702d8f6ceafd630bece669b11e


---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>Restauración de archivos en un equipo de Windows Server o cliente de Windows mediante el modelo de implementación de Resource Manager
> [!div class="op_single_selector"]
> * [Portal de Azure](backup-azure-restore-windows-server.md)
> * [Portal clásico](backup-azure-restore-windows-server-classic.md)
>
>

En este artículo se describen los pasos necesarios para realizar dos tipos de operaciones de restauración:

* La restauración de datos en la misma máquina desde la cual se realizaron las copias de seguridad.
* La restauración de datos en cualquier otra máquina.

En ambos casos, los datos se recuperan del almacén de Servicios de recuperación de Azure.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-restore-data-to-the-same-machine"></a>Uso de restauración instantánea para restaurar en la misma máquina
Si ha eliminado accidentalmente un archivo y desea restaurarlo en la misma máquina (desde la que se realizó la copia de seguridad), los pasos siguientes le ayudarán a recuperar esos datos.

1. Abra el complemento **Copia de seguridad de Microsoft Azure** . Si no conoce la ubicación donde se instaló el complemento, busque el equipo o servidor para **Microsoft Azure Backup**.

    La aplicación de escritorio debe aparecer en los resultados de búsqueda.

2. Haga clic en **Recuperar datos** para iniciar el asistente.

    ![Recuperar datos](./media/backup-azure-restore-windows-server/recover.png)

3. En el panel **Introducción**, para restaurar los datos en el mismo servidor o equipo, seleccione **Este servidor (`<server name>`)** y haga clic en **Siguiente**.

    ![Elegir la opción Este servidor para restaurar los datos en la misma máquina](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. En el panel **Seleccionar modo de recuperación**, elija si desea restaurar:
    - **Archivos y carpetas individuales**: utilice esta opción si desea restaurar archivos y carpetas específicos.
    - **Volumen**: utilice esta opción para restaurar todos los archivos y carpetas en el volumen seleccionado.

    y haga clic en **Siguiente**.

    ![Examinar archivos](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)

5. En el panel **Seleccionar volumen y fecha**, seleccione el volumen que contiene los archivos y/o carpetas que desea restaurar.

    En el calendario, seleccione un punto de recuperación. Puede realizar la restauración desde cualquier momento dado de recuperación. Las fechas en **negrita** indican la disponibilidad de al menos un punto de recuperación. Cuando selecciona una fecha, si están disponibles varios puntos de recuperación, elija el punto de recuperación específico desde el menú desplegable **Hora**.

    ![Fecha y volumen](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Una vez que haya elegido el punto de recuperación para restaurar, haga clic en **Montar**.

    Azure Backup monta el punto de recuperación local y lo usa como volumen de recuperación.

7. En el panel **Examinar y recuperar archivos**, haga clic en **Examinar** para abrir el Explorador de Windows y buscar los archivos y carpetas que desea.

    ![Opciones de recuperación](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

   * Tiene la opción de restaurar en la ubicación original (en la que la carpeta o el archivos se sobrescribirán) o en otra ubicación en la misma máquina.
   * Si el archivo o carpeta que desea restaurar existe en la ubicación de destino, tiene la opción de crear copias (dos versiones del mismo archivo), sobrescribir los archivos en la ubicación de destino u omitir la recuperación de los archivos que existen en el destino.
   * Se recomienda que deje la opción predeterminada de la restauración de las ACL en los archivos que se está recuperando.
8. En el Explorador de Windows, copie los archivos y/o carpetas que desea restaurar y péguelos en cualquier ubicación local en el servidor o equipo. Puede abrir o transmitir los archivos directamente desde el volumen de recuperación y comprobar que se recuperan las versiones correctas.

    ![Copia y pegado de archivos y carpetas desde el volumen montado en la ubicación local](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Cuando termine de restaurar los archivos y/o carpetas, en el panel **Examinar y recuperar archivos**, haga clic en **Desmontar**. Haga clic en **Sí** para confirmar que desea desmontar el volumen.

    ![Desmontaje del volumen y confirmación](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Si no hace clic en Desmontar, el volumen de recuperación permanecerá montado durante seis horas desde el momento en el que se montó. No se ejecutará ninguna operación de copia de seguridad mientras el volumen esté montado. Cualquier operación de copia de seguridad programada para ejecutarse durante el tiempo en el que el volumen está montado, se ejecutará después de desmontar el volumen de recuperación.
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Uso de restauración instantánea para restaurar datos en una máquina alternativa
Si ha perdido todo el servidor, todavía puede recuperar los datos de la Copia de seguridad de Azure en una máquina diferente. Los pasos siguientes muestran el flujo de trabajo.

> [!NOTE]
> Se necesita la [actualización de enero de 2017 de Azure Backup](https://support.microsoft.com/en-us/help/3216528?preview) si desea utilizar la restauración instantánea para restaurar datos en una máquina alternativa. También se deben proteger los datos de copia de seguridad en los almacenes en configuraciones regionales que aparecen en el artículo de soporte técnico. La restauración instantánea no está actualmente disponible en todas las configuraciones regionales.
>

La terminología usada en estos pasos incluye:

* *Máquina de origen* : es la máquina original desde la que se realizó la copia de seguridad y que no está disponible actualmente.
* *Máquina de destino* : es la máquina en la que se recuperan los datos.
* *Almacén de ejemplo*: el almacén de Recovery Services en el que se registran la*máquina de origen* y la *máquina de destino*. <br/>

> [!NOTE]
> Las copias de seguridad no se pueden restaurar en una máquina de destino en la que se ejecuta una versión anterior del sistema operativo. Por ejemplo, una copia de seguridad perteneciente a un equipo con Windows 7 se puede restaurar en un equipo con Windows 8 o posterior. Una copia de seguridad perteneciente a un equipo con Windows 8 no se puede restaurar en un equipo con Windows 7.
>
>

1. Abra el complemento **Copia de seguridad de Microsoft Azure** en la *Máquina de destino*.

2. Asegúrese de que tanto la *máquina de destino* como la *máquina de origen* están registradas en el mismo almacén de Recovery Services.

3. Haga clic en **Recuperar datos** para abrir el **Asistente de recuperación de datos**.

    ![Recuperar datos](./media/backup-azure-restore-windows-server/recover.png)

4. En el panel **Introducción**, seleccione **Otro servidor**.

    ![Otro servidor](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Proporcione el archivo de credenciales de almacén que se corresponde con el *Almacén de ejemplo* y haga clic en **Siguiente**.

    Si el archivo de credenciales de almacén no es válido (o ha expirado), descargue un nuevo archivo de credenciales de almacén desde el *Almacén de ejemplo* en Azure Portal. Cuando proporcione una credencial de almacén válida, aparecerá el nombre del almacén de copia de seguridad correspondiente.

6. En el panel **Seleccionar servidor de copia de seguridad**, seleccione la *máquina de origen* en la lista de máquinas mostradas y proporcione la frase de contraseña. A continuación, haga clic en **Siguiente**.

    ![Lista de máquinas](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. En el panel **Seleccionar modo de recuperación**, seleccione **Archivos y carpetas individuales** y haga clic en **siguiente**.

    ![Search](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. En el panel **Seleccionar volumen y fecha**, seleccione el volumen que contiene los archivos y/o carpetas que desea restaurar.

    En el calendario, seleccione un punto de recuperación. Puede realizar la restauración desde cualquier momento dado de recuperación. Las fechas en **negrita** indican la disponibilidad de al menos un punto de recuperación. Cuando selecciona una fecha, si están disponibles varios puntos de recuperación, elija el punto de recuperación específico desde el menú desplegable **Hora**.

    ![Buscar artículos](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Haga clic en **Montar** para montar localmente el punto de recuperación como un volumen de recuperación en la *máquina de destino*.

10. En el panel **Examinar y recuperar archivos**, haga clic en **Examinar** para abrir el Explorador de Windows y buscar los archivos y carpetas que desea.

    ![Cifrado](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. En el Explorador de Windows, copie los archivos y/o carpetas desde el volumen de recuperación y péguelos en la ubicación *Máquina de destino*. Puede abrir o transmitir los archivos directamente desde el volumen de recuperación y comprobar que se recuperan las versiones correctas.

    ![Cifrado](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Cuando termine de restaurar los archivos y/o carpetas, en el panel **Examinar y recuperar archivos**, haga clic en **Desmontar**. Haga clic en **Sí** para confirmar que desea desmontar el volumen.

    ![Cifrado](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Si no hace clic en Desmontar, el volumen de recuperación permanecerá montado durante seis horas desde el momento en el que se montó. No se ejecutará ninguna operación de copia de seguridad mientras el volumen esté montado. Cualquier operación de copia de seguridad programada para ejecutarse durante el tiempo en el que el volumen está montado, se ejecutará después de desmontar el volumen de recuperación.
    >

## <a name="next-steps"></a>Pasos siguientes
* Ahora que ha recuperado los archivos y las carpetas, puede [administrar las copias de seguridad](backup-azure-manage-windows-server.md).



<!--HONumber=Jan17_HO5-->


