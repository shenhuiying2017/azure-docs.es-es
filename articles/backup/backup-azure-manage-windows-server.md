<properties
	pageTitle="Administración de almacenes y servidores de Servicios de recuperación de Azure | Microsoft Azure"
	description="Use este tutorial para aprender a administrar almacenes y servidores de Servicios de recuperación de Azure."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/19/2016"
	ms.author="jimpark; markgal"/>


# Supervisión y administración de almacenes y servidores de los Servicios de recuperación de Azure para máquinas Windows

> [AZURE.SELECTOR]
- [Resource Manager](backup-azure-manage-windows-server.md)
- [Clásico](backup-azure-manage-windows-server-classic.md)

En este artículo encontrará información general sobre las tareas de administración de copias de seguridad que tiene disponibles en el Portal de administración de Azure y el agente de Copia de seguridad de Microsoft Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] modelo de implementación clásica.

## Tareas del Portal de administración

### Acceso a los almacenes de Servicios de recuperación

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/) mediante la suscripción de Azure.

2. En el menú del centro, haga clic en **Examinar** y, en la lista de recursos, escriba **Servicios de recuperación**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Haga clic en **Almacenes de Servicios de recuperación**.

    ![Creación del almacén de Servicios de recuperación, paso 1](./media/backup-azure-manage-windows-server/browse-to-rs-vaults.png) <br/>

2. Seleccione el nombre del almacén que desea ver en la lista para abrir la hoja del panel de Almacén de Servicios de recuperación.

    ![panel del almacén de Servicios de recuperación](./media/backup-azure-manage-windows-server/rs-vault-dashboard.png) <br/>

## Supervisión de trabajos y alertas
Puede supervisar los trabajos y alertas desde el panel del almacén de Servicios de recuperación, en el que encontrará:

- Detalles de las alertas de copia de seguridad
- Archivos y carpetas, así como las máquinas virtuales de Azure protegidas en la nube
- Almacenamiento total usado en Azure
- Estado del trabajo de copia de seguridad

![Copia de seguridad de las tareas del panel](./media/backup-azure-manage-windows-server/dashboard-tiles.png)

Si hace clic en la información de cada uno de estos iconos, se abrirá la hoja asociada en la que podrá administrar las tareas relacionadas.

En la parte superior del panel:

- El icono de configuración proporciona acceso a las tareas de copia de seguridad disponibles.
- El icono de copia de seguridad: ayuda a realizar la copia de seguridad de nuevos archivos y carpetas (o máquinas virtuales de Azure) en el almacén de Servicios de recuperación.
- El icono de eliminación: en caso de que ya no se use un almacén de Servicios de recuperación, puede eliminarlo para liberar espacio de almacenamiento. Este icono solo estará habilitado después de que todos los servidores protegidos se hayan eliminado del almacén.

![Copia de seguridad de las tareas del panel](./media/backup-azure-manage-windows-server/dashboard-tasks.png)

## Administración de alertas de copia de seguridad
Haga clic en el icono de **alertas de copia de seguridad** para abrir la hoja **Alertas de copias de seguridad** y administrar las alertas.

![Alertas de copia de seguridad](./media/backup-azure-manage-windows-server/manage-backup-alerts.png)

El icono de alertas de copia de seguridad le muestra el número de:

- alertas críticas sin resolver en las últimas 24 horas
- alertas de advertencia sin resolver en las últimas 24 horas

Si hace clic en cada uno de estos vínculos le llevarán a la hoja **Alertas de copias de seguridad** con una vista filtrada de estas alertas (críticas o de advertencia).

En la hoja Alertas de copias de seguridad, puede:

- Elegir la información adecuada para incluir con sus alertas.

    ![Elegir columnas](./media/backup-azure-manage-windows-server/choose-alerts-colunms.png)

- Filtrar alertas por gravedad, el estado y la hora de inicio y finalización.

    ![Filtrar alertas](./media/backup-azure-manage-windows-server/filter-alerts.png)

- Configure las notificaciones según la gravedad, la frecuencia y los destinatarios, y active o desactive las alertas.

    ![Filtrar alertas](./media/backup-azure-manage-windows-server/configure-notifications.png)

