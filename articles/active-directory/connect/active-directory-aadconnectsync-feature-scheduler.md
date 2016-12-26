---
title: "Sincronización de Azure AD Connect: Scheduler | Microsoft Docs"
description: "En este tema se describe la característica de Programador incorporada en la sincronización de Azure AD Connect."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/04/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 41b2cdf8fec8a0e3cb83682afdfb5c974452fe47


---
# <a name="azure-ad-connect-sync-scheduler"></a>Sincronización de Azure AD Connect: Programador
En este tema se describe el programador incorporado en la sincronización de Azure AD Connect (también denominado motor de sincronización).

Esta característica se introdujo con la compilación 1.1.105.0 (publicada en febrero de 2016).

## <a name="overview"></a>Información general
La sincronización de Azure AD Connect sincronizará los cambios que ocurren en su directorio local mediante un programador. Hay dos procesos de programador, uno para la sincronización de contraseñas y otro para la sincronización de objeto o atributo y tareas de mantenimiento. Este tema se tratará el último.

En versiones anteriores el programador para objetos y atributos era externo al motor de sincronización y se utilizaba el programador de tareas de Windows o un servicio independiente de Windows para desencadenar el proceso de sincronización. En las versiones 1.1 el programador viene integrado con el motor de sincronización y permite alguna personalización. La nueva frecuencia de sincronización predeterminada es de 30 minutos.

El programador es responsable de dos tareas:

* **Ciclo de sincronización**. El proceso para importar, sincronizar y exportar los cambios.
* **Tareas de mantenimiento**. Renueve las claves y certificados para el restablecimiento de contraseña y el servicio de registro de dispositivos (DRS). Purgue las entradas antiguas en el registro de operaciones.

El programador en sí, siempre está en ejecución, pero se puede configurar para que ejecute solo una o ninguna de estas tareas. Por ejemplo si necesita tener su propio proceso de ciclo de sincronización, puede deshabilitar esta tarea en el programador pero continuar ejecutando la tarea de mantenimiento.

## <a name="scheduler-configuration"></a>Configuración del programador
Para ver la configuración actual, vaya a PowerShell y ejecute `Get-ADSyncScheduler`. Se mostrará algo como esto:

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings.png)

Si al ejecutar este cmdlet, aparece el error **The sync command or cmdlet is not available** (El comando de sincronización o el cmdlet no están disponibles), significa que el módulo de PowerShell no está cargado. Esto podría suceder si ejecuta Azure AD Connect en un controlador de dominio o en un servidor con niveles de restricción para PowerShell más elevados que la configuración predeterminada. Si ve este error, ejecute `Import-Module ADSync` para que esté disponible el cmdlet.

