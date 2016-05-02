<properties
   pageTitle="Azure AD Connect: actualización automática | Microsoft Azure"
   description="En este tema se describe la característica de actualización automática integrada en Azure AD Connect Sync."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/15/2016"
   ms.author="andkjell"/>

# Azure AD Connect: actualización automática
Esta característica se introdujo con la compilación 1.1.105.0 (publicada en febrero de 2016).

## Información general
Tener la seguridad de que la instalación de Azure AD Connect está siempre actualizada nunca ha sido más fácil con la característica de **actualización automática**. Esta característica está habilitada de forma predeterminada para las instalaciones rápidas y garantizará que cuando se lance una nueva versión, se actualice automáticamente la instalación.

La actualización automática está habilitada de forma predeterminada en los siguientes casos:

- Instalación de la configuración rápida
- Mediante SQL Express LocalDB, que es lo que siempre se usará en la configuración rápida.
- La cuenta de AD es la cuenta de MSOL\_ predeterminada creada en la configuración rápida.
- Hay menos de 100 000 objetos en el metaverso.

Se puede ver el estado actual de la actualización automática con el cmdlet de PowerShell `Get-ADSyncAutoUpgrade`. Este cmdlet tiene los siguientes estados:

| Estado | Comentario |
| ---- | ---- |
| Enabled | La actualización automática está habilitada. |
| Suspended | Solo lo establece el sistema. El sistema ya no cumple los requisitos para recibir actualizaciones automáticas. |
| Disabled | La actualización automática está deshabilitada. |

Puede cambiar entre **Habilitado** y **Deshabilitado** con `Set-ADSyncAutoUpgrade`. Solo el sistema debe establecer el estado **Suspendido**.

En la actualización automática se utiliza Azure AD Connect Health como infraestructura de actualización. Para que la actualización automática funcione correctamente, asegúrese de que ha abierto las direcciones URL en el servidor proxy para **Azure AD Connect Health**, como se documenta en [URL de Office 365 e intervalos de direcciones IP ](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Si la IU de **Synchronization Service Manager** se está ejecutando en el servidor, la actualización se suspenderá hasta que la IU se cierre.

## Solución de problemas
Si la instalación de Connect no se actualiza según lo esperado, siga estos pasos para detectar dónde se produce el error.

En primer lugar, no debe esperar que la actualización automática se intente el primer día que se lanza una nueva versión. Hay una aleatoriedad intencional antes intentar una actualización, por lo que no se alarme si la instalación no se actualiza inmediatamente.

Si piensa que algo no es correcto, primero ejecute `Get-ADSyncAutoUpgrade` para asegurarse de que la actualización automática está habilitada.

Inicie el registro de eventos y busque en **Application**. Agregue un filtro de registro de eventos para el origen **Azure AD Connect Upgrade** y el intervalo de identificadores de eventos **300-399**. ![Filtro de registro de eventos para la actualización automática](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)

De este modo, obtendrá los registros de eventos asociados con el estado de la actualización automática. ![Filtro de registro de eventos para la actualización automática](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)

La primera mitad del resultado le dará una visión general del estado.

| Prefijo de resultado | Descripción |
| --- | --- |
| Correcto | La instalación se actualizó correctamente. |
| UpgradeAborted | Una condición temporal detuvo la actualización. Se reintentará nuevo y se espera que se complete correctamente. |
| UpgradeNotSupported | El sistema tiene una configuración que está impidiendo que el sistema se actualice automáticamente. Se volverá a intentar para ver si cambia el estado, pero se espera que el sistema se actualice manualmente. |

Aquí presentamos una lista de los mensajes más comunes que encontrará. No aparecen todos, pero el mensaje de resultado debe identificar claramente el problema.

| Mensaje de resultado | Descripción |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedSyncExeInUse | La [interfaz de usuario de Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) está abierta en el servidor.
| UpgradeAbortedInsufficientDiskSpace | No hay espacio suficiente en disco para admitir una actualización. |
| UpgradeAbortedSyncCycleDisabled | La opción SyncCycle en el [programador](active-directory-aadconnectsync-feature-scheduler.md) se ha deshabilitado. |
| UpgradeAbortedSyncOrConfigurationInProgress | El asistente para la instalación se está ejecutando o se programó una sincronización al margen del programador. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedCustomizedSyncRules | Ha agregado sus propias reglas personalizadas a la configuración. |
| UpgradeNotSupportedDeviceWritebackEnabled | Se ha habilitado la característica [reescritura de dispositivos](active-directory-aadconnect-feature-device-writeback.md). |
| UpgradeNotSupportedGroupWritebackEnabled | Se ha habilitado la característica [reescritura de grupos](active-directory-aadconnect-feature-preview.md#group-writeback). |
| UpgradeNotSupportedMetaverseSizeExceeeded | Tiene más de 100.000 objetos en el metaverso. |
| UpgradeNotSupportedMultiForestSetup | Se está conectando a más de un bosque. La configuración rápida solo se conectará a un bosque. |
| UpgradeNotSupportedNonMsolAccount | La [cuenta AD Connector](active-directory-aadconnect-accounts-permissions.md#active-directory-account) ya no es la cuenta MSOL\_ predeterminada.
| UpgradeNotSupportedStagingModeEnabled | El servidor está establecido en [modo provisional](active-directory-aadconnectsync-operations.md#staging-mode). |

## Pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0420_2016-->