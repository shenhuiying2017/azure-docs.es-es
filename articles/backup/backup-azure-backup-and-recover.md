<properties pageTitle="Copia de seguridad de Azure: copia de seguridad y restauración desde un cliente de Windows Server o Windows" | Microsoft Azure description="Aprenda a realizar copias de seguridad y restaurar a partir de un cliente de Windows Server o Windows. El artículo también describe la recuperación de servidor alternativo" services="backup" documentationCenter="" authors="Jim-Parker" manager="jwhit" editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="07/01/2015" ms.author="jimpark"; "aashishr"/>

# Copia de seguridad y restauración de una máquina cliente de Windows o un servidor de Windows
En este artículo se describen los pasos necesarios para realizar una copia de seguridad desde un servidor de Windows o de una máquina cliente de Windows. También describe los pasos necesarios para restaurar los archivos en el mismo equipo y para restaurar los archivos de copia de seguridad en cualquier otra máquina.

## Archivos de copia de seguridad

1. Una vez registrada la máquina, abra el complemento MMC de Copia de seguridad de Microsoft Azure.

    ![Resultado de la búsqueda](./media/backup-azure-backup-and-recover/result.png)

2. Haga clic en **Programar copia de seguridad**.

    ![Programación de una copia de seguridad](./media/backup-azure-backup-and-recover/schedulebackup.png)

3. Seleccione los elementos de los que desea hacer una copia de seguridad. Copia de seguridad de Azure en un cliente de Windows Server/Windows (es decir, sin System Center Data Protection Manager) permite proteger archivos y carpetas.

    ![Elementos para realizar copia de seguridad](./media/backup-azure-backup-and-recover/items.png)

4. Especifique la directiva de retención y programación de copia de seguridad, que se explica en detalle en el siguiente [artículo](backup-azure-backup-cloud-as-tape.md).

5. Elija el método de envío de la copia de seguridad inicial. La elección de completar la propagación inicial depende de la cantidad de datos de los que se desea realizar la copia de seguridad y de la velocidad del vínculo de carga de Internet. Si tiene intención de realizar copias de seguridad de GB/TB de datos mediante una conexión de latencia alta y ancho de banda bajo, se recomienda que complete la copia de seguridad inicial enviando un disco al centro de datos de Azure más cercano. Esto se denomina "Copia de seguridad sin conexión" y se trata en detalle en este [artículo](backup-azure-backup-import-export.md). Si dispone de conexión con ancho de banda suficiente, se recomienda que complete la copia de seguridad inicial en la red.

    ![Copia de seguridad inicial](./media/backup-azure-backup-and-recover/initialbackup.png)

6. Una vez completado el proceso, vuelva al complemento MMC y haga clic en **Hacer copia de seguridad ahora** para completar la propagación inicial a través de la red.

    ![Realizar copia de seguridad ahora](./media/backup-azure-backup-and-recover/backupnow.png)

7. Una vez completada la propagación inicial, la vista **Trabajos** de la consola de Copia de seguridad de Azure indica el estado.

    ![IR completado](./media/backup-azure-backup-and-recover/ircomplete.png)

## Recuperar datos en la misma máquina
Si ha eliminado accidentalmente un archivo y desea restaurar el archivo/volumen en la misma máquina (desde la que se realiza la copia de seguridad), los pasos siguientes le ayudarán a recuperar los datos.

1. Abra el complemento **Copia de seguridad de Microsoft Azure**.

2. Haga clic en **Recuperar datos** para iniciar el flujo de trabajo.

    ![Recuperar datos](./media/backup-azure-backup-and-recover/recover.png)

3. Seleccione la opción **Este servidor (*nombredesuequipo*)**, ya que desea restaurar en la misma máquina el archivo del que ha creado una copia de seguridad.

    ![Misma máquina](./media/backup-azure-backup-and-recover/samemachine.png)

4. Puede elegir **Examinar archivos** o **Buscar archivos**. Deje la opción predeterminada si va a restaurar uno o varios archivos cuya ruta de acceso se conoce. Si no está seguro de la estructura de carpetas pero desea buscar un archivo, escoja la opción **Buscar archivos**. En esta sección, se continuará con la opción predeterminada.

    ![Examinar archivos](./media/backup-azure-backup-and-recover/browseandsearch.png)

