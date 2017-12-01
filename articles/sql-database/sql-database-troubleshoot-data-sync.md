---
title: "Solución de problemas de Azure SQL Data Sync (versión preliminar) | Microsoft Docs"
description: "Aprenda a solucionar problemas comunes con Azure SQL Data Sync (versión preliminar)."
services: sql-database
ms.date: 11/13/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 50cabbaa584671e52c1ea7efbd2ad990b8438272
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2017
---
# <a name="troubleshoot-issues-with-sql-data-sync-preview"></a>Solución de problemas de SQL Data Sync (versión preliminar)

En este artículo se describe cómo solucionar problemas conocidos con Azure SQL Data Sync (versión preliminar). Si hay una solución para un problema, la encontrará aquí.

Para obtener información general sobre SQL Data Sync (versión preliminar), vea [Sincronización de datos entre varias bases de datos locales y de la nube con SQL Data Sync de Azure (versión preliminar)](sql-database-sync-data.md).

## <a name="sync-issues"></a>Problemas de sincronización

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a>Se produce un error de sincronización en la interfaz de usuario del portal para las bases de datos locales asociadas al agente cliente

#### <a name="description-and-symptoms"></a>Descripción y síntomas

Se produce un error de sincronización en la interfaz de usuario del portal de SQL Data Sync (versión preliminar) para las bases de datos locales asociadas al agente. En el registro de eventos del equipo local que ejecuta el agente, verá los errores System.IO.IOException. Estos errores indican que el disco no tiene espacio suficiente.

#### <a name="resolution"></a>Resolución

Cree más espacio en la unidad en la que se encuentra el directorio %TEMP%.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a>Mi grupo de sincronización está atascado en el estado de procesamiento

#### <a name="description-and-symptoms"></a>Descripción y síntomas

Un grupo de sincronización de SQL Data Sync (versión preliminar) lleva mucho tiempo en el estado de procesamiento. No responde al comando **stop** y los registros no muestran ninguna entrada nueva.

#### <a name="cause"></a>Causa

Cualquiera de las condiciones siguientes puede hacer que un grupo de sincronización se quede atascado en el estado de procesamiento:

-   **El agente cliente no tiene conexión**. Compruebe que el agente cliente está en línea y vuelva a intentarlo.

-   **El agente cliente se ha desinstalado o no está presente**. Si el agente cliente se ha desinstalado o no está presente, haga lo siguiente:

    1. Si el archivo XML de agente existe, quítelo de la carpeta de instalación de SQL Data Sync (versión preliminar).
    2. Instale el agente en un equipo local (puede ser el mismo o un equipo diferente). A continuación, envíe la clave del agente que se genera en el portal para el agente que se muestra como sin conexión.

-   **El servicio de SQL Data Sync se detiene**.

    1. En el menú **Inicio**, busque **Servicios**.
    2. En los resultados de la búsqueda, haga clic en **Servicios**.
    3. Busque el servicio **SQL Data Sync (versión preliminar)**.
    4. Si el estado del servicio es **Detenido**, haga clic con el botón derecho en el nombre del servicio y, después, seleccione **Iniciar**.

#### <a name="resolution"></a>Resolución

Si la información anterior no hace que el grupo de sincronización salga del estado de procesamiento, el Soporte técnico de Microsoft puede restablecer el estado del grupo de sincronización. Para restablecer el estado del grupo de sincronización, publique un mensaje en el [foro de Azure SQL Database](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted). En este mensaje, incluya el identificador de suscripción y el identificador del grupo de sincronización que es necesario reiniciar. Un ingeniero de Soporte técnico de Microsoft responderá a su mensaje y le informará cuando se haya restablecido el estado.

### <a name="i-see-erroneous-data-in-my-tables"></a>Veo datos erróneos en las tablas

#### <a name="description-and-symptoms"></a>Descripción y síntomas

Si se incluyen en una sincronización tablas que tienen el mismo nombre pero que son de esquemas de base de datos diferentes, verá datos erróneos en las tablas después de la sincronización.

#### <a name="cause"></a>Causa

Para el proceso de aprovisionamiento de SQL Data Sync (versión preliminar), se usan las mismas tablas de seguimiento para las tablas con el mismo nombre pero con esquemas distintos. Por este motivo, los cambios de ambas tablas se reflejan en la misma tabla de seguimiento. Esto provoca cambios de datos erróneos durante la sincronización.

#### <a name="resolution"></a>Resolución

