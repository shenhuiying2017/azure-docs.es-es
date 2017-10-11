---
title: "Restauración de datos en Windows Server o un cliente de Windows desde Azure mediante el modelo de implementación clásica| Microsoft Docs"
description: Aprenda a restaurar desde Windows Server o desde el Cliente de Windows.
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 85585dfc-c764-4e8c-8f0e-40b969640ac2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: saurse;trinadhk;markgal;
ms.openlocfilehash: 300b2b17b44e21ed446fd63d572a2461e2fc1343
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-the-classic-deployment-model"></a>Restauración de archivos en un equipo de Windows Server o cliente de Windows mediante el modelo de implementación de clásica
> [!div class="op_single_selector"]
> * [Portal clásico](backup-azure-restore-windows-server-classic.md)
> * [Portal de Azure](backup-azure-restore-windows-server.md)
>
>

En este artículo se explica cómo recuperar datos de un almacén de Backup y restaurarlos en un servidor o equipo. A partir de marzo de 2017, no se podrán crear almacenes de Backup en el portal clásico.

> [!IMPORTANT]
> Ahora puede actualizar los almacenes de Backup a almacenes de Recovery Services. Para más información, consulte el artículo [Actualización de un almacén de Backup a un almacén de Recovery Services](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft anima a actualizar los almacenes de Backup a almacenes de Recovery Services.<br/> **15 de octubre de 2017**, ya no podrá usar PowerShell para crear almacenes de Backup. <br/> **A partir del 1 de noviembre de 2017**:
>- Los almacenes de Backup restantes se actualizarán automáticamente a almacenes de Recovery Services.
>- No podrá acceder a los datos de copia de seguridad en el portal clásico. En su lugar, utilice Azure Portal para tener acceso a los datos de copia de seguridad en los almacenes de Recovery Services.
>

Para restaurar datos, utilice al Asistente para recuperar datos del agente de Microsoft Azure Recovery Services (MARS). Al restaurar datos, es posible realizar las siguientes tareas:

* La restauración de datos en la misma máquina desde la cual se realizaron las copias de seguridad.
* Restaurar datos en una máquina alternativa.

En enero de 2017, Microsoft publicó una actualización de versión preliminar para el agente de MARS. Además de las correcciones de errores, esta actualización permite restaurar de forma instantánea, lo que permite montar una instantánea de punto de recuperación grabable como un volumen de recuperación. Después, puede explorar el volumen y copiar archivos de recuperación en un equipo local, por tanto, restaurando los archivos de forma selectiva.

> [!NOTE]
> Se necesita la [actualización de enero de 2017 de Azure Backup](https://support.microsoft.com/en-us/help/3216528?preview) si desea utilizar la restauración instantánea para restaurar datos. También se deben proteger los datos de copia de seguridad en los almacenes en configuraciones regionales que aparecen en el artículo de soporte técnico. Consulte la [actualización de Azure Backup de enero de 2017](https://support.microsoft.com/en-us/help/3216528?preview) para obtener la lista más reciente de configuraciones regionales que admitan la restauración instantánea. La restauración instantánea **no** está actualmente disponible en todas las configuraciones regionales.
>

La restauración instantánea está disponible en los almacenes de Recovery Services de Azure Portal y en los almacenes de Backup del portal clásico. Si desea usar la restauración instantánea, descargue la actualización de MARS y siga los procedimientos que mencionan la restauración instantánea.


## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Uso de la restauración instantánea para recuperar datos en la misma máquina

Si ha eliminado accidentalmente un archivo y desea restaurarlo en la misma máquina (desde la que se realizó la copia de seguridad), los pasos siguientes le ayudarán a recuperar esos datos.

1. Abra el complemento **Microsoft Azure Backup** . Si no conoce la ubicación donde se instaló el complemento, busque el equipo o servidor para **Microsoft Azure Backup**.

    La aplicación de escritorio debe aparecer en los resultados de búsqueda.

2. Haga clic en **Recuperar datos** para iniciar el asistente.

    ![Recuperar datos](./media/backup-azure-restore-windows-server/recover.png)

3. En el panel **Introducción**, para restaurar los datos en el mismo servidor o equipo, seleccione **Este servidor (`<server name>`)** y haga clic en **Siguiente**.

    ![Elegir la opción Este servidor para restaurar los datos en la misma máquina](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. En el panel **Seleccionar modo de recuperación**, seleccione **Archivos y carpetas individuales** y, luego, haga clic en **Siguiente**.

    ![Examinar archivos](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)

5. En el panel **Seleccionar volumen y fecha**, seleccione el volumen que contiene los archivos y/o carpetas que desea restaurar.

    En el calendario, seleccione un punto de recuperación. Puede realizar la restauración desde cualquier momento dado de recuperación. Las fechas en **negrita** indican la disponibilidad de al menos un punto de recuperación. Cuando selecciona una fecha, si están disponibles varios puntos de recuperación, elija el punto de recuperación específico desde el menú desplegable **Hora**.

    ![Fecha y volumen](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Una vez que haya elegido el punto de recuperación para restaurar, haga clic en **Montar**.

    Azure Backup monta el punto de recuperación local y lo usa como volumen de recuperación.

7. En el panel **Examinar y recuperar archivos**, haga clic en **Examinar** para abrir el Explorador de Windows y buscar los archivos y carpetas que desea.

    ![Opciones de recuperación](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. En el Explorador de Windows, copie los archivos y/o carpetas que desea restaurar y péguelos en cualquier ubicación local en el servidor o equipo. Puede abrir o transmitir los archivos directamente desde el volumen de recuperación y comprobar que se recuperan las versiones correctas.

    ![Copia y pegado de archivos y carpetas desde el volumen montado en la ubicación local](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Cuando termine de restaurar los archivos y/o carpetas, en el panel **Examinar y recuperar archivos**, haga clic en **Desmontar**. Haga clic en **Sí** para confirmar que desea desmontar el volumen.

    ![Desmontaje del volumen y confirmación](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Si no hace clic en Desmontar, el volumen de recuperación permanecerá montado durante seis horas desde el momento en el que se montó. No se ejecutará ninguna operación de copia de seguridad mientras el volumen esté montado. Cualquier operación de copia de seguridad programada para ejecutarse durante el tiempo en el que el volumen está montado, se ejecutará después de desmontar el volumen de recuperación.
    >


## <a name="recover-data-to-the-same-machine"></a>Recuperar los datos en la misma máquina
Si ha eliminado accidentalmente un archivo y desea restaurarlo en la misma máquina (desde la que se realizó la copia de seguridad), los pasos siguientes le ayudarán a recuperar esos datos.

1. Abra el complemento **Microsoft Azure Backup** .
2. Haga clic en **Recuperar datos** para iniciar el flujo de trabajo.

    ![Recuperar datos](./media/backup-azure-restore-windows-server-classic/recover.png)
3. Seleccione la opción **Este servidor (*nombreDeLaMáquina*)**, para restaurar en la misma máquina el archivo del que ha creado una copia de seguridad.

    ![Misma máquina](./media/backup-azure-restore-windows-server-classic/samemachine.png)
4. Elija entre **Examinar archivos** o **Buscar archivos**.

    Deje la opción predeterminada si va a restaurar uno o varios archivos cuya ruta de acceso se conoce. Si no está seguro de la estructura de carpetas pero desea buscar un archivo, escoja la opción **Buscar archivos** . En esta sección, se continuará con la opción predeterminada.

    ![Examinar archivos](./media/backup-azure-restore-windows-server-classic/browseandsearch.png)
5. Seleccione el volumen desde el que desea restaurar el archivo.

    Puede realizar la restauración a un momento dado. Las fechas que aparecen en **negrita** en el control del calendario indican la disponibilidad de un punto de restauración. Una vez seleccionada una fecha, según su programación de copia de seguridad (y el éxito que haya tenido al realizar una operación de copia de seguridad), puede seleccionar un momento dato en el menú desplegable **Tiempo** .

    ![Fecha y volumen](./media/backup-azure-restore-windows-server-classic/volanddate.png)
6. Seleccione los elementos que desea recuperar. Puede seleccionar varias carpetas y archivos para restaurar.

    ![Seleccionar archivos](./media/backup-azure-restore-windows-server-classic/selectfiles.png)
7. Especifique los parámetros de recuperación.

    ![Opciones de recuperación](./media/backup-azure-restore-windows-server-classic/recoveroptions.png)

   * Tiene la opción de restaurar en la ubicación original (en la que la carpeta o el archivos se sobrescribirán) o en otra ubicación en la misma máquina.
   * Si el archivo o carpeta que desea restaurar existe en la ubicación de destino, tiene la opción de crear copias (dos versiones del mismo archivo), sobrescribir los archivos en la ubicación de destino u omitir la recuperación de los archivos que existen en el destino.
   * Se recomienda que deje la opción predeterminada de la restauración de las ACL en los archivos que se está recuperando.
8. Una vez proporcionadas estas entradas, haga clic en **Siguiente**. El flujo de trabajo de recuperación que se encarga de restaurar los archivos de la máquina, se iniciará.

## <a name="recover-to-an-alternate-machine"></a>Recuperar en una máquina alternativa
Si ha perdido todo el servidor, todavía puede recuperar los datos de Azure Backup en una máquina diferente. Los pasos siguientes muestran el flujo de trabajo.  

La terminología usada en estos pasos incluye:

* *Máquina de origen* : es la máquina original desde la que se realizó la copia de seguridad y que no está disponible actualmente.
* *Máquina de destino* : es la máquina en la que se recuperan los datos.
* *Almacén de ejemplo*: almacén de copia de seguridad en el que se registran la *máquina de origen* y la *máquina de destino*. <br/>

> [!NOTE]
> No se pueden restaurar copias de seguridad realizadas desde una máquina en una máquina que está ejecutando una versión anterior del sistema operativo. Por ejemplo, si se realizan copias de seguridad de una máquina con Windows 7, esta puede restaurarse en un Windows 8 o una máquina con una versión superior. Sin embargo, la acción a la inversa no está asegurada.
>
>

1. Abra el complemento **Microsoft Azure Backup** en la *Máquina de destino*.
2. Asegúrese de que tanto la *Máquina de destino* como la *Máquina de origen* están registradas en el mismo almacén de copia de seguridad.
3. Haga clic en **Recuperar datos** para iniciar el flujo de trabajo.

    ![Recuperar datos](./media/backup-azure-restore-windows-server-classic/recover.png)
4. Seleccione **Otro servidor**

    ![Otro servidor](./media/backup-azure-restore-windows-server-classic/anotherserver.png)
5. Proporcione el archivo de credenciales de almacén que se corresponde con el *Almacén de ejemplo*. Si el archivo de credenciales de almacén no es válido (o ha expirado), descargue un nuevo archivo de credenciales de almacén desde el *almacén de ejemplo* en el Portal de Azure clásico. Una vez que se proporciona el archivo de almacén de credenciales, se muestra el almacén de copia de seguridad en el archivo de almacén de credenciales.
6. Seleccione la *Máquina de origen* en la lista de máquinas mostradas.

    ![Lista de máquinas](./media/backup-azure-restore-windows-server-classic/machinelist.png)
7. Seleccione la opción **Buscar archivos** o **Examinar archivos**. En esta sección, usaremos la opción **Buscar archivos** .

    ![Search](./media/backup-azure-restore-windows-server-classic/search.png)
8. Seleccione el volumen y la fecha en la pantalla siguiente. Busque el nombre de la carpeta o el archivo que desea restaurar.

    ![Buscar artículos](./media/backup-azure-restore-windows-server-classic/searchitems.png)
9. Seleccione la ubicación en la que se deben restaurar los archivos.

    ![Restaurar ubicación](./media/backup-azure-restore-windows-server-classic/restorelocation.png)
10. Proporcione la frase de contraseña de cifrado que se proporcionó durante el registro de la *Máquina de origen* en el *Almacén de ejemplo*.

    ![Cifrado](./media/backup-azure-restore-windows-server-classic/encryption.png)
11. Una vez especificada la entrada, haga clic en **Recuperar**, para desencadenar la restauración de los archivos con copia de seguridad en el destino proporcionado.

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Uso de restauración instantánea para restaurar datos en una máquina alternativa
Si ha perdido todo el servidor, todavía puede recuperar los datos de Azure Backup en una máquina diferente. Los pasos siguientes muestran el flujo de trabajo.

La terminología usada en estos pasos incluye:

* *Máquina de origen* : es la máquina original desde la que se realizó la copia de seguridad y que no está disponible actualmente.
* *Máquina de destino* : es la máquina en la que se recuperan los datos.
* *Almacén de ejemplo*: el almacén de Recovery Services en el que se registran la*máquina de origen* y la *máquina de destino*. <br/>

> [!NOTE]
> Las copias de seguridad no se pueden restaurar en una máquina de destino en la que se ejecuta una versión anterior del sistema operativo. Por ejemplo, una copia de seguridad perteneciente a un equipo con Windows 7 se puede restaurar en un equipo con Windows 8 o posterior. Una copia de seguridad perteneciente a un equipo con Windows 8 no se puede restaurar en un equipo con Windows 7.
>
>

1. Abra el complemento **Microsoft Azure Backup** en la *Máquina de destino*.

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
* [Preguntas más frecuentes de Azure Backup](backup-azure-backup-faq.md)
* Visite el [Foro de Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933).

## <a name="learn-more"></a>Más información
* [Información general de Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=222425)
* [Copia de seguridad de máquinas virtuales de Azure](backup-azure-vms-introduction.md)
* [Copia de seguridad de las cargas de trabajo de Microsoft](backup-azure-dpm-introduction.md)
