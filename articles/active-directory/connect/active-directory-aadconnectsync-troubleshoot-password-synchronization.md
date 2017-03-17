---
title: "Solución de problemas de la implementación de la sincronización de contraseña con la sincronización de Azure AD Connect | Microsoft Docs"
description: "En este artículo se proporciona información sobre cómo solucionar problemas de sincronización de contraseñas."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: e7ad2cb4c464c7095f704cc137f1c42422fbb40e
ms.openlocfilehash: 4c42821b95d666721b84d4976966b4886e517193
ms.lasthandoff: 03/01/2017


---
# <a name="troubleshoot-password-synchronization-with-azure-ad-connect-sync"></a>Solución de problemas de la implementación de la sincronización de contraseña con la sincronización de Azure AD Connect
En este tema se proporcionan los pasos para solucionar problemas relacionados con la sincronización de contraseñas. Si las contraseñas no se sincronizan como se esperaba, puede ser para un subconjunto de usuarios o para todos los usuarios.

* Si tiene un problema con contraseñas que no se sincronizan, consulte la sección de [Solución de problemas cuando no se sincronizan las contraseñas](#no-passwords-are-synchronized).
* Si tiene un problema con objetos individuales, consulte la sección [Solución de problemas de un objeto que no sincroniza contraseñas](#one-object-is-not-synchronizing-passwords).

## <a name="no-passwords-are-synchronized"></a>Las contraseñas no se sincronizan
Siga estos pasos para averiguar por qué no se sincroniza ninguna contraseña:

1. ¿Está el servidor de Connect en [modo de almacenamiento provisional](active-directory-aadconnectsync-operations.md#staging-mode)? Un servidor en modo de almacenamiento provisional no sincroniza contraseñas.
2. Ejecute el script en la sección [Obtención del estado de configuración de sincronización de contraseñas](#get-the-status-of-password-sync-settings). Le ofrece una visión general de la configuración de sincronización de contraseñas.  
![Salida del script de PowerShell de la configuración de sincronización de contraseñas](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/psverifyconfig.png)  
3. Si la característica no está habilitada en Azure AD o si el estado del canal de sincronización no está habilitado, ejecute el asistente para la instalación de Connect. Seleccione **Personalizar las opciones de sincronización** y anule la selección de sincronización de contraseñas. Este cambio deshabilita temporalmente la característica. Después, vuelva a ejecutar al asistente y habilite de nuevo la sincronización de contraseñas. Vuelva a ejecutar el script para comprobar que la configuración es correcta.
4. Busque errores en el registro de eventos. Busque los siguientes eventos, que podrían indicar un problema:
    1. Origen: "Sincronización de directorios" ID: 0, 611, 652, 655 Si ve esto, significa que hay un problema de conectividad. El mensaje de registro de eventos contiene información de bosques donde haya un problema. Para obtener más información, consulte [Problema de conectividad](#connectivity problem).
5. Si no ve ningún latido o si nada funciona, ejecute [Desencadenamiento de una sincronización completa de todas las contraseñas](#trigger-a-full-sync-of-all-passwords). Solo debe ejecutar el script una vez.
6. Lea la sección [Solución de problemas de un objeto que no sincroniza contraseñas](#one-object-is-not-synchronizing-passwords).

### <a name="connectivity-problem"></a>Problema de conectividad

1. ¿Hay conectividad con Azure AD?
2. ¿La cuenta tiene los permisos necesarios para leer los valores de hash de contraseña en todos los dominios? Si instaló Connect usando la configuración rápida, los permisos deberían ser los correctos. Si ha usado la instalación personalizada, debe establecer los permisos manualmente.
    1. Para buscar la cuenta que usa el conector de Active Directory, inicie **Synchronization Service Manager**. Vaya a **Conectores** y busque el bosque local de Active Directory donde va a solucionar problemas. Seleccione el conector y haga clic en **Propiedades**. Vaya a **Conexión al bosque de Active Directory**.  
    ![Cuenta que usa AD Connector](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/connectoraccount.png)  
    Tome nota del nombre de usuario y el dominio donde está la cuenta.
    2. Comience en **Usuarios y equipos de Active Directory**. Compruebe que la cuenta que encontró en los pasos anteriores tienen los permisos de seguimiento establecidos en la raíz de todos los dominios del bosque:
        * Replicación de cambios de directorio
        * Replicación de todos los cambios de directorio
3. ¿Puede Azure AD Connect acceder a los controladores de dominio? Si el servidor de Connect no puede conectarse a todos los controladores de dominio, debe configurar **Only use preferred domain controller** (Usar solo el controlador de dominio preferido).  
    ![Controlador de dominio que usa AD Connector](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/preferreddc.png)  
    Vuelva a **Synchronization Service Manager** y **Configure Directory Partition** (Configurar partición de directorio). Seleccione el dominio en **Seleccionar particiones de directorio**, active la casilla **Only use preferred domain controller** (Usar solo el controlador de dominio preferido) y haga clic en **Configurar**. En la lista, escriba los controladores de dominio que Connect debe usar para realizar la sincronización de contraseñas. También se usa la misma lista para los procesos de importación y exportación. Siga estos pasos con todos los dominios.
4. Si el script de muestra que no hay ningún latido, ejecute el script de [Desencadenamiento de una sincronización completa de todas las contraseñas](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords"></a>Un objeto no sincroniza contraseñas
Puede solucionar fácilmente los problemas relacionados con la sincronización de contraseñas si revisa el estado actual de un objeto.

1. Comience en **Usuarios y equipos de Active Directory**. Busque el usuario y compruebe que **El usuario debe cambiar la contraseña en el siguiente inicio de sesión** está seleccionado.  
![Contraseñas productivas de Active Directory](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/adprodpassword.png)  
Si está seleccionado, pida al usuario que inicie sesión y cambie la contraseña. Las contraseñas temporales no se sincronizan en Azure AD.
2. Si parece correcto en Active Directory, el paso siguiente es seguir al usuario en el motor de sincronización. Al seguir al usuario desde Active Directory local a Azure AD, puede ver si hay un error descriptivo en el objeto.
    1. Inicie **[Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)**.
    2. Haga clic en **Conectores**.
    3. Seleccione el **Conector Active Directory** en el que está ubicado el usuario.
    4. Seleccione **Search Connector Space**(Buscar espacio de conector).
    5. En **Ámbito**, seleccione **DN o delimitador**. Escriba el DN completo del usuario donde va a solucionar problemas.
    ![Búsqueda del usuario en el servidor de configuración con DN](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/searchcs.png)  
    6. Busque el usuario y haga clic en **Propiedades** para ver todos los atributos. Si el usuario no aparece en el resultado de búsqueda, compruebe las [reglas de filtrado](active-directory-aadconnectsync-configure-filtering.md) y asegúrese de ejecutar [Aplicación y comprobación de los cambios](active-directory-aadconnectsync-configure-filtering.md#apply-and-verify-changes) para que el usuario se muestre en Connect.
    7. Para ver los detalles de la sincronización de contraseñas del objeto de la semana pasada, haga clic en **Log...**(Registro).  
    ![Detalles del registro de objetos](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/csobjectlog.png)  
    Si el registro de objetos está vacío, Azure AD Connect no ha sido capaz de leer el valor de hash de contraseña de Active Directory. Continúe solucionando problemas con [Errores de conectividad](#connectivity-errors). Si ve un valor distinto de **correcto**, consulte la tabla de [registro de sincronización de contraseñas](#password-sync-log).
    8. Seleccione la pestaña **Linaje** y asegúrese de que al menos una de las reglas de sincronización muestra **Sincronización de contraseñas** con el valor **True**. En la configuración predeterminada, el nombre de la regla de sincronización es **In from AD - User AccountEnabled**.  
    ![Información de linaje de un usuario](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync.png)  
    9. Haga clic en **Propiedades del objeto de metaverso**. Verá una lista de atributos en el usuario.  
    ![Información de metaverso](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvpasswordsync.png)  
    Compruebe que no hay ningún atributo **cloudFiltered**. Asegúrese de que los atributos de dominio (domainFQDN y domainNetBios) tienen los valores esperados.
    10. Haga clic en la pestaña **Conectores**. Asegúrese de que ve los conectores en AD de Azure y en AD local.
    ![Información de metaverso](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvconnectors.png)  
    11. Seleccione la fila que representa Azure AD y haga clic en **Propiedades**. Haga clic en la pestaña **Linaje**. El objeto de espacio del conector debe tener una regla de salida con **Sincronización de contraseñas** establecida en **True**. En la configuración predeterminada, el nombre de la regla de sincronización es **Out to AAD - User Join**.  
    ![Propiedades del espacio de conector de un usuario](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Registro de sincronización de contraseñas
La columna de estado puede presentar los siguientes valores:

| Estado | Description |
| --- | --- |
| Correcto |La contraseña se sincronizó correctamente. |
| FilteredByTarget |La contraseña se establece en **El usuario debe cambiar la contraseña en el siguiente inicio de sesión**. La contraseña no se ha sincronizado. |
| NoTargetConnection |No hay ningún objeto en el metaverso o en el espacio del conector de Azure AD. |
| SourceConnectorNotPresent |No se encontró ningún objeto en el espacio del conector de Active Directory local. |
| TargetNotExportedToDirectory |Aún no se exportó el objeto del espacio del conector de Azure AD. |
| MigratedCheckDetailsForMoreInfo |La entrada de registro se creó antes de la versión 1.0.9125.0 y se muestra en su estado heredado. |

## <a name="scripts-to-help-troubleshooting"></a>Scripts para ayudar a solucionar problemas

### <a name="get-the-status-of-password-sync-settings"></a>Obtención del estado de configuración de sincronización de contraseñas
```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Desencadenamiento de una sincronización completa de todas las contraseñas
> [!NOTE]
> Solo debe ejecutar el script una vez. Si necesita ejecutarlo varias veces, el problema se debe a otro motivo. Póngase en contacto con el equipo de asistencia técnica de Microsoft para ayudarlo a solucionar el problema.

Puede desencadenar una sincronización completa de todas las contraseñas mediante el siguiente script:

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Pasos siguientes
* [Implementación de la sincronización de contraseña mediante la sincronización de Azure AD Connect](active-directory-aadconnectsync-implement-password-synchronization.md)
* [Sincronización de Azure AD Connect: personalización de las opciones de sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

