<properties
	pageTitle="Copia de seguridad de Azure: copia de seguridad y restauración de un equipo cliente de Windows o de un servidor de Windows"
	description="Aprenda cómo realizar la copia de seguridad y restauración de un equipo cliente de Windows o de un servidor de Windows. El artículo también describe la recuperación del servidor alternativo."
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="04/02/2015"
	 ms.author="prvijay"/>

# Copia de seguridad y restauración de una máquina cliente de Windows o un servidor de Windows
Este artículo describe los pasos necesarios para la copia de seguridad de un servidor de Windows o de una máquina cliente de Windows. También describe los pasos necesarios para restaurar los archivos en el mismo equipo y para restaurar los archivos de copia de seguridad en cualquier otra máquina.

## Archivos de copia de seguridad
1. Una vez registrada la máquina, abra el complemento mmc de copias de seguridad de Microsoft Azure. <br/> ![Resultado de la búsqueda][1]

2. Haga clic en **Programar copia de seguridad** <br/> ![Programación de una copia de seguridad][2].

3. Seleccione los elementos de los que desea una copia de seguridad. La copia de seguridad de Azure en un cliente de Windows o un servidor de Windows (es decir, sin System Center Data Protection Manager) permite proteger archivos y carpetas. <br/> ![Elementos para realizar copia de seguridad][3]

4. Especifique la directiva de retención y programación de copia de seguridad, que se explica en detalle en el siguiente [artículo](backup-azure-backup-cloud-as-tape.md).