Si está seleccionada la opción **Por alerta** como frecuencia en **Notificar** no se producirá ninguna agrupación ni reducción de los correos electrónicos. Cada alerta generará una notificación. Esta es la configuración predeterminada y el correo electrónico de resolución también se envía inmediatamente.

Si se selecciona la opción **Resumen cada hora** como frecuencia en **Notificar** se envía un correo electrónico al usuario indicándole que hay nuevas alertas sin resolver generadas en la última hora. Se envía un correo electrónico de resolución al final del período de una hora.

Se pueden enviar alertas para los siguientes niveles de gravedad:

- crítico
- advertencia
- informativo

Desactive la alerta con el botón **Desactivar** en la hoja de detalles del trabajo. Al hacer clic en Desactivar, puede proporcionar notas de resolución.

Elija las columnas que desea que aparezcan como parte de la alerta con el botón **Elegir columnas**.

>[AZURE.NOTE] Desde la hoja **Configuración**, puede administrar alertas de copias de seguridad seleccionando **Supervisión e informes > Alertas y eventos > Alertas de copias de seguridad** y, a continuación, haga clic en **Filtrar** o en **Configurar notificaciones**.

## Administración de elementos de copia de seguridad
La administración de copias de seguridad locales ya está disponible en el Portal de administración. En la sección Copia de seguridad del panel, el icono **Elementos de copia de seguridad** muestra el número de elementos de copia de seguridad protegidos en el almacén.

Haga clic en **Archivos y carpetas** en el icono Elementos de copia de seguridad.

![Icono Elementos de copia de seguridad](./media/backup-azure-manage-windows-server/backup-items-tile.png)

Se abre la hoja Elementos de copia de seguridad con el filtro establecido en Archivos y carpetas en el que podrá ver cada elemento de copia de seguridad específico en una lista.

![Elementos de copia de seguridad](./media/backup-azure-manage-windows-server/backup-item-list.png)

Si hace clic en los elementos de una copia de seguridad específica de la lista, podrá ver los detalles esenciales de ese elemento.

>[AZURE.NOTE] Desde la hoja **Configuración**, puede administrar los archivos y carpetas seleccionando **Elementos protegidos > Elementos de copia de seguridad** y, a continuación, seleccionando **Archivos y carpetas** en el menú desplegable.

![Elementos de copia de seguridad en la configuración](./media/backup-azure-manage-windows-server/backup-files-and-folders.png)

## Administración de trabajos de copia de seguridad
Los trabajos de copia de seguridad locales (cuando el servidor local realiza la copia de seguridad en Azure) y los de copias de seguridad de Azure aparecen en el panel.

En la sección Copia de seguridad del panel, el icono Trabajo de copia de seguridad muestra el número de trabajos:

- En curso
- Con error en las últimas 24 horas.

Para administrar los trabajos de copia de seguridad, haga clic en el icono **Trabajos de copia de seguridad** que permite abrir la hoja del mismo nombre.

![Elementos de copia de seguridad en la configuración](./media/backup-azure-manage-windows-server/backup-jobs.png)

Puede modificar la información disponible en la hoja Trabajos de copia de seguridad con el botón **Elegir columnas** situado en la parte superior de la página.

Use el botón **Filtrar** para seleccionar entre Archivos y carpetas y Copia de seguridad de la máquina virtual de Azure.

Si no ve los archivos y carpetas de los que se ha realizado la copia de seguridad, haga clic en el botón **Filtrar** situado en la parte superior de la página y seleccione **Archivos y carpetas** en el menú Tipo de elemento.

>[AZURE.NOTE] En la hoja **Configuración**, puede administrar los trabajos de copia de seguridad mediante **Supervisión e informes > Trabajos > Trabajos de copia de seguridad** y, a continuación, seleccionando **Archivos y carpetas** en el menú desplegable.

## Supervisión del uso de Copia de seguridad
En la sección Copia de seguridad del panel, el icono Uso de copia de seguridad muestra el almacenamiento consumido en Azure. El uso de almacenamiento se proporciona para:
- Uso de almacenamiento LRS en la nube asociado con el almacén
- Uso de almacenamiento GRS en la nube asociado con el almacén

## Servidores de producción
Para administrar los servidores de producción, haga clic en **Configuración**. En Administrar, haga clic en **Infraestructura de copia de seguridad > Servidores de producción**.