5. Seleccione el volumen desde el que desea restaurar el archivo. La pantalla le permite restaurar desde cualquier punto en el tiempo. Las fechas que aparecen en **negrita** en el control del calendario indican la disponibilidad de un punto de restauración. Una vez que se selecciona una fecha, según su programación de copia de seguridad (y el éxito de una operación de copia de seguridad), puede seleccionar un punto en el tiempo desde la lista desplegable **Tiempo**.

    ![Fecha y volumen](./media/backup-azure-backup-and-recover/volanddate.png)

6. Seleccione los elementos que desea recuperar. Puede seleccionar varias carpetas y archivos para restaurar.

    ![Seleccionar archivos](./media/backup-azure-backup-and-recover/selectfiles.png)

7. Especifique los parámetros de recuperación.

    ![Opciones de recuperación](./media/backup-azure-backup-and-recover/recoveroptions.png)

  - Tiene la opción de restaurar en la ubicación original (en la que la carpeta o el archivos se sobrescribirán) o en otra ubicación en la misma máquina.
  - Si el archivo o carpeta que desea restaurar existe en la ubicación de destino, tiene la opción de crear copias (dos versiones del mismo archivo) o sobrescribir los archivos en la ubicación de destino u omitir la recuperación de los archivos que existen en el destino.
  - Se recomienda que deje la opción predeterminada de la restauración de las ACL en los archivos que se está recuperando.

8. Una vez que se proporcionan estas entradas, el flujo de trabajo de recuperación se inicia y restaura los archivos en esta máquina.

## Recuperar en una máquina alternativa
Si ha perdido todo el servidor, todavía puede recuperar el volumen o el archivo o en una máquina diferente. Los pasos siguientes muestran el flujo de trabajo.

La terminología usada en los pasos es la siguiente: -*Máquina de origen*: la máquina original desde la que se realizó la copia de seguridad y que está disponible actualmente. -*Equipo de destino*: el equipo en el que se recuperan los datos. -*Almacén de ejemplo*: almacén de copia de seguridad en el que se registran la *máquina de origen* y la *máquina de destino*. <br/>

> [AZURE.NOTE]No se pueden restaurar copias de seguridad realizadas desde una máquina en una máquina que está ejecutando una versión anterior del sistema operativo. Por ejemplo, si se realizan copias de seguridad de una máquina con Windows 7, esta puede restaurarse en un Windows 8 o una máquina con una versión superior. Sin embargo, la acción a la inversa no está asegurada.

1. Abra el complemento **Copia de seguridad de Microsoft Azure** en la *máquina de destino*.

2. Asegúrese de que la *máquina de destino* y la *máquina de origen* se restauran en el mismo almacén de copia de seguridad.

3. Haga clic en **Recuperar datos** para iniciar el flujo de trabajo.

    ![Recuperar datos](./media/backup-azure-backup-and-recover/recover.png)

4. Seleccione **Otro servidor**.

    ![Otro servidor](./media/backup-azure-backup-and-recover/anotherserver.png)

5. Proporcione el archivo de almacén de credenciales que se corresponde con el *almacén ejemplo*. Si el archivo de credenciales de almacén no es válido (o ha caducado), descargue un nuevo archivo de credenciales de almacén desde el *almacén ejemplo* en el portal de Azure. Una vez que se proporciona el archivo de almacén de credenciales, se muestra el almacén de copia de seguridad en el archivo de almacén de credenciales.

6. Seleccione la *máquina de origen* en la lista de máquinas mostradas.

    ![Lista de máquinas](./media/backup-azure-backup-and-recover/machinelist.png)

7. Como en el caso anterior, seleccione la opción **Buscar archivos** o **Examinar archivos**. En esta sección, usaremos la opción **Buscar archivos**.

    ![Search](./media/backup-azure-backup-and-recover/search.png)

8. Seleccione el volumen y la fecha en la pantalla siguiente. Busque el nombre de carpeta o archivo que desea restaurar.

    ![Buscar artículos](./media/backup-azure-backup-and-recover/searchitems.png)

9. Seleccione la ubicación en la que se deben restaurar los archivos.

    ![Restaurar ubicación](./media/backup-azure-backup-and-recover/restorelocation.png)

10. Proporcione la frase de contraseña de cifrado que se facilitó durante el registro de la *máquina de origen* en el *almacén de ejemplo*.

    ![Cifrado](./media/backup-azure-backup-and-recover/encryption.png)

11. Una vez especificada la entrada, haga clic en el botón **Recuperar**, que desencadena la restauración de los archivos con copia de seguridad en el destino proporcionado.

## Pasos siguientes
- [Preguntas más frecuentes de Copia de seguridad de Azure](backup-azure-backup-faq.md)

<!---HONumber=July15_HO4-->