Asegúrese de que los nombres de las tablas implicadas en una sincronización sean diferentes, aunque las tablas pertenezcan a esquemas de base de datos distintos.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a>Tras una sincronización correcta, veo datos clave principales incoherentes

#### <a name="description-and-symptoms"></a>Descripción y síntomas

Una sincronización se notifica como correcta, y en su registro no se muestran filas con errores ni omitidas, pero observa que los datos clave principales son incoherentes entre las bases de datos del grupo de sincronización.

#### <a name="cause"></a>Causa

Este resultado es así por naturaleza. Cualquier cambio realizado en una clave principal tiene como resultado datos incoherentes en las filas en las que se ha cambiado la clave principal.

#### <a name="resolution"></a>Resolución

Para evitar este problema, asegúrese de que no se modifica ningún dato en la columna de clave principal.

Para corregir este problema después de que se haya producido, elimine la fila que tiene datos incoherentes de todos los puntos de conexión del grupo de sincronización. A continuación, vuelva a insertar la fila.

### <a name="i-see-a-significant-degradation-in-performance"></a>Veo una degradación significativa del rendimiento

#### <a name="description-and-symptoms"></a>Descripción y síntomas

El rendimiento disminuye considerablemente, posiblemente hasta tal punto que ni siquiera puede abrir la interfaz de usuario de Data Sync.

#### <a name="cause"></a>Causa

La causa más probable es que exista un bucle de sincronización. Un bucle de sincronización se produce cuando una sincronización del grupo de sincronización A desencadena una sincronización del grupo de sincronización B, y esto desencadena una sincronización del grupo de sincronización A. La situación real puede ser más compleja e implicar a más de dos grupos de sincronización del bucle. El problema es que hay un desencadenamiento circular de sincronización que se debe a que los grupos de sincronización se superponen entre sí.

#### <a name="resolution"></a>Resolución

La mejor corrección es la prevención. Asegúrese de que no haya referencias circulares en los grupos de sincronización. Cualquier fila sincronizada por un grupo de sincronización no puede ser sincronizada por otro grupo de sincronización.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a>He recibido el mensaje "No se puede insertar el valor NULL en la columna \<columna\>. La columna no admite valores NULL." ¿Qué significa esto y cómo puedo corregir este error? 
Este mensaje de error indica que se ha producido uno de los dos problemas siguientes:
-  Una tabla no tiene ninguna clave principal. Para corregir este problema, agregue una clave principal a todas las tablas que va a sincronizar.
-  Hay una cláusula WHERE en la instrucción CREATE INDEX. Data Sync (versión preliminar) no controla esta condición. Para solucionar este problema, quite la cláusula WHERE o realice los cambios manualmente en todas las bases de datos. 
 
### <a name="how-does-data-sync-preview-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>¿Cómo trata Data Sync (versión preliminar) las referencias circulares? En otras palabras, ¿cuándo se sincronizan los mismos datos en varios grupos de sincronización y sigue cambiando como resultado?
Data Sync (versión preliminar) no controla las referencias circulares, así que asegúrese de no usarlas. 

## <a name="client-agent-issues"></a>Problemas del agente cliente

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a>La instalación, desinstalación o reparación del agente cliente produce un error

#### <a name="cause"></a>Causa

Muchos escenarios pueden provocar este error. Para determinar cuál es la causa concreta, debe examinar los registros.

#### <a name="resolution"></a>Resolución

Para encontrar la causa específica del error, debe generar y examinar los registros de Windows Installer. Puede activar el registro desde el símbolo del sistema. Por ejemplo, si el archivo AgentServiceSetup.msi descargado es LocalAgentHost.msi, genere y examine los archivos de registro mediante el uso de las siguientes líneas de comandos:

-   Para las instalaciones: `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`
-   Para las desinstalaciones: `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

También puede activar el registro para todas las instalaciones realizadas por Windows Installer. En el artículo de Microsoft Knowledge Base [Cómo habilitar el registro de Windows Installer](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) se proporciona una solución de un solo clic para activar el registro en Windows Installer. También indica la ubicación de los registros.

### <a name="my-client-agent-doesnt-work"></a>Mi agente cliente no funciona

#### <a name="description-and-symptoms"></a>Descripción y síntomas

Al intentar utilizar el agente cliente, recibe los siguientes mensajes:

"Error de sincronización con la excepción 'Se ha producido un error al intentar deserializar el parámetro www.microsoft.com/.../05:GetBatchInfoResult'. Consulte InnerException para obtener más información".

"Mensaje de excepción interna: 'Microsoft.Synchronization.ChangeBatch' es un tipo de colección no válido porque no tiene ningún constructor predeterminado".

#### <a name="cause"></a>Causa

Se trata de un problema conocido de la instalación de SQL Data Sync (versión preliminar). La causa más probable de este mensaje es una de las siguientes:

-   está ejecutando Windows 8 Developer Preview, o
-   tiene instalado .NET Framework 4.5.

#### <a name="resolution"></a>Resolución

Asegúrese de instalar el agente cliente en un equipo que no esté ejecutando Windows 8 Developer Preview y que no tenga instalado .NET Framework 4.5.

### <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>El agente cliente no funciona cuando cancelo la desinstalación

#### <a name="description-and-symptoms"></a>Descripción y síntomas

El agente cliente no funciona aunque cancele su desinstalación.

#### <a name="cause"></a>Causa

Este problema se produce porque el agente cliente de SQL Data Sync (versión preliminar) no almacena credenciales.

#### <a name="resolution"></a>Resolución

Puede probar estas dos soluciones:

-   Use services.msc para volver a introducir las credenciales para el agente cliente.
-   Desinstale este agente cliente y, después, instale uno nuevo. Descargue e instale el agente cliente más reciente del [Centro de descarga](http://go.microsoft.com/fwlink/?linkid=221479).

### <a name="my-database-isnt-listed-in-the-agent-list"></a>Mi base de datos no aparece en la lista del agente

#### <a name="description-and-symptoms"></a>Descripción y síntomas

Al intentar agregar una base de datos existente de SQL Server a un grupo de sincronización, la base de datos no aparece en la lista de agentes.

#### <a name="cause"></a>Causa

Estos escenarios pueden provocar este error:

-   El agente cliente y el grupo de sincronización se encuentran en distintos centros de datos.
-   La lista de agentes cliente de la base de datos no está actualizada.

#### <a name="resolution"></a>Resolución

La resolución dependerá de la causa del problema.

- **El agente cliente y el grupo de sincronización se encuentran en distintos centros de datos**

    El agente cliente y el grupo de sincronización deben estar en el mismo centro de datos. Para configurar esto, tiene dos opciones:

    -   Cree un nuevo agente en el centro de datos donde se encuentra el grupo de sincronización. Registre la base de datos con ese agente.
    -   Elimine el grupo de sincronización actual. A continuación, vuelva a crear el grupo de sincronización en el centro de datos donde se encuentra el agente.

- **La lista de agentes cliente de la base de datos no está actualizada**

    Detenga y reinicie el servicio de agente cliente.

    El agente local descarga la lista de bases de datos asociadas solo la primera vez que se envía la clave del agente. No descarga la lista de bases de datos asociadas en los envíos posteriores de la clave del agente. Las bases de datos que se registran durante un traslado del agente no aparecen en la instancia original del agente.

### <a name="client-agent-doesnt-start-error-1069"></a>El agente cliente no se inicia (error 1069)

#### <a name="description-and-symptoms"></a>Descripción y síntomas

El agente no se está ejecutando en un equipo que hospeda SQL Server. Al intentar iniciar manualmente el agente, ve un cuadro de diálogo con el mensaje "Error 1069: el servicio no se inició debido a un error de inicio de sesión".

![Cuadro de diálogo del error 1069 de Data Sync](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

#### <a name="cause"></a>Causa

Una causa probable de este error es que la contraseña del servidor local ha cambiado desde que creó el agente y la contraseña del agente.

#### <a name="resolution"></a>Resolución

Actualice la contraseña del agente con su contraseña actual del servidor:

1. Busque el servicio de vista previa de agentes cliente de SQL Data Sync (versión preliminar).  
    a. Seleccione **Iniciar**.  
    b. En el cuadro de búsqueda, escriba **services.msc**.  
    c. En los resultados de la búsqueda, haga clic en **Servicios**.  
    d. En la ventana **Servicios**, desplácese hasta la entrada para la **vista previa de agentes de SQL Data Sync (versión preliminar)**.  
2. Haga clic con el botón derecho en **Vista previa de agentes de SQL Data Sync (versión preliminar)** y, después, seleccione **Detener**.
3. Haga clic con el botón derecho en **Vista previa de agentes de SQL Data Sync (versión preliminar)** y, después, seleccione **Propiedades**.
4. En **Propiedades de vista previa de agentes de SQL Data Sync (versión preliminar)**, seleccione la pestaña **Inicio de sesión**.
5. En el cuadro de texto **Contraseña**, escriba su contraseña.
6. En el cuadro de texto **Confirmar contraseña**, vuelva a escribir su contraseña.
7. Seleccione **Aplicar** y luego **Aceptar**.
8. En la ventana **Servicios**, haga clic con el botón derecho en el servicio **Vista previa de agentes de SQL Data Sync (versión preliminar)** y, después, haga clic en **Iniciar**.
9. Cierre la ventana **Servicios**.

### <a name="i-cant-submit-the-agent-key"></a>No puedo enviar la clave del agente

#### <a name="description-and-symptoms"></a>Descripción y síntomas

Después de crear o volver a crear una clave para un agente, intenta enviar esa clave a través de la aplicación SqlAzureDataSyncAgent y no se puede completar el envío.

![Cuadro de diálogo de error de sincronización: no se puede enviar la clave del agente](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

Antes de continuar, compruebe las condiciones siguientes:

-   El servicio de Windows SQL Data Sync (versión preliminar) se está ejecutando.  
-   La cuenta del servicio de Windows de vista previa de SQL Data Sync (versión preliminar) tiene acceso a la red.    
-   El agente cliente puede ponerse en contacto con el servicio de ubicación. Compruebe que la siguiente clave de registro tiene el valor https://locator.sync.azure.com/LocatorServiceApi.svc:  
    -   En un equipo x86: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`  
    -   En un equipo x64: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