La hoja Servidores de producción muestra todos los servidores de producción disponibles. Haga clic en un servidor de la lista para abrir los detalles del servidor.

![Elementos protegidos](./media/backup-azure-manage-windows-server/production-server-list.png)

## Tareas del agente de Copia de seguridad de Microsoft Azure

## Apertura del agente de copia de seguridad

Abra el **agente de Copia de seguridad de Microsoft Azure** (puede encontrarlo si busca en su equipo *Copia de seguridad de Microsoft Azure*).

![Programar una copia de seguridad de Windows Server](./media/backup-azure-manage-windows-server/snap-in-search.png)

Con las **acciones** disponibles en la parte derecha de la consola del agente de Copia de seguridad, podrá llevar a cabo las siguientes tareas de administración:

- Registrar un servidor
- Programación de una copia de seguridad
- Realizar una copia de seguridad en ese momento
- Cambiar propiedades

![Acciones de la consola del agente de Copia de seguridad de Microsoft Azure](./media/backup-azure-manage-windows-server/console-actions.png)

>[AZURE.NOTE] Para **recuperar datos**, consulte [Restauración de archivos en una máquina de Windows Server o del cliente de Windows](backup-azure-restore-windows-server.md).

## Modificación de una copia de seguridad existente

1. En el agente de Copia de seguridad de Microsoft Azure, haga clic en **Programar copia de seguridad**.

    ![Programar una copia de seguridad de Windows Server](./media/backup-azure-manage-windows-server/schedule-backup.png)

2. En el **Asistente para programar copias de seguridad** deje activada la opción **Cambiar la hora o las horas de las copias de seguridad** y haga clic en **Siguiente**.

    ![Programar una copia de seguridad de Windows Server](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)

