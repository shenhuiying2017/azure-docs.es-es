<properties
	pageTitle="Implementación de la sincronización de contraseña mediante la sincronización de Azure AD Connect | Microsoft Azure"
	description="Proporciona información sobre cómo funciona la sincronización de contraseña y cómo habilitarla."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>
<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/15/2016"
	ms.author="markusvi;andkjell"/>


# Implementación de la sincronización de contraseña mediante la sincronización de Azure AD Connect

Con la sincronización de contraseñas, puede usar la contraseña de la instancia de Active Directory local para iniciar sesión también en Azure Active Directory.

Este tema le proporciona la información que necesita para habilitar la sincronización de contraseñas y resolver problemas relativos a ella en su entorno.


## Qué es la sincronización de contraseñas

La sincronización de contraseñas es una característica para sincronizar contraseñas de usuario de una instancia de Active Directory local con otra de Azure Active Directory (Azure AD) basado en la nube. Esta característica le permite iniciar sesión en los servicios de Azure Active Directory (como Office 365, Microsoft Intune y CRM Online) con la misma contraseña que usa para iniciar sesión en la red local. Para utilizar esta característica, debe instalar los servicios de sincronización de Azure Active Directory Connect (sincronización de Azure AD Connect).


> [AZURE.NOTE] Para obtener más información acerca de los Servicios de dominio de Active Directory configurados para la sincronización de contraseñas y FIPS, consulte [Sincronización de contraseñas y FIPS](#password-synchronization-and-fips).

## Funcionamiento de la sincronización de contraseñas

La sincronización de contraseñas es una extensión de la característica de sincronización de directorios implementada por la sincronización de Azure AD Connect. Esta característica requiere la sincronización de directorios entre su instancia de AD local y su instancia de Azure Active Directory para realizar la configuración.

El Servicio de dominio de Active Directory almacena contraseñas en forma de representación de valor hash de la contraseña de usuario real. No puede usar un hash de contraseña para:

- Iniciar sesión en la red local

- Revertir a la versión de texto sin formato de una contraseña

Para sincronizar una contraseña, la sincronización de Azure AD Connect extrae el hash de contraseña de usuario de la instancia de Active Directory local. Se aplica un procesamiento de seguridad adicional al hash de contraseña antes de que se sincronice con el servicio de autenticación de Azure Active Directory. El flujo de datos reales del proceso de sincronización de contraseñas es similar al de sincronización de datos de usuario, como el nombre para mostrar o las direcciones de correo electrónico.

Las contraseñas se sincronizan:

- Usuario por usuario

- Por orden cronológico

- Con más frecuencia que la ventana de sincronización de directorios estándar para otros atributos

Una contraseña sincronizada sobrescribe la contraseña existente en la nube.

La primera vez que se habilita la característica de sincronización de contraseñas, se realiza una sincronización inicial de las contraseñas de todos los usuarios dentro del ámbito. No puede definir explícitamente un subconjunto de contraseñas de usuario que desee sincronizar.

Al cambiar una contraseña local, la contraseña actualizada se sincroniza más a menudo en cuestión de minutos. La característica de sincronización de contraseñas reintenta automáticamente las sincronizaciones de contraseñas de usuario erróneas. Si se produce un error al intentar sincronizar una contraseña, se registra un error en el visor de eventos.

La sincronización de una contraseña no influye en el usuario con la sesión iniciada actualmente. Si se sincroniza un cambio de contraseña mientras está conectado a un servicio en la nube, este cambio no afectará de inmediato a la sesión del servicio en la nube. Sin embargo, tan pronto como el servicio en la nube requiera de nuevo su autenticación, deberá proporcionar la nueva contraseña.

> [AZURE.NOTE] Solo se admite la sincronización de la contraseña para el usuario del tipo de objeto de Active Directory. No se admite para el tipo de objeto iNetOrgPerson.

### Funcionamiento de la sincronización de contraseñas con Servicios de dominio de Azure AD

Si habilita este servicio en Azure AD, se requiere la opción de sincronización de contraseñas para obtener una experiencia de inicio de sesión único. Con este servicio habilitado, cambia el comportamiento de sincronización de contraseñas y los valores de hash de contraseña también se sincronizan tal cual desde la instancia de Active Directory local a Servicios de dominio de Azure Active Directory. La funcionalidad es similar a Herramienta de migración Active Directory (ADMT) y permite a Servicios de dominio de Azure Active Directory poder autenticar al usuario con todos los métodos disponibles en la instancia de AD local.

### Consideraciones sobre la seguridad

Al sincronizar contraseñas, la versión de texto sin formato de su contraseña no se expone a la característica de sincronización de contraseñas ni a Azure AD ni a ninguno de los servicios asociados.

Además, no hay ningún requisito en Active Directory local para almacenar la contraseña en un formato cifrado reversible. Se usa un resumen del hash de contraseña de Active Directory para la transmisión entre Azure Active Directory y AD local. El resumen del hash de contraseña no se puede usar para obtener acceso a recursos en su entorno local.

### Consideraciones de la directiva de contraseña

Existen dos tipos de directivas de contraseña que se ven afectados por la habilitación de la sincronización de contraseñas:

1. Directiva de complejidad de contraseñas
2. Directiva de expiración de contraseñas

**Directiva de complejidad de contraseñas**

Cuando se habilita la sincronización de contraseñas, las directivas de complejidad de contraseñas configuradas en Active Directory local reemplazan a las directivas de complejidad que pueden definirse en la nube para los usuarios sincronizados. Esto significa que cualquier contraseña válida en el entorno del Active Directory local del usuario puede usarse para obtener acceso a servicios de Azure AD.

> [AZURE.NOTE] Las contraseñas para los usuarios que se crean directamente en la nube siguen estando sujetas a las directivas de contraseñas tal como se definen en la nube.

**Directiva de expiración de contraseñas**

Si un usuario está en el ámbito de sincronización de contraseñas, la contraseña de cuenta en la nube se establece en "*No expira nunca*". Esto significa que puede seguir iniciando sesión en servicios en la nube con una contraseña sincronizada que ha expirado en su entorno local.

La contraseña en la nube se actualizará la próxima vez que cambie la contraseña en el entorno local.

### Sobrescritura de las contraseñas sincronizadas

Un administrador puede restablecer manualmente la contraseña mediante PowerShell.

En este caso, la nueva contraseña sobrescribe la contraseña sincronizada y todas las directivas de contraseñas definidas en la nube se aplican a la nueva contraseña.

Si cambia la contraseña local de nuevo, la nueva contraseña se sincroniza con la nube y reemplaza a la contraseña actualizada manualmente.


## Habilitación de la sincronización de contraseñas

Para habilitar la sincronización de contraseña, tiene dos opciones:

- Si utiliza configuración rápida para instalar Azure AD Connect, la sincronización de contraseñas está habilitada de forma predeterminada.

- Si utiliza una configuración personalizada al instalar Azure AD Connect, podrá habilitar la sincronización de contraseña en la página de inicio de sesión del usuario.

<br> ![Habilitación de la sincronización de contraseñas](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png) <br>

Si elige usar **Federación con AD FS**, podrá habilitar opcionalmente la sincronización de contraseñas como una copia de seguridad en caso de error en la infraestructura de AD FS. También puede habilitarla si piensa usa Servicios de dominio de Azure Active Directory.

### Sincronización de contraseñas y FIPS

Si el servidor se bloquea de acuerdo con FIPS (Estándar federal de procesamiento de información), el MD5 se deshabilita. Para habilitar esta opción para la sincronización de contraseñas, agregue la clave enforceFIPSPolicy en miiserver.exe.config en C:\\Archivos de programa\\Azure AD Sync\\Bin.

```
<configuration>
    <runtime>
        <enforceFIPSPolicy enabled="false"/>
    </runtime>
</configuration>
```

El nodo configuration/runtime puede encontrarse al final del archivo config.

Para obtener información sobre la seguridad y FIPS, consulte [AAD Password Sync, Encryption and FIPS compliance](http://blogs.technet.com/b/ad/archive/2014/06/28/aad-password-sync-encryption-and-and-fips-compliance.aspx) (Sincronización de contraseñas de AAD, cifrado y cumplimiento FIPS)


## Solución de problemas de sincronización de contraseña

**Para solucionar problemas de sincronización de contraseña, realice los pasos siguientes:**

1. Abra **Synchronization Service Manager**.

2. Haga clic en **Conectores**.

3. Seleccione el Conector Active Directory en el que está ubicado el usuario

4. Seleccione **Buscar espacio de conector**.

5. Localice el usuario que está buscando.

6. Seleccione la pestaña **Linaje** y asegúrese de que al menos una de las reglas de sincronización muestra en la columna de **sincronización de contraseñas** el valor **True**. En la configuración predeterminada, el nombre de la regla de sincronización es **In from AD - User AccountEnabled**.

    ![Información de linaje de un usuario](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)

7. También debería [seguir al usuario](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system) a través del metaverso hasta el espacio de conector de Azure AD. El objeto de espacio de conector debe tener una regla de salida con la columna de **sincronización de contraseñas** con un valor establecido como **True**. En la configuración predeterminada, el nombre de la regla de sincronización es **Out to AAD - User Join**.

    ![Propiedades del espacio de conector de un usuario](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)

8. Para ver los detalles de sincronización de contraseñas del objeto, haga clic en el botón **Registro...**<br> Esto hará que se cree una página con una vista con el historial de estados de sincronización de contraseña del usuario durante la semana pasada.

    ![Detalles del registro de objetos](./media/active-directory-aadconnectsync-implement-password-synchronization/csobjectlog.png)

La columna de estado puede presentar los siguientes valores:

| Status | Descripción |
| ---- | ----- |
| Correcto | La contraseña se sincronizó correctamente. |
| FilteredByTarget | La contraseña se establece en **El usuario debe cambiar la contraseña en el siguiente inicio de sesión**. La contraseña no se ha sincronizado. |
| NoTargetConnection | No hay ningún objeto en el metaverso o en el espacio del conector de Azure AD. |
| SourceConnectorNotPresent | No se encontró ningún objeto en el espacio del conector de Active Directory local. |
| TargetNotExportedToDirectory | Aún no se exportó el objeto del espacio del conector de Azure AD. |
| MigratedCheckDetailsForMoreInfo | La entrada de registro se creó antes de la versión 1.0.9125.0 y se muestra en su estado heredado. |


## Desencadenamiento de una sincronización completa de todas las contraseñas

Normalmente, no hay ninguna necesidad de forzar una sincronización completa de todas las contraseñas.<br> Sin embargo, si es necesario, puede desencadenar una sincronización completa de todas las contraseñas mediante el siguiente script:

    $adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
    $aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
    Import-Module adsync
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter “Microsoft.Synchronize.ForceFullPasswordSync”, String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true




## Pasos siguientes

* [Sincronización de Azure AD Connect: personalización de las opciones de sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0420_2016-->