#### <a name="cause"></a>Causa

La clave del agente identifica de forma única cada agente local. La clave debe cumplir dos condiciones:

-   La clave del agente cliente del servidor SQL Data Sync (versión preliminar) y del equipo local debe ser idéntica.
-   La clave del agente cliente solo se puede usar una vez.

#### <a name="resolution"></a>Resolución

Si el agente no funciona, es porque una de estas condiciones o ninguna de las dos se cumplen. Para que el agente vuelva a funcionar:

1. Genere una nueva clave.
2. Aplique la nueva clave al agente.

Para aplicar la nueva clave al agente:

1. En el Explorador de archivos, vaya al directorio de instalación del agente. El directorio de instalación predeterminado es C:\\Archivos de programa (x86)\\Microsoft SQL Data Sync.
2. Haga doble clic en el subdirectorio bin.
3. Abra la aplicación SqlAzureDataSyncAgent.
4. Seleccione **Enviar clave del agente**.
5. Pegue la clave del portapapeles en el espacio proporcionado.
6. Seleccione **Aceptar**.
7. Cierre el programa.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>El agente cliente no se puede eliminar del portal si no se puede acceder a su base de datos local

#### <a name="description-and-symptoms"></a>Descripción y síntomas

Si no se puede acceder a un punto de conexión local (es decir, a una base de datos) que está registrada con un agente cliente de SQL Data Sync (versión preliminar), el agente cliente no se puede eliminar.

#### <a name="cause"></a>Causa

El agente local no se puede eliminar porque la base de datos a la que no se puede acceder sigue registrada con el agente. Al intentar eliminar el agente, el proceso de eliminación intenta acceder a la base de datos, en la que se produce un error.

#### <a name="resolution"></a>Resolución

Use "Forzar eliminación" para eliminar la base de datos a la que no se puede acceder.

> [!NOTE]
> Si tras una eliminación forzada, las tablas de metadatos de sincronización permanecen, use deprovisioningutil.exe para limpiarlas.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a>La aplicación local del agente de sincronización no se puede conectar al servicio de sincronización local

#### <a name="resolution"></a>Resolución

Realice estos pasos:

1. Salga de la aplicación.  
2. Abra el panel Servicios de componentes.  
    a. En el cuadro de búsqueda de la barra de tareas, escriba **services.msc**.  
    b. En los resultados de la búsqueda, haga doble clic en **Servicios**.  
3. Detenga el servicio **Vista previa de SQL Data Sync (versión preliminar)**.
4. Reinicie el servicio **Vista previa de SQL Data Sync (versión preliminar)**.  
5. Vuelva a abrir la aplicación.

## <a name="setup-and-maintenance-issues"></a>Problemas de configuración y mantenimiento

### <a name="i-get-a-disk-out-of-space-message"></a>Aparece un mensaje en el que se indica "espacio insuficiente en disco"

#### <a name="cause"></a>Causa

