---
title: "Solución de problemas de Azure SQL Data Sync | Microsoft Docs"
description: Aprenda a solucionar problemas comunes con Azure SQL Data Sync
services: sql-database
ms.date: 11/2/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: bbfcac5a54f04f20dbdeeecef7c06b91128b8c6a
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2017
---
# <a name="troubleshoot-issues-with-azure-sql-data-sync-preview"></a>Solución de problemas de SQL Data Sync de Azure (versión preliminar)

En este artículo se explica cómo solucionar los problemas actuales conocidos por el equipo de SQL Data Sync (versión preliminar). Si hay una solución alternativa para un problema, la encontrará aquí.

Para obtener información general sobre SQL Data Sync, vea [Sincronización de datos entre varias bases de datos locales y de la nube con SQL Data Sync de Azure (versión preliminar)](sql-database-sync-data.md).
                                                           
## <a name="my-client-agent-doesnt-work"></a>Mi agente cliente no funciona

### <a name="description-and-symptoms"></a>Descripción y síntomas

Al intentar utilizar el agente cliente, recibe los siguientes mensajes de error.

"Error de sincronización con la excepción 'Se ha producido un error al intentar deserializar el parámetro www.microsoft.com/.../05:GetBatchInfoResult'. Consulte InnerException para obtener más información".

"Mensaje de excepción interna: 'Microsoft.Synchronization.ChangeBatch' es un tipo de colección no válido porque no tiene ningún constructor predeterminado".


### <a name="cause"></a>Causa

Este error es un problema con SQL Data Sync (versión preliminar).

La causa más probable de este problema es que:

-   está ejecutando Windows 8 Developer Preview, o

-   tiene instalado .NET 4.5.

### <a name="solution-or-workaround"></a>Solución o solución alternativa

Asegúrese de instalar el agente cliente en un equipo que no esté ejecutando Windows 8 Developer Preview y que no tenga instalado .NET Framework 4.5.

## <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>El agente cliente no funciona cuando cancelo la desinstalación

### <a name="description-and-symptoms"></a>Descripción y síntomas

El agente cliente no funciona aunque cancele su desinstalación.

### <a name="cause"></a>Causa

Este problema se produce porque el agente cliente de SQL Data Sync (versión preliminar) no almacena credenciales.

### <a name="solution-or-workaround"></a>Solución o solución alternativa

Puede probar dos soluciones:

-   En primer lugar, use services.msc para volver a introducir sus credenciales para el agente cliente.

