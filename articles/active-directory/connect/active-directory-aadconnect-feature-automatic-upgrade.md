---
title: "Azure AD Connect: actualización automática | Microsoft Docs"
description: "En este tema se describe la característica de actualización automática integrada en la sincronización de Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: ddf23193e7b9c2fb7a69045be539841dbb6e6249
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: actualización automática
Esta característica se introdujo con la compilación 1.1.105.0 (publicada en febrero de 2016).

## <a name="overview"></a>Información general
Tener la seguridad de que la instalación de Azure AD Connect está siempre actualizada nunca ha sido más fácil con la característica de **actualización automática** . Esta característica está habilitada de forma predeterminada para realizar instalaciones rápidas y actualizaciones de DirSync. Cuando se publica una nueva versión, se actualiza automáticamente la instalación.
La actualización automática está habilitada de forma predeterminada en los siguientes casos:

* Instalación de la configuración rápida y actualizaciones de sincronización de directorios.
* Uso de SQL Express LocalDB, que es el modo de ejecución que siempre se utiliza en la configuración rápida. DirSync con SQL Express LocalDB también utiliza LocalDB.
* La cuenta de AD es la cuenta de MSOL_ predeterminada creada en la configuración rápida y DirSync.
* Tiene menos de 100 000 objetos en el metaverso.

Se puede ver el estado actual de la actualización automática con el cmdlet de PowerShell `Get-ADSyncAutoUpgrade`. Este cmdlet tiene los siguientes estados:

| Estado | Comentario |
| --- | --- |
| Enabled |La actualización automática está habilitada. |
| Suspended |Solo lo establece el sistema. El sistema ya no cumple los requisitos para recibir actualizaciones automáticas. |
| Disabled |La actualización automática está deshabilitada. |

Puede cambiar entre **Habilitado** y **Deshabilitado** con `Set-ADSyncAutoUpgrade`. Solo el sistema debe establecer el estado **Suspendido**.