5. Elija el método de envío de la copia de seguridad inicial. La elección de completar la propagación inicial depende de la cantidad de datos de los que se desea realizar la copia de seguridad y de la velocidad de vínculo de carga de Internet. Si tiene intención de realizar copias de seguridad de GB/TB de datos con una alta latencia y una conexión de bajo ancho de banda, se recomienda que complete la copia de seguridad inicial enviando un disco al centro de datos de Azure más cercano. Esto se denomina "Copia de seguridad sin conexión" y se trata en detalle en este [artículo](https://msdn.microsoft.com/library/azure/dn894419.aspx). Si dispone de conexión de ancho de banda suficiente, se recomienda que complete la copia de seguridad inicial en la red. <br/> ![Copia de seguridad inicial][4]

6. Una vez completado el flujo de trabajo, vuelva al complemento mmc y haga clic en **Realizar copia de seguridad ahora** para completar la propagación inicial a través de la red. <br/> ![Realizar copia de seguridad ahora][5]

7. Una vez completada la propagación inicial, la vista **Trabajos** en la consola de la copia de seguridad de Azure indica el estado. <br/> ![IR completado][6]

## Recuperar datos en la misma máquina
Si ha eliminado accidentalmente un archivo y desea restaurar un volumen o un archivo en la misma máquina (desde la que se realiza la copia de seguridad), los pasos siguientes ayudarían a recuperar los datos.

1. Abra el complemento **Copia de seguridad de Microsoft Azure**.

2. Haga clic en **Recuperar datos** para iniciar el flujo de trabajo. <br/> ![Recuperar datos][7]

3. Seleccione la opción **Este servidor (*nombredesuequipo*)**, ya que desea restaurar los archivos en la misma máquina. <br/> ![Misma máquina][8]

4. Puede elegir también **Examinar archivos** o **Buscar archivos**. Deje la opción predeterminada si va a restaurar uno o varios archivos cuya ruta de acceso se conoce. Si no está seguro de la estructura de carpetas pero desea buscar un archivo, escoja la opción **Buscar archivos**. En esta sección, se continuará con la opción predeterminada. <br/> ![Examinar archivos][9]

5. En la siguiente pantalla, seleccione el volumen desde el que desea restaurar el archivo. La pantalla le permite restaurar desde cualquier punto en el tiempo. Las fechas que aparecen en **negrita** en el control del calendario indican la disponibilidad de un punto de restauración. Una vez que se selecciona una fecha, según su programación de copia de seguridad (y el éxito de una operación de copia de seguridad), puede seleccionar un punto en el tiempo desde la lista desplegable **Tiempo**. <br/> ![Fecha y volumen][10]

6. En la pantalla siguiente, seleccione los elementos que desea recuperar. Puede seleccionar varias carpetas y archivos para restaurar. <br/> ![Seleccionar archivos][11]

7. En la siguiente pantalla, especifique los parámetros de recuperación. <br/> ![Opciones de recuperación][12]
  + Tiene la opción de restaurar en la ubicación original (en la que la carpeta o el archivos se sobrescribirán) o en otra ubicación en la misma máquina.

  + Si el archivo o carpeta que desea restaurar existe en la ubicación de destino, tiene la opción de crear copias (dos versiones del mismo archivo) o sobrescribir los archivos en la ubicación de destino u omitir la recuperación de los archivos que existen en el destino.

  + Se recomienda que deje la opción predeterminada de la restauración de las ACL en los archivos que se está recuperando.

8. Una vez que se proporcionan estas entradas, el flujo de trabajo de recuperación se inicia y restaura los archivos en esta máquina.

## Recuperar en una máquina alternativa
Si ha perdido todo el servidor, todavía puede recuperar el volumen o el archivo o en una máquina diferente. Los pasos siguientes muestran el flujo de trabajo.

La terminología usada en los pasos son los siguientes: + *máquina de origen*: la máquina original desde la que se realizó la copia de seguridad y que no está disponible actualmente.

  + *Máquina de destino*: la máquina en la que se recuperan los datos.

  + *Almacén de ejemplo*: almacén de copia de seguridad en el que se registran la *máquina de origen* y la *máquina de destino*. <br/>

> [AZURE.NOTE]No se pueden restaurar copias de seguridad realizadas desde una máquina en una máquina que está ejecutando una versión anterior del sistema operativo. Por ejemplo, si se realizan copias de seguridad de una máquina con Windows 7, esta puede restaurarse en un Windows 8 o una máquina con una versión superior. Sin embargo, la acción a la inversa no está asegurada.

1. Abra el complemento **Copia de seguridad de Microsoft Azure** en la *máquina de destino*.

2. Asegúrese de que la *máquina de destino* y la *máquina de origen* se restauran en el mismo almacén de copia de seguridad (en este artículo: *almacén de ejemplo*).

3. Haga clic en **Recuperar datos** para iniciar el flujo de trabajo. <br/> ![Recuperar datos][7]

4. Seleccione **Otro servidor** <br/> ![Otro servidor][13].

5. Proporcione el archivo de almacén de credenciales que se corresponde con el *almacén ejemplo*. Si el archivo de credenciales de almacén no es válido (o ha caducado), descargue un nuevo archivo de credenciales de almacén desde el *almacén ejemplo* en el portal de Azure. Una vez que se proporciona el archivo de almacén de credenciales, se muestra el almacén de copia de seguridad en el archivo de almacén de credenciales.

6. Seleccione la *máquina de origen* en la lista de máquinas mostradas. <br/> ![Lista de máquinas][14]

7. Igual que antes, seleccione la opción **Buscar archivos** o **Examinar archivos**. En esta sección, usaremos la opción **Buscar archivos**. <br/> ![Search][15]

8. Seleccione el volumen y la fecha en la pantalla siguiente. Busque el nombre de carpeta o archivo que desea restaurar. <br/> ![Buscar artículos][16]

9. Seleccione la ubicación a la que tiene que restaurar los archivos. <br/> ![Restaurar ubicación][17]

10. Proporcione la frase de contraseña de cifrado que se proporcionó durante el registro de la *máquina de origen* en el *almacén de ejemplo*. <br/> ![Cifrado][18]

Una vez proporcionada la entrada, haga clic en el botón **Recuperar**, que desencadena la restauración de los archivos de copia de seguridad en el destino proporcionado.

<!--Image references-->
[1]: ./media/backup-azure-backup-and-recover/result.png
[2]: ./media/backup-azure-backup-and-recover/schedulebackup.png
[3]: ./media/backup-azure-backup-and-recover/items.png
[4]: ./media/backup-azure-backup-and-recover/initialbackup.png
[5]: ./media/backup-azure-backup-and-recover/backupnow.png
[6]: ./media/backup-azure-backup-and-recover/ircomplete.png

[7]: ./media/backup-azure-backup-and-recover/recover.png
[8]: ./media/backup-azure-backup-and-recover/samemachine.png
[9]: ./media/backup-azure-backup-and-recover/browseandsearch.png
[10]: ./media/backup-azure-backup-and-recover/volanddate.png
[11]: ./media/backup-azure-backup-and-recover/selectfiles.png
[12]: ./media/backup-azure-backup-and-recover/recoveroptions.png

[13]: ./media/backup-azure-backup-and-recover/anotherserver.png
[14]: ./media/backup-azure-backup-and-recover/machinelist.png
[15]: ./media/backup-azure-backup-and-recover/search.png
[16]: ./media/backup-azure-backup-and-recover/searchitems.png
[17]: ./media/backup-azure-backup-and-recover/restorelocation.png
[18]: ./media/backup-azure-backup-and-recover/encryption.png

<!---HONumber=GIT-SubDir--> 