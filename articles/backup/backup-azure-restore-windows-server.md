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
ms.date: 1/4/2018
ms.author: saurse;trinadhk;markgal;
ms.openlocfilehash: 3444b13972ab9e5c435fc009e8ddb51bcafb1a41
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>Restauración de archivos en un equipo de Windows Server o cliente de Windows mediante el modelo de implementación de Resource Manager

En este artículo se explica cómo restaurar datos a partir de un almacén de copia de seguridad. Para restaurar datos, utilice al Asistente para recuperar datos del agente de Microsoft Azure Recovery Services (MARS). Al restaurar datos, es posible realizar las siguientes tareas:

* La restauración de datos en la misma máquina desde la cual se realizaron las copias de seguridad.
* Restaurar datos en una máquina alternativa.

En enero de 2017, Microsoft publicó una actualización de versión preliminar para el agente de MARS. Además de las correcciones de errores, esta actualización permite restaurar de forma instantánea, lo que permite montar una instantánea de punto de recuperación grabable como un volumen de recuperación. Después, puede explorar el volumen y copiar archivos de recuperación en un equipo local, por tanto, restaurando los archivos de forma selectiva.

> [!NOTE]
> Se necesita la [actualización de enero de 2017 de Azure Backup](https://support.microsoft.com/en-us/help/3216528?preview) si desea utilizar la restauración instantánea para restaurar datos. También se deben proteger los datos de copia de seguridad en los almacenes en configuraciones regionales que aparecen en el artículo de soporte técnico. Consulte la [actualización de Azure Backup de enero de 2017](https://support.microsoft.com/en-us/help/3216528?preview) para obtener la lista más reciente de configuraciones regionales que admitan la restauración instantánea. La restauración instantánea **no** está actualmente disponible en todas las configuraciones regionales.
>

Use almacenes de Restauración instantánea con Recovery Services en Azure Portal. Si almacenó datos en los almacenes de Backup, se convirtieron en almacenes de Recovery Services. Si desea usar la restauración instantánea, descargue la actualización de MARS y siga los procedimientos que mencionan la restauración instantánea.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

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
    > Si no hace clic en Desmontar, el volumen de recuperación permanecerá montado durante seis horas desde el momento en el que se montó. Sin embargo, el tiempo de montaje se puede extender hasta 24 horas en el caso de una copia de archivos en curso. No se ejecutará ninguna operación de copia de seguridad mientras el volumen esté montado. Cualquier operación de copia de seguridad programada para ejecutarse durante el tiempo en el que el volumen está montado, se ejecutará después de desmontar el volumen de recuperación.
    >


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

    Si el archivo de credenciales de almacén no es válido (o ha expirado), descargue un nuevo archivo de credenciales de almacén desde el *Almacén de ejemplo* en Azure Portal. Cuando proporcione una credencial de almacén válida, aparecerá el nombre del almacén de Backup correspondiente.


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
    > Si no hace clic en Desmontar, el volumen de recuperación permanecerá montado durante seis horas desde el momento en el que se montó. Sin embargo, el tiempo de montaje se puede extender hasta 24 horas en el caso de una copia de archivos en curso. No se ejecutará ninguna operación de copia de seguridad mientras el volumen esté montado. Cualquier operación de copia de seguridad programada para ejecutarse durante el tiempo en el que el volumen está montado, se ejecutará después de desmontar el volumen de recuperación.
    >

## <a name="troubleshooting"></a>solución de problemas
Si Azure Backup no monta correctamente el volumen de recuperación incluso después de varios minutos de hacer clic en **Montar** o no se puede montar el volumen de recuperación con uno o varios errores, siga los pasos siguientes para empezar a realizar la recuperación normalmente.

1.  Cancele el proceso de montaje en curso en caso de que se haya estado ejecutando durante varios minutos.

2.  Asegúrese de que se encuentra en la versión más reciente del agente de Azure Backup. Para averiguar la información de versión del agente de Azure Backup, haga clic en **Acerca del agente de Microsoft Azure Recovery Services** en el panel **Acciones** de la consola de Microsoft Azure Backup y asegúrese de que el número de la **versión** es igual o mayor al de la versión mencionada en [este artículo](https://go.microsoft.com/fwlink/?linkid=229525). Puede descargar la versión más reciente [aquí](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Vaya al **Administrador de dispositivos** -> **Controladores de almacenamiento** y asegúrese de que puede encontrar el **iniciador iSCSI de Microsoft**. Si es así, vaya directamente al paso 7 a continuación. 

4.  Si no encuentra el servicio del iniciador iSCSI de Microsoft como se mencionó en el paso 3, compruebe si puede encontrar una entrada en **Administrador de dispositivos** -> **Controladores de almacenamiento** denominada **Dispositivo desconocido** con el identificador de hardware **ROOT\ISCSIPRT**.

5.  Haga clic con el botón derecho en **Dispositivo desconocido** y seleccione **Actualizar software de controlador**.

6.  Actualice el controlador. Para ello, seleccione la opción **Buscar software de controlador actualizado automáticamente**. Al finalizar la actualización **Dispositivo desconocido** debería pasar a **Iniciador iSCSI de Microsoft** tal y como se muestra a continuación. 

    ![Cifrado](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Vaya a **Administrador de tareas** -> **Servicios (local)** -> **Servicio del iniciador iSCSI de Microsoft**. 

    ![Cifrado](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)
    
8.  Reinicie el servicio del iniciador iSCSI de Microsoft haciendo clic con el botón derecho en el servicio, después en **Detener** y, finalmente, haga clic con el botón derecho de nuevo y seleccione **Iniciar**.

9.  Vuelva a intentar la recuperación mediante la restauración instantánea. 

Si la recuperación sigue sin funcionar, reinicie el cliente y el servidor. Si no es recomendable reiniciar el equipo o la recuperación sigue sin funcionar incluso después de reiniciar el servidor, intente recuperar desde una máquina alternativa y póngase en contacto con el soporte técnico de Azure. Para ello, vaya a [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) y envíe una solicitud de soporte técnico.

## <a name="next-steps"></a>pasos siguientes
* Ahora que ha recuperado los archivos y las carpetas, puede [administrar las copias de seguridad](backup-azure-manage-windows-server.md).