* **AllowedSyncCycleInterval**. En la mayoría de los casos Azure AD permitirá que se produzcan las sincronizaciones. No se puede sincronizar con más frecuencia que esto y mantener la compatibilidad.
* **CurrentlyEffectiveSyncCycleInterval**. La programación en vigor actualmente. Tendrá el mismo valor que CustomizedSyncInterval (si está establecido) si no es más frecuente que AllowedSyncInterval. Si cambia CustomizedSyncCycleInterval, esto surtirá efecto tras el próximo ciclo de sincronización.
* **CustomizedSyncCycleInterval**. Si desea que el programador se ejecute con cualquier otra frecuencia distinta a la del valor predeterminado de 30 minutos, configure este valor. En la imagen anterior, el programador se ha establecido para que se ejecute cada hora. Si se establece en un valor inferior al de AllowedSyncInterval, se utilizará el último.
* **NextSyncCyclePolicyType**. Diferencial o inicial. Define si la siguiente ejecución debe procesar solo cambios diferenciales, o si esta debería hacer una importación y sincronización completas que a su vez volvería a procesar las reglas nuevas o modificadas.
* **NextSyncCycleStartTimeInUTC**. La hora en que el programador iniciará el siguiente ciclo de sincronización.
* **PurgeRunHistoryInterval**. El tiempo que deben mantenerse los registros de operación. Estos se pueden revisar en el Synchronization Service Manager. El valor predeterminado es de 7 días.
* **SyncCycleEnabled**. Indica si el programador está ejecutando los procesos de importación, sincronización y exportación como parte de su funcionamiento.
* **MaintenanceEnabled**. Muestra si está habilitado el proceso de mantenimiento. Actualizará los certificados o claves y purgará el registro de operaciones.
* **IsStagingModeEnabled**. Muestra si el [modo provisional](active-directory-aadconnectsync-operations.md#staging-mode) está habilitado.

Puede cambiar algunos de estos valores con `Set-ADSyncScheduler`. Se pueden modificar los parámetros siguientes:

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

La configuración del programador se almacena en Azure AD. Si tiene un servidor de almacenamiento provisional, cualquier cambio realizado en el servidor principal también afectará este servidor (a excepción de IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Sintaxis: `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
 d: días; HH: horas; mm: minutos; ss: segundos

Ejemplo: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
 cambiará el programador para ejecutarse cada 3 horas.

Ejemplo: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
 cambiará el programador para ejecutarse a diario.

## <a name="start-the-scheduler"></a>Inicio del programador
De forma predeterminada, el programador se ejecutará cada 30 minutos. En algunos casos puede querer ejecutar un ciclo de sincronización entre los ciclos programados o debe ejecutar un tipo diferente.

**Ciclo de sincronización diferencial**  
 Un ciclo de sincronización diferencial incluye los siguientes pasos:

* Importación diferencial en todos los conectores
* Sincronización diferencial en todos los conectores
* Exportación en todos los conectores

Es posible que haya un cambio urgente que debe sincronizar inmediatamente para lo cual necesita ejecutar manualmente un ciclo. Si necesita ejecutar manualmente un ciclo, ejecute `Start-ADSyncSyncCycle -PolicyType Delta`desde PowerShell.

**Ciclo de sincronización completo**  
Si ha realizado uno de los siguientes cambios de configuración, debe ejecutar un ciclo de sincronización completo (también conocido como sincronización inicial):

* Agregó más objetos o atributos para su importación desde un directorio de origen
* Realizó cambios en las reglas de sincronización
* Cambió el [filtrado](active-directory-aadconnectsync-configure-filtering.md) para que se incluya un número diferente de objetos

Si ha realizado uno de estos cambios, debe ejecutar un ciclo de sincronización completo, para que el motor de sincronización tenga la oportunidad de volver a consolidar los espacios de conector. Un ciclo de sincronización completo incluye los pasos siguientes:

* Importación completa en todos los conectores
* Sincronización completa en todos los conectores
* Exportación en todos los conectores

Para iniciar un ciclo de sincronización completo, ejecute `Start-ADSyncSyncCycle -PolicyType Initial` desde un símbolo del sistema de PowerShell. Esto iniciará un ciclo de sincronización completo.

## <a name="stop-the-scheduler"></a>Detención del programador
Si el programador está ejecutando actualmente un ciclo de sincronización puede que necesite detenerlo. Por ejemplo, si inicia el Asistente para instalación y recibe este error:

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

Cuando se está ejecutando un ciclo de sincronización, no puede realizar cambios de configuración. Debe esperar hasta que el programador haya terminado el proceso, pero también es posible detenerlo para poder realizar los cambios inmediatamente. Detener el ciclo actual no es perjudicial y los cambios que aún no se hayan procesado se procesarán en la próxima ejecución.

1. Para empezar, indique al programador que detenga el ciclo actual con el cmdlet `Stop-ADSyncSyncCycle`de PowerShell.
2. La detención del programador no hará que el conector en cuestión detenga su tarea actual. Para forzar el conector a que se detenga, tome las medidas siguientes: ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
   * Inicie el **Servicio de sincronización** desde el menú Inicio. Vaya a **Conectores**, seleccione el conector con el estado **En ejecución** y seleccione **Detener** en la lista de acciones.

El programador permanecerá todavía activo y se iniciará de nuevo a la siguiente oportunidad.

## <a name="custom-scheduler"></a>Programador personalizado
Los cmdlets que se documentan en esta sección solo están disponibles en la compilación [1.1.130.0](active-directory-aadconnect-version-history.md#111300) y versiones posteriores.

Si el programador integrado no cumple con sus requisitos, puede programar los conectores con PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
Puede iniciar un perfil para un conector de esta manera:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Los nombres para utilizar como [nombres de conectores](active-directory-aadconnectsync-service-manager-ui-connectors.md) y [nombres de perfil de ejecución](active-directory-aadconnectsync-service-manager-ui-connectors.md#configure-run-profiles) pueden encontrarse en la [interfaz de usuario de Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md).

![Invocar el perfil de ejecución](./media/active-directory-aadconnectsync-feature-scheduler/invokerunprofile.png)  

El cmdlet `Invoke-ADSyncRunProfile` es sincrónico; es decir, no devolverá el control hasta que el conector haya completado la operación, ya sea correcta o incorrectamente.

Al programar los conectores, se recomienda hacerlo en el siguiente orden:

1. (Diferencial y completo) Importar desde directorios locales, como Active Directory
2. (Diferencial y completo) Importar desde Azure AD
3. (Diferencial y completo) Sincronización desde directorios locales, como Active Directory
4. (Diferencial y completo) Sincronización desde Azure AD
5. Exportar a Azure AD
6. Exportar a los directorios locales, como Active Directory

Si observa el programador integrado, este es el orden en que se ejecutarán los conectores.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
También puede supervisar el motor de sincronización para ver si está ocupado o inactivo. Este cmdlet devolverá un resultado vacío si el motor de sincronización está inactivo y no está ejecutando un conector. Si está ejecutando un conector, devolverá el nombre del conector.

```
Get-ADSyncConnectorRunStatus
```

![Estado de la ejecución de conector](./media/active-directory-aadconnectsync-feature-scheduler/getconnectorrunstatus.png)  
 En la ilustración anterior, la primera línea refleja un estado donde el motor de sincronización está inactivo. La segunda línea es de cuando se ejecuta el conector de Azure AD.

## <a name="scheduler-and-installation-wizard"></a>Programador y Asistente para instalación
Si inicia el Asistente para instalación el programador se suspenderá temporalmente. Esto es debido a que se supone que realizará los cambios de configuración y estos no se pueden aplicar si el motor de sincronización se está ejecutando activamente. Por este motivo, no deje el Asistente para instalación abierto ya que impedirá al motor de sincronización realizar ninguna acción.

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la configuración de la [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).




<!--HONumber=Nov16_HO3-->