-   En segundo lugar, desinstale este agente cliente e instale uno nuevo. Descargue e instale el agente cliente más reciente del [Centro de descarga](http://go.microsoft.com/fwlink/?linkid=221479).

## <a name="my-database-isnt-listed-in-the-agent-dropdown"></a>Mi base de datos no aparece en la lista desplegable del agente

### <a name="description-and-symptoms"></a>Descripción y síntomas

Al intentar agregar una base de datos existente de SQL Server a un grupo de sincronización, la base de datos no aparece en la lista desplegable.

### <a name="cause"></a>Causa

Hay varias causas posibles para este problema:

-   El agente cliente y el grupo de sincronización se encuentran en distintos centros de datos.

-   La lista de agentes cliente de la base de datos no está actualizada.

### <a name="solution"></a>Solución

La solución dependerá de la causa del problema.

#### <a name="the-client-agent-and-sync-group-are-in-different-data-centers"></a>El agente cliente y el grupo de sincronización se encuentran en distintos centros de datos

Debe tener el agente cliente y el grupo de sincronización en el mismo centro de datos. Puede establecer esta configuración mediante una de las siguientes acciones:

-   Cree un nuevo agente en el mismo centro de datos que el grupo de sincronización y registre la base de datos con ese agente. Para obtener más información, consulte [Cómo instalar un agente cliente de SQL Data Sync (versión preliminar)](#install-a-sql-data-sync-client-agent).

-   Elimine el grupo de sincronización actual. A continuación, vuelva a crearlo en el mismo centro de datos que el agente.

#### <a name="the-client-agents-list-of-databases-is-not-current"></a>La lista de agentes cliente de la base de datos no está actualizada

Detenga y reinicie el servicio de agente cliente.
El agente local descarga la lista de bases de datos asociadas solo la primera vez que se envía la clave del agente, pero no en los envíos posteriores. Por lo tanto, las bases de datos registradas durante un traslado del agente no aparecen en la instancia original del agente.

## <a name="client-agent-doesnt-start-error-1069"></a>El agente cliente no se inicia (error 1069)

### <a name="description-and-symptoms"></a>Descripción y síntomas

El agente no se está ejecutando en un equipo que hospeda SQL Server. Al intentar iniciar manualmente el agente, se abrirá un cuadro de diálogo con el mensaje de error "Error 1069: el servicio no se inició debido a un error de inicio de sesión".

![Cuadro de diálogo del error 1069 de Data Sync](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

### <a name="cause"></a>Causa

Una causa probable de este error es que la contraseña del servidor local ha cambiado desde que creó el agente y le asignó una contraseña de inicio de sesión.

### <a name="solution-or-workaround"></a>Solución o solución alternativa

Actualice la contraseña del agente con su contraseña actual del servidor.

1. Busque el servicio de vista previa de agentes cliente de SQL Data Sync (versión preliminar).

    a. Haga clic en **Iniciar**.

    b. Escriba "services.msc" en el cuadro de búsqueda.

    c. En los resultados de la búsqueda, haga clic en “Servicios”.

    d. En la ventana **Servicios**, desplácese hasta la entrada para la **vista previa de agentes de SQL Data Sync (versión preliminar)**.

2. Haga clic con el botón derecho en la entrada y seleccione **Detener**.

3. Haga clic con el botón derecho en la entrada y después en **Propiedades**.

4. En la ventana **Propiedades de vista previa de agentes de SQL Data Sync (versión preliminar)**, haga clic en la pestaña **Inicio de sesión**.

5. Introduzca su contraseña en el cuadro de texto Contraseña.

6. Confirme su contraseña en el cuadro de texto Confirmar contraseña.

7. Haga clic en **Aplicar** y en **Aceptar**.

8. En la ventana **Servicios**, haga clic en el servicio **Vista previa de agentes de SQL Data Sync (versión preliminar)** y en **Iniciar**.

9. Cierre la ventana **Servicios**.

## <a name="i-get-a-disk-out-of-space-message"></a>Aparece un mensaje en el que se indica "espacio insuficiente en disco"

### <a name="cause"></a>Causa

El mensaje "espacio insuficiente en disco" puede aparecer cuando los archivos que se deben eliminar permanecen detrás. Esto puede suceder a causa de un software antivirus o porque los archivos están abiertos al intentar realizar las operaciones de eliminación.

### <a name="solution"></a>Solución

La solución consiste en eliminar manualmente los archivos de sincronización en `%temp%` (`del \*sync\* /s`) y después quitar también los subdirectorios.

> [!IMPORTANT]
> Espere hasta que finalice la sincronización antes de eliminar los archivos.

## <a name="i-cannot-delete-my-sync-group"></a>No puedo eliminar mi grupo de sincronización

### <a name="description-and-symptoms"></a>Descripción y síntomas

Se produce un error al intentar eliminar un grupo de sincronización.

### <a name="causes"></a>Causas

El error al eliminar un grupo de sincronización se puede producir por cualquiera de los siguientes motivos:

-   El agente cliente no tiene conexión.

-   El agente cliente se ha desinstalado o no está presente. 

-   Una base de datos está sin conexión. 

-   El grupo de sincronización está aprovisionando o sincronizando. 

### <a name="solutions"></a>Soluciones

Para solucionar el error al eliminar un grupo de sincronización, haga las comprobaciones siguientes:

-   Compruebe que el agente cliente está en línea y vuelva a intentarlo.

-   Si el agente cliente se ha desinstalado o no está presente, haga lo siguiente:

    a. Si el archivo XML de agente existe, quítelo de la carpeta de instalación de SQL Data Sync (versión preliminar).

    b. Instale el agente en el mismo equipo local o en otro, envíe la clave del agente desde el portal generado para el agente que aparece sin conexión.

-   **El servicio de SQL Data Sync (versión preliminar) se detiene.**

    a. En el menú **Inicio**, busque Servicios.

    b. En los resultados de la búsqueda, haga clic en Servicios.

    c. Busque el servicio **Vista previa de SQL Data Sync (versión preliminar)**.

    d. Si el estado del servicio está en **Detenido**, haga clic con el botón derecho en el nombre del servicio y seleccione **Iniciar** en el menú desplegable.

-   Compruebe las bases de datos de SQL Database y SQL Server para asegurarse de que están todas en línea.

-   Espere a que finalice el proceso de aprovisionamiento o sincronización. A continuación, vuelva a intentar eliminar el grupo de sincronización.

## <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-associated-with-the-client-agent"></a>Se produce un error de sincronización en la interfaz de usuario del portal para las bases de datos locales asociadas al agente cliente

### <a name="description-and-symptoms"></a>Descripción y síntomas

Se produce un error de sincronización en la interfaz de usuario del portal de SQL Data Sync (versión preliminar) asociado al agente. En el registro de eventos del equipo local que ejecuta el agente, verá los errores System.IO.IOException, en los que se indica que el disco no tiene espacio suficiente.

### <a name="solution-or-workaround"></a>Solución o solución alternativa

Cree más espacio en la unidad en la que reside el directorio %TEMP%.

## <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>No puedo anular el registro de una base de datos de SQL Server local

### <a name="cause"></a>Causa

Es muy probable que esté intentando anular el registro de una base de datos que ya se ha eliminado.

### <a name="solution-or-workaround"></a>Solución o solución alternativa

Para anular el registro de una base de datos de SQL Server local, seleccione la base de datos y haga clic en **Forzar eliminación**.

Si con esta operación no se puede quitar la base de datos del grupo de sincronización, haga lo siguiente:

1. Detenga y reinicie el servicio de host del agente cliente.

    a. Haga clic en el menú Inicio.

    b. Escriba *services.msc* en el cuadro de búsqueda.

    c. En la sección Programas del panel de resultados, haga doble clic en **Servicios**.

    d. Busque y haga clic con el botón derecho en el servicio **SQL Data Sync (versión preliminar)**.

    e. Si el servicio se está ejecutando, deténgalo.

    f. Haga clic con el botón derecho y seleccione **Iniciar**.

    g. Compruebe si la base de datos ya no está registrada. Si ya no lo está, habrá terminado. En caso contrario, continúe con el paso siguiente.

2. Abra la aplicación de agente cliente (SqlAzureDataSyncAgent).

3. Haga clic en **Editar credenciales** y proporcione las credenciales de la base de datos para que sea accesible.

4. Continúe con la anulación del registro.

## <a name="i-cannot-submit-the-agent-key"></a>No puedo enviar la clave del agente

### <a name="description-and-symptoms"></a>Descripción y síntomas

Después de crear o volver a crear una clave para un agente, intenta enviar esa clave a través de la aplicación SqlAzureDataSyncAgent pero no se puede completar el envío.

![Cuadro de diálogo de error de sincronización: no se puede enviar la clave del agente](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

Antes de continuar, asegúrese de que el problema no está causado por una de las situaciones siguientes.

-   El servicio de Windows SQL Data Sync (versión preliminar) se está ejecutando.

-   La cuenta del servicio de Windows de vista previa de SQL Data Sync (versión preliminar) tiene acceso a la red.

-   El agente cliente puede ponerse en contacto con el servicio de ubicación. Compruebe que la siguiente clave de registro tiene el valor "https://locator.sync.azure.com/LocatorServiceApi.svc".

    -   En un equipo x86: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

    -   En un equipo x64: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

### <a name="cause"></a>Causa

La clave del agente identifica de forma única cada agente local. La clave debe cumplir dos condiciones para poder funcionar:

-   La clave del agente cliente del servidor SQL Data Sync (versión preliminar) y del equipo local debe ser idéntica.

-   La clave del agente cliente solo se puede usar una vez.

### <a name="solution-or-workaround"></a>Solución o solución alternativa

Si el agente no funciona, es porque una de estas condiciones o las dos no se cumplen. Para que el agente vuelva a funcionar:

1. Genere una nueva clave.

2. Aplique la nueva clave al agente.

Para aplicar la nueva clave al agente, haga lo siguiente:

1. Utilice el Explorador de archivos para ir hasta el directorio de instalación del agente. El directorio de instalación predeterminado es `c:\\program files (x86)\\microsoft sql data sync`.

2. Haga doble clic en el subdirectorio `bin`.

3. Inicie la aplicación `SqlAzureDataSyncAgent`.

4. Haga clic en **Enviar clave del agente**.

5. Pegue la clave del portapapeles en el espacio proporcionado.

6. Haga clic en **Aceptar**.

7. Cierre el programa.

## <a name="i-do-not-have-sufficient-privileges-to-start-system-services"></a>No tengo suficientes privilegios para iniciar los servicios del sistema

### <a name="cause"></a>Causa

Este error se produce en dos situaciones:

-   El nombre de usuario o la contraseña son incorrectos.

-   La cuenta de usuario especificada no tiene suficientes privilegios para iniciar sesión como servicio.

### <a name="solution-or-workaround"></a>Solución o solución alternativa

Conceda a la cuenta de usuario credenciales para iniciar sesión como servicio.

1. Vaya a **Inicio | Panel de control | Herramientas administrativas | Directiva de seguridad local | Directiva local | Administración de derechos de usuario**.

2. Busque la entrada **Iniciar sesión como servicio** y haga clic en ella.

3. Agregue la cuenta de usuario en el cuadro de diálogo **Propiedades: Iniciar sesión como servicio**.

4. Haga clic en **Aplicar** y en **Aceptar**.

5. Cierre las ventanas.

## <a name="local-sync-agent-app-is-unable-to-connect-to-the-local-sync-service"></a>La aplicación local del agente de sincronización no se puede conectar al servicio de sincronización local

### <a name="solution-or-workaround"></a>Solución o solución alternativa

Realice estos pasos:

1. Salga de la aplicación.

2. Abra el panel Servicios de componentes.

    a. En el cuadro de búsqueda de la barra de tareas, escriba "services.msc".

    b. En los resultados de la búsqueda, haga doble clic en “Servicios”.

3. Detenga el servicio “Vista previa de SQL Data Sync (versión preliminar)” y vuelva a iniciarlo.

4. Reinicie la aplicación.

## <a name="install-uninstall-or-repair-fails"></a>Se produce un error al instalar, desinstalar o reparar

### <a name="cause"></a>Causa

Hay varias causas posibles para este error. Para determinar cuál es la causa concreta, debe examinar los registros.

### <a name="solution-or-workaround"></a>Solución o solución alternativa

Para encontrar la causa específica del error, necesita generar y examinar los registros de Windows Installer. Puede activar el registro desde la línea de comandos. Por ejemplo, supongamos que el archivo AgentServiceSetup.msi descargado es LocalAgentHost.msi. Genere y examine los archivos de registro utilizando las siguientes líneas de comandos:

-   Para las instalaciones: `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`

-   Para las desinstalaciones: `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

También puede habilitar el registro para todas las instalaciones realizadas por Windows Installer. En el artículo de Microsoft Knowledge Base [Cómo habilitar el registro de Windows Installer](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) se proporciona una solución de un solo clic para activar el registro en Windows Installer. También indica la ubicación de esos registros.

## <a name="a-database-has-an-out-of-date-status"></a>Una base de datos tiene como estado "Obsoleto"

### <a name="cause"></a>Causa

SQL Data Sync (versión preliminar) quita del servicio las bases de datos que han estado sin conexión durante 45 días o más (contados a partir del momento en que la base de datos quedó sin conexión). Si una base de datos está sin conexión durante 45 días o más y después vuelve a conectarse, su estado se establece en "Obsoleto".

### <a name="solution-or-workaround"></a>Solución o solución alternativa

Para evitar un estado "Obsoleto", asegúrese de que ninguna de las bases de datos esté sin conexión durante 45 días o más.

Si el estado de la base de datos es "Obsoleto", debe hacer lo siguiente:

1. Quite del grupo de sincronización la base de datos obsoleta.

2. Vuelva a agregar la base de datos al grupo de sincronización.

> [!WARNING]
> Perderá todos los cambios que haya realizado en la base de datos mientras estaba sin conexión.

## <a name="a-sync-group-has-an-out-of-date-status"></a>Un grupo de sincronización tiene como estado "Obsoleto"

### <a name="cause"></a>Causa

Un grupo de sincronización puede quedar obsoleto si uno o más cambios no se pueden aplicar durante todo el período de retención de 45 días.

### <a name="solution-or-workaround"></a>Solución o solución alternativa

Para evitar el estado "Obsoleto", examine de forma regular los resultados de los trabajos de sincronización en el visor del historial, averigüe qué cambios no se pudieron aplicar y resuélvalos.

Si el estado de un grupo de sincronización es "Obsoleto", tiene que eliminar el grupo de sincronización y volver a crearlo.

## <a name="i-see-erroneous-data-in-my-tables"></a>Veo datos erróneos en las tablas

### <a name="description-and-symptoms"></a>Descripción y síntomas

Si la sincronización incluye tablas con el mismo nombre pero con esquemas diferentes de una base de datos, después de la sincronización verá datos erróneos en dichas tablas.

### <a name="cause-and-fix"></a>Causa y solución

Para el proceso de aprovisionamiento de SQL Data Sync (versión preliminar), se usan las mismas tablas de seguimiento para las tablas con el mismo nombre pero con esquemas distintos. Como resultado, los cambios de ambas tablas se reflejan en la misma tabla de seguimiento y este comportamiento que provoca que los datos sean erróneos cambia durante la sincronización.

### <a name="resolution-or-workaround"></a>Solución o solución alternativa

Asegúrese de que los nombres de las tablas implicadas en la sincronización sean diferentes aunque pertenezcan a esquemas distintos.

## <a name="i-see-inconsistent-primary-key-data-after-a-successful-synchronization"></a>Tras una sincronización correcta, veo datos clave principales incoherentes

### <a name="description-and-symptoms"></a>Descripción y síntomas

Después de una sincronización que se notifica como correcta y en cuyo registro no se muestran filas con errores u omitidas, observa que los datos clave principales son incoherentes entre las bases de datos del grupo de sincronización.

### <a name="cause"></a>Causa

Este comportamiento es así por diseño. Cualquier cambio realizado en una clave principal tiene como resultado datos incoherentes en las filas en las que se ha cambiado la clave principal.

### <a name="resolution-or-workaround"></a>Solución o solución alternativa

Para evitar este problema, asegúrese de que no se modifica ningún dato en la columna de clave principal.

Para corregir este problema una vez producido, debe quitar la fila afectada de todos los puntos de conexión del grupo de sincronización y volver a insertarla.

## <a name="i-see-a-significant-degradation-in-performance"></a>Veo una degradación significativa del rendimiento

### <a name="description-and-symptoms"></a>Descripción y síntomas

El rendimiento disminuye considerablemente, posiblemente hasta tal punto que ni siquiera puede iniciar la interfaz de usuario de Data Sync.

### <a name="cause"></a>Causa

La causa más probable es que exista un bucle de sincronización. Un bucle de sincronización se produce cuando una sincronización realizada por un grupo de sincronización A desencadena una sincronización realizada por un grupo de sincronización B, que a su vez desencadena una sincronización realizada por un grupo de sincronización A. La situación real puede ser más compleja e involucrar más de dos grupos de sincronización en el bucle, pero el factor decisivo es que hay un desencadenamiento circular de sincronizaciones causadas por grupos de sincronización que se superponen los unos a los otros.

### <a name="resolution-or-workaround"></a>Solución o solución alternativa

La mejor corrección es la prevención. Asegúrese de que no haya referencias circulares en los grupos de sincronización. Cualquier fila sincronizada por un grupo de sincronización no puede ser sincronizada por otro grupo de sincronización.

## <a name="client-agent-cannot-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>El agente cliente no se puede eliminar del portal si no se puede acceder a su base de datos local

### <a name="description-and-symptoms"></a>Descripción y síntomas

Si no se puede acceder a un punto de conexión local (es decir, a una base de datos) que está registrada con un agente cliente de SQL Data Sync (versión preliminar), el agente cliente no se puede eliminar.

### <a name="cause"></a>Causa

El agente local no se puede eliminar porque la base de datos a la que no se puede acceder sigue registrada con el agente. Al intentar eliminar el agente, el proceso de eliminación intenta acceder a la base de datos, en la que se produce un error.

### <a name="resolution-or-workaround"></a>Solución o solución alternativa

Use "Forzar eliminación" para eliminar la base de datos a la que no se puede acceder.

> [!NOTE]
> Si tras una eliminación forzada las tablas de metadatos de sincronización permanecen, use deprovisioningutil.exe para limpiarlas.

## <a name="a-sync-group-cannot-be-deleted-within-three-minutes-of-uninstallingstopping-the-agent"></a>Un grupo de sincronización no se puede eliminar dentro de los tres minutos posteriores a la desinstalación o detención del agente

### <a name="description-and-symptoms"></a>Descripción y síntomas

No puede eliminar un grupo de sincronización durante los tres minutos posteriores a la desinstalación o detención del agente cliente de SQL Data Sync (versión preliminar) asociado.

### <a name="resolution-or-workaround"></a>Solución o solución alternativa

1. Quite un grupo de sincronización mientras los agentes de sincronización asociados estén en línea (opción recomendada).

2. Si el agente está sin conexión pero instalado, póngalo en línea en el equipo local. A continuación, espere a que el estado del agente aparezca como en línea en el portal de SQL Data Sync (versión preliminar) y quite el grupo de sincronización.

3. Si el agente está sin conexión porque se ha desinstalado, haga lo siguiente. A continuación, vuelva a intentar eliminar el grupo de sincronización.

    a.  Si el archivo XML de agente existe, quítelo de la carpeta de instalación de SQL Data Sync (versión preliminar).

    b.  Instale el agente en el mismo equipo local o en otro, envíe la clave del agente desde el portal generado para el agente que aparece sin conexión.

## <a name="my-sync-group-is-stuck-in-the-processing-state"></a>Mi grupo de sincronización está atascado en el estado de procesamiento

### <a name="description-and-symptoms"></a>Descripción y síntomas

Un grupo de sincronización en SQL Data Sync (versión preliminar) ha estado en el estado de procesamiento durante un largo período de tiempo, no responde al comando de detención y los registros no muestran ninguna entrada nueva.

### <a name="causes"></a>Causas

Cualquiera de las condiciones siguientes puede hacer que un grupo de sincronización se quede atascado en el estado de procesamiento.

-   **El agente cliente no tiene conexión.** Compruebe que el agente cliente está en línea y vuelva a intentarlo.

-   **El agente cliente se ha desinstalado o no está presente.** Si el agente cliente se ha desinstalado o no está presente, haga lo siguiente:

    1. Si el archivo XML de agente existe, quítelo de la carpeta de instalación de SQL Data Sync (versión preliminar).

    2. Instale el agente en el mismo equipo local o en otro. A continuación, envíe la clave del agente desde el portal generado para el agente que se muestra como sin conexión.

-   **El servicio de SQL Data Sync (versión preliminar) se detiene.**

    1. En el menú **Inicio**, busque Servicios.

    2. En los resultados de la búsqueda, haga clic en Servicios.

    3. Busque el servicio **SQL Data Sync (versión preliminar)**.

    4. Si el estado del servicio está en **Detenido**, haga clic con el botón derecho en el nombre del servicio y seleccione **Iniciar** en el menú desplegable.

### <a name="solution-or-workaround"></a>Solución o solución alternativa

Si no puede corregir el problema, el soporte técnico de Microsoft puede restablecer el estado del grupo de sincronización. Para restablecer el estado, cree una entrada en el [foro de la base de datos de Azure SQL](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted) e incluya el identificador de suscripción y el identificador del grupo de sincronización para el grupo que deba reiniciarse. Un ingeniero de soporte técnico de Microsoft responderá a su entrada y le informará cuando se haya restablecido el estado.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre SQL Data Sync, consulte:

-   [Sincronización de datos entre varias bases de datos locales y de la nube con SQL Data Sync](sql-database-sync-data.md)
-   [Introducción a SQL Data Sync de Azure](sql-database-get-started-sql-data-sync.md)
-   [Procedimientos recomendados para SQL Data Sync de Azure](sql-database-best-practices-data-sync.md)

-   Para obtener ejemplos completos de PowerShell que muestren cómo configurar SQL Data Sync:
    -   [Uso de PowerShell para sincronizar entre varias bases de datos SQL de Azure.](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Uso de PowerShell para realizar la sincronización entre Azure SQL Database y una base de datos de SQL Server local](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Descarga de la documentación de la API de REST de SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Para más información sobre SQL Database, consulte:

-   [Información general de SQL Database](sql-database-technical-overview.md)
-   [Administración del ciclo de vida de las aplicaciones](https://msdn.microsoft.com/library/jj907294.aspx)