3. Si quiere agregar o cambiar elementos, en la pantalla **Seleccionar elementos de los que realizar copia de seguridad**, haga clic en **Agregar elementos**.

    También puede establecer preferencias en **Configuración de exclusión**, en esta página del asistente. Si quiere excluir archivos o tipos de archivos, lea el procedimiento para agregar [configuración de exclusión](#exclusion-settings).

4. Seleccione los archivos y las carpetas de los que quiere realizar la copia de seguridad y haga clic en **Aceptar**.

    ![Programar una copia de seguridad de Windows Server](./media/backup-azure-manage-windows-server/add-items-modify.png)

5. Especifique la **programación de copia de seguridad** y haga clic en **Siguiente**.

    Puede programar copias de seguridad diarias (un máximo de 3 veces al día) o semanales.

    ![Elementos para la copia de seguridad de Windows Server](./media/backup-azure-manage-windows-server/specify-backup-schedule-modify-close.png)

    >[AZURE.NOTE] En este [artículo](backup-azure-backup-cloud-as-tape.md) se explica detalladamente cómo especificar la programación de copias de seguridad.

6. Seleccione la **directiva de retención** de la copia de seguridad y haga clic en **Siguiente**.

    ![Elementos para la copia de seguridad de Windows Server](./media/backup-azure-manage-windows-server/select-retention-policy-modify.png)

7. En la pantalla **Confirmación**, revise la información y haga clic en **Finalizar**.

8. Cuando el asistente finalice la creación de la **programación de copia de seguridad**, haga clic en **Cerrar**.

    Tras modificar la protección, puede confirmar que las copias de seguridad se estén activando correctamente; para ello, vaya a la pestaña **Trabajos** y confirme que se hayan reflejado los cambios en los trabajos de copia de seguridad.

## Habilitación de la limitación de la red  
En el agente de Copia de seguridad de Azure se incluye la pestaña Limitación, donde podrá controlar cómo se utiliza el ancho de banda de red durante la transferencia de datos. Este control puede resultar de ayuda si necesita realizar una copia de seguridad de los datos durante las horas de trabajo, pero no quiere que el proceso interfiera con otro tráfico de Internet. La limitación de la transferencia de datos se aplica a las actividades de copia de seguridad y restauración.

Para habilitar la limitación, siga estos pasos:

1. En el **agente de Copia de seguridad**, haga clic en **Cambiar propiedades**.

2. Active la casilla **Habilitar límite de uso del ancho de banda de Internet para las operaciones de copia de seguridad**.

    ![Limitación de la red](./media/backup-azure-manage-windows-server/throttling-dialog.png)

3. Una vez que se ha habilitado la limitación, especifique el ancho de banda permitido para la transferencia de datos de copia de seguridad durante la **jornada laboral** y las **horas de descanso**.

    Los valores de ancho de banda comienzan en 512 kilobytes por segundo (Kbps) y pueden subir hasta 1023 megabytes por segundo (Mbps). También puede designar el inicio y el final de la **jornada laboral**, así como qué días de la semana se consideran laborables. El tiempo no comprendido en la jornada laboral designada se considera horas de descanso.

4. Haga clic en **Aceptar**.

## Administración de la configuración de exclusión

1. Abra el **agente de Copia de seguridad de Microsoft Azure** (puede encontrarlo si busca en su equipo *Copia de seguridad de Microsoft Azure*).

    ![Programar una copia de seguridad de Windows Server](./media/backup-azure-manage-windows-server/snap-in-search.png)

2. En el agente de Copia de seguridad de Microsoft Azure, haga clic en **Programar copia de seguridad**.

    ![Programar una copia de seguridad de Windows Server](./media/backup-azure-manage-windows-server/schedule-backup.png)

3. En el Asistente para programar copias de seguridad deje activada la opción **Cambiar la hora o las horas de las copias de seguridad** y haga clic en **Siguiente**.

    ![Programar una copia de seguridad de Windows Server](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)

4. Haga clic en **Configuración de exclusión**.

    ![Programar una copia de seguridad de Windows Server](./media/backup-azure-manage-windows-server/exclusion-settings.png)

5. Haga clic en **Agregar exclusión**.

    ![Programar una copia de seguridad de Windows Server](./media/backup-azure-manage-windows-server/add-exclusion.png)

6. Seleccione la ubicación y, después, haga clic en **Aceptar**.

    ![Programar una copia de seguridad de Windows Server](./media/backup-azure-manage-windows-server/exclusion-location.png)

7. Agregue la extensión de archivo en el campo **Tipo de archivo**.

    ![Programar una copia de seguridad de Windows Server](./media/backup-azure-manage-windows-server/exclude-file-type.png)

    Adición de una extensión .mp3

    ![Programar una copia de seguridad de Windows Server](./media/backup-azure-manage-windows-server/exclude-mp3.png)

    Para agregar otra extensión, haga clic en **Agregar exclusión** y especifique otra extensión de tipo de archivo (por ejemplo, .jpeg).

    ![Programar una copia de seguridad de Windows Server](./media/backup-azure-manage-windows-server/exclude-jpg.png)

8. Cuando haya agregado todas las extensiones, haga clic en **Aceptar**.

9. Avance por las distintas pantallas del Asistente para programar copias de seguridad haciendo clic en **Siguiente** hasta llegar a la **página Confirmación** y, después, haga clic en **Finalizar**.

    ![Programar una copia de seguridad de Windows Server](./media/backup-azure-manage-windows-server/finish-exclusions.png)

## Preguntas más frecuentes
**P1. El estado del trabajo de copia de seguridad aparece como completado en el agente de Copia de seguridad de Azure, ¿por qué no se ve reflejado inmediatamente en el portal?**

R1. Hay un retraso máximo de 15 minutos entre que el estado del trabajo de copia de seguridad se refleja en el agente de Copia de seguridad de Azure y en el Portal de Azure.

**P.2 Cuando se produce un error en un trabajo de copia de seguridad, ¿cuánto tiempo se tarda en generar una alerta?**

R.2 Se genera una alerta en menos de 5 minutos desde que se produce el error de Copia de seguridad de Azure.

**P3. ¿Hay algún caso en el que no se envíe ningún correo electrónico si se configuran las notificaciones?**

R3. Si se configuran las notificaciones por horas y se genera una alerta y esta se resuelve en menos de una hora, no se enviará ningún correo electrónico.

## Pasos siguientes
- [Restauración de Windows Server o el cliente de Windows desde Azure](backup-azure-restore-windows-server.md)
- Para obtener más información sobre Copia de seguridad de Azure, consulte [Información general de Copia de seguridad de Azure](backup-introduction-to-azure-backup.md).
- Visite el [Foro de Copia de seguridad de Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0817_2016-->