El mensaje "espacio insuficiente en disco" puede aparecer si es necesario eliminar archivos sobrantes. Esto puede deberse a un software antivirus o a archivos abiertos cuando se intentan realizar operaciones de eliminación.

#### <a name="resolution"></a>Resolución

Elimine manualmente los archivos de sincronización que se encuentran en la carpeta %temp% (`del \*sync\* /s`). A continuación, elimine los subdirectorios de la carpeta %temp%.

> [!IMPORTANT]
> No elimine los archivos mientras la sincronización esté en curso.

### <a name="i-cant-delete-my-sync-group"></a>No puedo eliminar mi grupo de sincronización

#### <a name="description-and-symptoms"></a>Descripción y síntomas

Se produce un error al intentar eliminar un grupo de sincronización.

#### <a name="causes"></a>Causas

El error al eliminar un grupo de sincronización se puede producir en cualquier de los siguientes escenarios:

-   El agente cliente no tiene conexión.
-   El agente cliente se ha desinstalado o no está presente. 
-   Una base de datos está sin conexión. 
-   El grupo de sincronización se está aprovisionando o sincronizando. 

#### <a name="resolution"></a>Resolución

Para resolver el error al eliminar un grupo de sincronización:

-   Asegúrese de que el agente cliente está en línea y vuelva a intentarlo.
-   Si el agente cliente se ha desinstalado o no está presente, haga lo siguiente:  
    a. Si el archivo XML de agente existe, quítelo de la carpeta de instalación de SQL Data Sync (versión preliminar).  
    b. Instale el agente en un equipo local (puede ser el mismo o un equipo diferente). A continuación, envíe la clave del agente que se genera en el portal para el agente que se muestra como sin conexión.
-   El servicio de SQL Data Sync (versión preliminar) se está ejecutando:  
    a. En el menú **Inicio**, busque **Servicios**.  
    b. En los resultados de la búsqueda, haga clic en **Servicios**.  
    c. Busque el servicio **Vista previa de SQL Data Sync (versión preliminar)**.  
    d. Si el estado del servicio es **Detenido**, haga clic con el botón derecho en el nombre del servicio y, después, seleccione **Iniciar**.
-   Asegúrese de que las bases de datos de SQL Database y SQL Server están todas en línea.
-   Espere a que finalice el proceso de aprovisionamiento o sincronización y, después, vuelva a intentar eliminar el grupo de sincronización.

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>No puedo anular el registro de una base de datos de SQL Server local

#### <a name="cause"></a>Causa

Es muy probable que esté intentando anular el registro de una base de datos que ya se ha eliminado.

#### <a name="resolution"></a>Resolución

Para anular el registro de una base de datos de SQL Server local, seleccione la base de datos y, después, haga clic en **Forzar eliminación**.

Si con esta operación no se puede quitar la base de datos del grupo de sincronización:

1. Detenga y reinicie el servicio de host del agente cliente:  
    a. Seleccione el menú **Inicio**.  
    b. En el cuadro de búsqueda, escriba **services.msc**.  
    c. En la sección **Programas** del panel de resultados, haga doble clic en **Servicios**.  
    d. Haga clic con el botón derecho en el servicio **SQL Data Sync (versión preliminar)**.  
    e. Si el servicio se está ejecutando, deténgalo.  
    f. Haga clic con el botón derecho en el servicio y, después, seleccione **Iniciar**.  
    g. Compruebe si la base de datos sigue registrada. Si ya no lo está, habrá terminado. En caso contrario, continúe con el paso siguiente.
2. Abra la aplicación de agente cliente (SqlAzureDataSyncAgent).
3. Seleccione **Editar credenciales** y, después, escriba las credenciales de la base de datos.
4. Continúe con la anulación del registro.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a>No tengo suficientes privilegios para iniciar los servicios del sistema

#### <a name="cause"></a>Causa

Este error se produce en dos situaciones:
-   El nombre de usuario o la contraseña son incorrectos.
-   La cuenta de usuario especificada no tiene suficientes privilegios para iniciar sesión como servicio.

#### <a name="resolution"></a>Resolución

Conceda a la cuenta de usuario credenciales para iniciar sesión como servicio:

1. Vaya a **Inicio** > **Panel de control** > **Herramientas administrativas** > **Directiva de seguridad local** > **Directiva local** > **User Rights Management** (Administración de los permisos del usuario).
2. Seleccione **Iniciar sesión como servicio**.
3. En el cuadro de diálogo **Propiedades**, agregue la cuenta de usuario.
4. Seleccione **Aplicar** y luego **Aceptar**.
5. Cierre todas las ventanas.