En la actualización automática se utiliza Azure AD Connect Health como infraestructura de actualización. Para que la actualización automática funcione, asegúrese de que ha abierto las direcciones URL en el servidor proxy de **Azure AD Connect Health** , tal y como se documenta en [URL de Office 365 e intervalos de direcciones IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Si la IU de **Synchronization Service Manager** se está ejecutando en el servidor, la actualización se suspenderá hasta que la IU se cierre.

## <a name="troubleshooting"></a>solución de problemas
Si la instalación de Connect no se actualiza según lo esperado, siga estos pasos para detectar dónde se produce el error.

En primer lugar, no debe esperar que la actualización automática se intente el primer día que se lanza una nueva versión. Hay una aleatoriedad intencional antes intentar una actualización, por lo que no se alarme si la instalación no se actualiza inmediatamente.

Si piensa que algo no es correcto, primero ejecute `Get-ADSyncAutoUpgrade` para asegurarse de que la actualización automática está habilitada.

A continuación, asegúrese de que ha abierto las direcciones URL necesarias en su servidor proxy o firewall. La actualización automática utiliza Azure AD Connect Health, como se describe en la [información general](#overview). Si utiliza un servidor proxy, asegúrese de que Health se ha configurado para usar un [servidor proxy](../connect-health/active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Pruebe también la [conectividad de Health](../connect-health/active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) con Azure AD.

Una vez verificada la conectividad a Azure AD, es el momento de buscar en los registros de eventos. Inicie el visor de eventos y busque en el registro de eventos **Application** . Agregue un filtro de registro de eventos para el origen **Azure AD Connect Upgrade** y el intervalo de identificadores de eventos **300-399**.  
![Filtro de registro de eventos para la actualización automática](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)  

Ahora puede ver los registros de eventos asociados con el estado de la actualización automática.  
![Filtro de registro de eventos para la actualización automática](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)  

El código de resultado tiene un prefijo con información general sobre el estado.

| Prefijo del código de resultado | DESCRIPCIÓN |
| --- | --- |
| Correcto |La instalación se actualizó correctamente. |
| UpgradeAborted |Una condición temporal detuvo la actualización. Se reintentará nuevo y se espera que se complete correctamente más tarde. |
| UpgradeNotSupported |El sistema tiene una configuración que está impidiendo que el sistema se actualice automáticamente. Se volverá a intentar para ver si cambia el estado, pero se espera que el sistema se actualice manualmente. |

Aquí presentamos una lista de los mensajes más comunes que se encuentran. No aparecen todos, pero el mensaje de resultado debe identificar claramente el problema.

| Mensaje de resultado | DESCRIPCIÓN |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |No se pudo escribir en el registro. |
| UpgradeAbortedInsufficientDatabasePermissions |El grupo de administradores integrado no tiene permisos en la base de datos. Actualiza manualmente a la versión más reciente de Azure AD Connect para solucionar este problema. |
| UpgradeAbortedInsufficientDiskSpace |No hay espacio suficiente en disco para admitir una actualización. |
| UpgradeAbortedSecurityGroupsNotPresent |No se pudieron encontrar y resolver todos los grupos de seguridad que utiliza el motor de sincronización. |
| UpgradeAbortedServiceCanNotBeStarted |El servicio NT **Microsoft Azure AD Sync** no se pudo iniciar. |
| UpgradeAbortedServiceCanNotBeStopped |El servicio NT **Microsoft Azure AD Sync** no se pudo detener. |
| UpgradeAbortedServiceIsNotRunning |El servicio NT **Microsoft Azure AD Sync** no se está ejecutando. |
| UpgradeAbortedSyncCycleDisabled |La opción SyncCycle del [programador](active-directory-aadconnectsync-feature-scheduler.md) se ha deshabilitado. |
| UpgradeAbortedSyncExeInUse |La [interfaz de usuario de Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) está abierta en el servidor. |
| UpgradeAbortedSyncOrConfigurationInProgress |El asistente para la instalación se está ejecutando o se programó una sincronización al margen del programador. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedAdfsSignInMethod | Ha seleccionado AD FS como método de inicio de sesión. | 
| UpgradeNotSupportedCustomizedSyncRules |Ha agregado sus propias reglas personalizadas a la configuración. |
| UpgradeNotSupportedDeviceWritebackEnabled |Se ha habilitado la característica [Reescritura de dispositivos](active-directory-aadconnect-feature-device-writeback.md) . |
| UpgradeNotSupportedGroupWritebackEnabled |Se ha habilitado la característica [Reescritura de grupos](active-directory-aadconnect-feature-preview.md#group-writeback) . |
| UpgradeNotSupportedInvalidPersistedState |La instalación no es una configuración rápida ni una actualización de DirSync. |
| UpgradeNotSupportedMetaverseSizeExceeeded |Tiene más de 100.000 objetos en el metaverso. |
| UpgradeNotSupportedMultiForestSetup |Se está conectando a más de un bosque. La configuración rápida solo se conecta a un bosque. |
| UpgradeNotSupportedNonLocalDbInstall |No se está utilizando una base de datos de SQL Server Express LocalDB. |
| UpgradeNotSupportedNonMsolAccount |La [cuenta del conector AD](active-directory-aadconnect-accounts-permissions.md#active-directory-account) ya no es la cuenta de MSOL_ predeterminada. |
| UpgradeNotSupportedNotConfiguredSignInMethod | Cuando configuró AAD Connect, eligió *No configurar* cuando seleccionó el método de inicio de sesión. | 
| UpgradeNotSupportedPtaSignInMethod | Ha seleccionado Autenticación de paso a través como método de inicio de sesión. |
| UpgradeNotSupportedStagingModeEnabled |El servidor está establecido en [modo provisional](active-directory-aadconnectsync-operations.md#staging-mode). |
| UpgradeNotSupportedUserWritebackEnabled |Se ha habilitado la característica [Reescritura de usuarios](active-directory-aadconnect-feature-preview.md#user-writeback) . |

## <a name="next-steps"></a>pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