### <a name="a-database-has-an-out-of-date-status"></a>Una base de datos tiene como estado "Obsoleto"

#### <a name="cause"></a>Causa

SQL Data Sync (versión preliminar) quita del servicio las bases de datos que han estado sin conexión durante 45 días o más (contados a partir del momento en que la base de datos quedó sin conexión). Si una base de datos está sin conexión durante 45 días o más y después vuelve a conectarse, su estado se establece en **Obsoleto**.

#### <a name="resolution"></a>Resolución

Para evitar un estado **Obsoleto**, asegúrese de que ninguna de las bases de datos esté sin conexión durante 45 días o más.

Si el estado de una base de datos es **Obsoleto**:

1. Quite la base de datos con un estado **Obsoleto** del grupo de sincronización.
2. Vuelva a agregar la base de datos al grupo de sincronización.

> [!WARNING]
> Perderá todos los cambios que haya realizado en la base de datos mientras estaba sin conexión.

### <a name="a-sync-group-has-an-out-of-date-status"></a>Un grupo de sincronización tiene como estado "Obsoleto"

#### <a name="cause"></a>Causa

Un grupo de sincronización puede quedar obsoleto si uno o más cambios no se pueden aplicar durante todo el período de retención de 45 días.

#### <a name="resolution"></a>Resolución

Para evitar el estado **Obsoleto** de un grupo de sincronización, examine de forma regular los resultados de los trabajos de sincronización en el visor del historial. Averigüe qué cambios no se pudieron aplicar y resuélvalos.

Si el estado de un grupo de sincronización es **Obsoleto**, tiene que eliminar el grupo de sincronización y volver a crearlo.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a>Un grupo de sincronización no se puede eliminar durante los tres minutos posteriores a la desinstalación o detención del agente

#### <a name="description-and-symptoms"></a>Descripción y síntomas

No puede eliminar un grupo de sincronización durante los tres minutos posteriores a la desinstalación o detención del agente cliente de SQL Data Sync (versión preliminar) asociado.

#### <a name="resolution"></a>Resolución

1. Quite un grupo de sincronización mientras los agentes de sincronización asociados estén en línea (opción recomendada).
2. Si el agente está sin conexión pero instalado, póngalo en línea en el equipo local. Espere a que el estado del agente aparezca como **en línea** en el portal de SQL Data Sync (versión preliminar). A continuación, quite el grupo de sincronización.
3. Si el agente está sin conexión porque se ha desinstalado:  
    a.  Si el archivo XML de agente existe, quítelo de la carpeta de instalación de SQL Data Sync (versión preliminar).  
    b.  Instale el agente en un equipo local (puede ser el mismo o un equipo diferente). A continuación, envíe la clave del agente que se genera en el portal para el agente que se muestra como sin conexión.  
    c. Intente eliminar el grupo de sincronización.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a>¿Qué ocurre cuando se restaura una base de datos perdida o dañada?

Si restaura una base de datos perdida o dañada desde una copia de seguridad, puede que los datos de los grupos de sincronización a los que pertenece la base de datos no coincidan.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de SQL Data Sync (versión preliminar), vea:

-   [Sincronización de datos entre varias bases de datos locales y de la nube con SQL Data Sync de Azure (versión preliminar)](sql-database-sync-data.md)  
-   [Configuración de Azure SQL Data Sync (versión preliminar)](sql-database-get-started-sql-data-sync.md)  
-   [Procedimientos recomendados para SQL Data Sync de Azure (versión preliminar)](sql-database-best-practices-data-sync.md)  
-   [Supervisar SQL Data Sync de Azure con Log Analytics de OMS](sql-database-sync-monitor-oms.md)  
-   Para obtener ejemplos completos de PowerShell que muestren cómo configurar SQL Data Sync (versión preliminar):  
    -   [Uso de PowerShell para sincronizar entre varias bases de datos SQL de Azure.](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Uso de PowerShell para realizar la sincronización entre Azure SQL Database y una base de datos de SQL Server local](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [Descarga de la documentación de la API de REST de SQL Data Sync (versión preliminar)](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Para obtener más información acerca de SQL Database, vea:

-   [Información general de SQL Database](sql-database-technical-overview.md)
-   [Administración del ciclo de vida de las aplicaciones](https://msdn.microsoft.com/library/jj907294.aspx)
