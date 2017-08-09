---
title: "Configuración del registro automático de dispositivos unidos a un dominio de Windows con Azure Active Directory | Microsoft Docs"
description: "Configure sus dispositivos Windows unidos a un dominio para registrarse de forma automática y silenciosa en Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: dccd7df6a5f85df4179c7ea7cfc476cfb57f48c0
ms.contentlocale: es-es
ms.lasthandoff: 07/22/2017

---
# <a name="how-to-configure-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Configuración del registro automático de dispositivos unidos a un dominio de Windows con Azure Active Directory

Para usar el [acceso condicional basado en dispositivo de Azure Active Directory](active-directory-conditional-access-azure-portal.md), los dispositivos deben estar registrados en Azure Active Directory (Azure AD). Para obtener una lista de los dispositivos registrados de una organización, utilice el cmdlet [Get MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) en el [módulo de PowerShell de Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0). 

Este artículo le proporciona los pasos para configurar el registro automático de dispositivos unidos a un dominio de Windows con Azure AD en su organización.


Para más información acerca de:

- Acceso condicional, consulte [Acceso condicional en Azure Active Directory: versión preliminar](active-directory-conditional-access-azure-portal.md). 
- Dispositivos con Windows 10 en el área de trabajo y las experiencias mejoradas al registrarse en Azure AD, consulte [Windows 10 para empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md).
- Windows 10 Enterprise E3 en CSP, consulte la [Introducción a Windows 10 Enterprise E3 en CSP](https://docs.microsoft.com/en-us/windows/deployment/windows-10-enterprise-e3-overview).


## <a name="before-you-begin"></a>Antes de empezar

Antes de empezar a configurar el registro automático de los dispositivos unidos a un dominio de Windows en su entorno, debe familiarizarse con los escenarios admitidos y las restricciones.  

Para mejorar la legibilidad de las descripciones, en este tema se utiliza el término siguiente: 

- **Dispositivos de Windows actuales**: este término indica los dispositivos unidos a un dominio en los que se ejecutan Windows 10 o Windows Server 2016.
- **Dispositivos de Windows de nivel inferior**: este término indica todos los dispositivos de Windows unidos a un dominio **compatibles** en los que no se ejecutan Windows 10 ni Windows Server 2016.  


### <a name="windows-current-devices"></a>Dispositivos de Windows actuales

- Para los dispositivos en los que se ejecuta el sistema operativo de escritorio Windows, se recomienda usar la Actualización de aniversario de Windows 10 (versión 1607), o una versión posterior. 
- El registro de los dispositivos de Windows actuales **se** admite en entornos de no federadas, como las configuraciones de sincronización de hash de contraseña.  


### <a name="windows-down-level-devices"></a>Dispositivos de Windows de nivel inferior

- Se admiten los siguientes dispositivos de nivel inferior de Windows:
    - Windows 8.1
    - Windows 7
    - Windows Server 2012 R2
    - Windows Server 2012
    - Windows Server 2008 R2
- El registro de dispositivos de Windows de nivel inferior **se** admite en entornos no federados a través del inicio de sesión único de conexión directa [Inicio de sesión único de conexión directa de Azure Active Directory](https://aka.ms/hybrid/sso).
- El registro de dispositivos de Windows de nivel inferior **no se** admite en dispositivos que usan perfiles móviles. Si confía en la itinerancia de la configuración o de los perfiles, use Windows 10.



## <a name="prerequisites"></a>Requisitos previos

Antes de empezar a habilitar el registro automático de los dispositivos unidos a un dominio de su organización, es preciso asegurarse de que se ejecuta una versión actualizada de Azure AD Connect.

Azure AD Connect:

- Mantiene la asociación entre la cuenta del equipo en la instancia local de Active Directory (AD) y el objeto de dispositivo en Azure AD. 
- Habilita otras características relacionadas del dispositivo como Windows Hello para empresas.



## <a name="configuration-steps"></a>Pasos de configuración

En este tema se incluye los pasos requeridos para todos los escenarios de configuración típicos.  
Utilice la tabla siguiente para obtener una visión general de los pasos necesarios para su escenario:  



| Pasos                                      | Dispositivos actuales de Windows y sincronización de hash de contraseña | Dispositivos actuales de Windows y federación | Dispositivos de Windows de nivel inferior |
| :--                                        | :-:                                    | :-:                            | :-:                |
| Paso 1: Configuración del punto de conexión de servicio | ![Comprobar][1]                            | ![Comprobar][1]                    | ![Comprobar][1]        |
| Paso 2: Configuración de la emisión de notificaciones           |                                        | ![Comprobar][1]                    | ![Comprobar][1]        |
| Paso 3: Habilitación de dispositivos que no tengan Windows 10      |                                        |                                | ![Comprobar][1]        |
| Paso 4: Control de implementación y lanzamiento     | ![Comprobar][1]                            | ![Comprobar][1]                    | ![Comprobar][1]        |
| Paso 5: Comprobación de los dispositivos registrados          | ![Comprobar][1]                            | ![Comprobar][1]                    | ![Comprobar][1]        |



## <a name="step-1-configure-service-connection-point"></a>Paso 1: Configurar el punto de conexión de servicio

El objeto de punto de conexión de servicio (SCP) lo usan los dispositivos durante el registro para detectar la información del inquilino de Azure AD. En la instancia de Active Directory (AD) local, el objeto de SCP para el registro automático de dispositivos unidos a un dominio debe existir en la partición del contexto de nomenclatura de la configuración del bosque del equipo. Hay solo un contexto de nomenclatura de configuración por bosque. En una configuración de Active Directory con varios bosques, el punto de conexión debe existir en todos los bosques que contengan equipos unidos a un dominio.

Se puede usar el comando [**Get ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx) para recuperar el contexto de nomenclatura de configuración del bosque.  

En el caso de un bosque con el nombre de dominio de Active Directory *fabrikam.com*, el contexto de nomenclatura de configuración es:

`CN=Configuration,DC=fabrikam,DC=com`

En el bosque, el objeto de SCP para el registro automático de los dispositivos unidos a un dominio se encuentra en:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

En función de cómo se haya implementado Azure AD Connect, el objeto SCP puede que ya se haya configurado.
Con el siguiente script de Windows PowerShell se puede comprobar la existencia del objeto y recuperar los valores de detección: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

La salida de **$scp.Keywords** muestra la información del inquilino de Azure AD, por ejemplo:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Si el punto de conexión de servicio no existe, se puede crear mediante la ejecución del cmdlet `Initialize-ADSyncDomainJoinedComputerSync` en un servidor de Azure AD Connect. Se necesitan las credenciales de administrador de organización para ejecutar este cmdlet.  
El cmdlet:

- Crea el punto de conexión de servicio en el bosque de Active Directory al que está conectado Azure AD Connect. 
- Requiere que se especifique el parámetro `AdConnectorAccount`. Se trata de la cuenta configurada como cuenta de conector de Active Directory en Azure AD Connect. 


El siguiente script muestra un ejemplo de uso del cmdlet. En este script, `$aadAdminCred = Get-Credential` requiere que escriba un nombre de usuario. Es proceso que use el formato de nombre principal de usuario (UPN) (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

El cmdlet `Initialize-ADSyncDomainJoinedComputerSync`:

- Usa el módulo de PowerShell de Active Directory, que usa Active Directory Web Services que se ejecuta en un controlador de dominio. Active Directory Web Services es compatible con los controladores de dominio en los que se ejecuta Windows Server 2008 R2, y las versiones posteriores.
- Solo es compatible con la **versión 1.1.166.0 del módulo de MSOnline PowerShell**. Para descargar este módulo, use este [vínculo](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185).   

En los casos de los controladores de dominio en los que se ejecuta Windows Server 2008 R2, o alguna versión anterior, use el siguiente script para crear el punto de conexión de servicio.

En una configuración con varios bosques, debe usar el script siguiente para crear el punto de conexión de servicio en cada bosque en el que existan equipos:
 
    $verifiedDomain = "contoso.com"    # Replace this with any of your verified domain names in Azure AD
    $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47"    # Replace this with you tenant ID
    $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com"    # Replace this with your AD configuration naming context

    $de = New-Object System.DirectoryServices.DirectoryEntry
    $de.Path = "LDAP://CN=Services," + $configNC

    $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
    $deDRC.CommitChanges()

    $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
    $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
    $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

    $deSCP.CommitChanges()


## <a name="step-2-setup-issuance-of-claims"></a>Paso 2: Configuración de la emisión de notificaciones

En una configuración de Azure AD federada, los dispositivos usan Active Directory Federation Services (AD FS) o un servicio de federación local de terceros para autenticarse en Azure AD. Los dispositivos se autentican para obtener un token de acceso para registrarse en Azure Active Directory Device Registration Service (Azure DRS).

Los dispositivos de Windows actuales se autentican mediante la autenticación integrada de Windows en un punto de conexión de WS-Trust activo (versiones 1.3 o 2005) hospedado por el servicio de federación local.

> [!NOTE]
> Si se usa AD FS, es preciso habilitar **adfs/services/trust/13/windowstransport** o **adfs/services/trust/2005/windowstransport**. Si usa el proxy de autenticación web, asegúrese también de que este punto de conexión se publique a través del proxy. Para ver qué puntos de conexión están habilitados, vaya a **Servicio > Puntos de conexión** en la consola de administración de AD FS.
>
>Si AD FS no es el servicio de federación local, siga las instrucciones de su proveedor para asegurarse de que admite puntos de conexión de WS-Trust 1.3 o 2005 y que estos se publican a través del archivo de intercambio de metadatos (MEX).

Para que se complete el registro del dispositivo, las siguientes notificaciones deben existir en el token que recibe Azure DRS. Azure DRS creará un objeto de dispositivo en Azure AD con una parte de esta información, que después usa Azure AD Connect para asociar el objeto de dispositivo recién creado con la cuenta local del equipo.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Si hay más de un nombre de dominio comprobado, es preciso proporcionar la siguiente notificación para los equipos:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Si ya se emite una notificación de ImmutableID (p. ej., identificador de inicio de sesión alternativo), será preciso proporcionar una notificación correspondiente para los equipos:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

En las siguientes secciones encontrará información acerca de:
 
- Los valores que deben tener todas las notificaciones
- El aspecto de una definición en AD FS

La definición le ayuda a comprobar si los valores están presentes o si debe crearlos.

> [!NOTE]
> Si no usa AD FS como servidor de federación local, siga las instrucciones de su proveedor para crear la configuración apropiada para emitir estas notificaciones.

### <a name="issue-account-type-claim"></a>Emisión de notificación de tipo de cuenta

**`http://schemas.microsoft.com/ws/2012/01/accounttype`**: esta notificación debe contener un valor de **DJ**, que identifica el dispositivo como equipo unido a un dominio. En AD FS, puede agregar una regla de transformación de emisión como esta:

    @RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>Emisión de objectGUID de la cuenta local del equipo

**`http://schemas.microsoft.com/identity/claims/onpremobjectguid`**: esta notificación debe contener el valor **objectGUID** de la cuenta local del equipo. En AD FS, puede agregar una regla de transformación de emisión como esta:

    @RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );
 
### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>Emisión de objectSID de la cuenta local del equipo

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`**: esta notificación debe contener el valor **objectSid** de la cuenta local del equipo. En AD FS, puede agregar una regla de transformación de emisión como esta:

    @RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);

### <a name="issue-issuerid-for-computer-when-multiple-verified-domain-names-in-azure-ad"></a>Emisión de issuerID para un equipo cuando haty varios nombres de dominio comprobados en Azure AD

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`**: esta notificación debe contener el identificador uniforme de recursos (URI) de cualquiera de los nombres de dominio comprobados que se conectan al servicio de federación local (AD FS o uno de terceros) que emite el token. En AD FS, puede agregar reglas de transformación de emisión que se parecen a las que verá a continuación en ese orden específico después de las anteriores. Tenga en cuenta que se necesita una regla que emita explícitamente la regla para los usuarios. En las reglas siguientes, se agrega una primera regla que identifique al usuario frente a la autenticación del equipo.

    @RuleName = "Issue account type with the value User when its not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://<verified-domain-name>/adfs/services/trust/"
    );


En la notificación anterior,

- `$<domain>` corresponde a la dirección URL del servicio AD FS.
- `<verified-domain-name>` corresponde a un marcador de posición que se debe reemplazar con uno de los nombres de dominio comprobados en Azure AD.



Consulte [Incorporación de su nombre de dominio personalizado a Azure Active Directory](active-directory-add-domain.md) para más información sobre nombres de dominios comprobados.  
Para obtener una lista de los dominios comprobados de la compañía, puede usar el cmdlet [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0). 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)

### <a name="issue-immutableid-for-computer-when-one-for-users-exist-eg-alternate-login-id-is-set"></a>Emisión de ImmutableID para un equipo cuando existe uno para los usuarios (por ejemplo, se ha establecido el identificador de inicio de sesión alternativo)

**`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`**: esta notificación debe contener un valor válido para los equipos. En AD FS, se puede crear una regla de transformación de emisión como se indica a continuación:

    @RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Script de aplicación auxiliar para crear reglas de transformación de emisión de AD FS

El siguiente script le ayuda con la creación de las reglas de transformación de emisión que se han descrito anteriormente.

    $multipleVerifiedDomainNames = $false
    $immutableIDAlreadyIssuedforUsers = $false
    $oneOfVerifiedDomainNames = 'example.com'   # Replace example.com with one of your verified domains
    
    $rule1 = '@RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );'

    $rule2 = '@RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'

    $rule3 = '@RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);'

    $rule4 = ''
    if ($multipleVerifiedDomainNames -eq $true) {
    $rule4 = '@RuleName = "Issue account type with the value User when it is not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://' + $oneOfVerifiedDomainNames + '/adfs/services/trust/"
    );'
    }

    $rule5 = ''
    if ($immutableIDAlreadyIssuedforUsers -eq $true) {
    $rule5 = '@RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'
    }

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

### <a name="remarks"></a>Comentarios 

- Este script anexa las reglas a las reglas existentes. No ejecute el script dos veces porque el conjunto de reglas se agregaría dos veces. Asegúrese de que no existe ninguna regla correspondiente para estas notificaciones (en las condiciones correspondientes) antes de volver a ejecutar el script.

- Si tiene varios nombres de dominio comprobados (como se muestra en el portal de Azure AD o mediante el cmdlet Get-MsolDomains), establezca el valor de **$multipleVerifiedDomainNames** en el script en **$true**. Además, asegúrese de que quita cualquier notificación issuerid existente que pueda haber creado Azure AD Connect o a través de otros medios. Este es un ejemplo de esta regla:


        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Si ya ha emitido una notificación **ImmutableID** para las cuentas de usuario, establezca el valor de **$immutableIDAlreadyIssuedforUsers** en el script en **$true**.

## <a name="step-3-enable-windows-down-level-devices"></a>Paso 3: Habilitación de dispositivos de Windows de nivel inferior

Si algunos de los dispositivos unidos a un dominio son dispositivos de Windows de nivel inferior, necesitará:

- Establecer una directiva en Azure AD que permita a los usuarios registrar dispositivos.
 
- Configurar el servicio de federación local para emitir notificaciones para admitir la **autenticación integrada de Windows (IWA)** para el registro de dispositivos.
 
- Agregar el punto de conexión de autenticación de dispositivos de Azure AD a las zonas de la intranet locales para evitar las solicitudes de certificado al autenticar el dispositivo.

### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Establecer una directiva de Azure AD que permita a los usuarios registrar dispositivos

Para registrar dispositivos de Windows de nivel inferior, es preciso asegurarse de que está establecida la configuración que permite a los usuarios registrar dispositivos en Azure AD. En Azure Portal, esta configuración se encuentra en:

`Azure Active Directory > Users and groups > Device settings`
    
En la siguiente directiva se debe elegir **Todos**: **Los usuarios pueden registrar sus dispositivos con Azure AD**

![Registro de dispositivos](./media/active-directory-conditional-access-automatic-device-registration-setup/23.png)


### <a name="configure-on-premises-federation-service"></a>Configuración de un servicio de federación local 

Un servicio de federación local debe admitir la emisión de las notificaciones **authenticationmehod** y **wiaormultiauthn** al recibir una solicitud de autenticación para el usuario de confianza de Azure AD que contiene un parámetro resouce_params con un valor codificado, como se muestra a continuación:

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

Cuando llega una solicitud de este tipo, el servicio de federación local debe autenticar al usuario mediante la autenticación integrada de Windows y una vez que la autenticación se haya realiza correctamente, debe emitir las dos notificaciones siguientes:

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

En AD FS, debe agregar una regla de transformación de emisión que atraviesa el método de autenticación.  

**Para agregar esta regla:**

1. En la consola de administración de AD FS, vaya a `AD FS > Trust Relationships > Relying Party Trusts`.
2. Haga clic con el botón derecho en el objeto de confianza para usuario de confianza de la Plataforma de identidad de Microsoft Office 365 y seleccione **Editar reglas de notificación**.
3. En la pestaña **Reglas de transformación de emisión**, seleccione **Agregar regla**.
4. Seleccione **Enviar notificaciones con una regla personalizada** en la lista de plantillas **Regla de notificación**.
5. Seleccione **Siguiente**.
6. En el cuadro **Nombre de regla de notificación**, escriba **Regla de notificaciones del método de autenticación**.
7. En el cuadro **Regla de notificación** escriba la siguiente regla:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. En el servidor de federación, escriba el comando de PowerShell siguiente después de reemplazar **\<RPObjectName\>** por el nombre de objeto de usuario de confianza del objeto de confianza del usuario de confianza de Azure AD. Normalmente, este objeto se llama **Plataforma de identidad de Microsoft Office 365**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-end-point-to-the-local-intranet-zones"></a>Adición del punto de conexión de autenticación de dispositivos de Azure AD a las zonas de intranet locales

Para evitar las peticiones de certificados cuando los usuarios de dispositivos registrados se autentican en Azure AD se puede insertar una directiva en los dispositivos unidos a un dominio para agregar la siguiente dirección URL a la zona de intranet local en Internet Explorer:

`https://device.login.microsoftonline.com`

## <a name="step-4-control-deployment-and-rollout"></a>Paso 4: Control de implementación y lanzamiento

Cuando se hayan completado los pasos requeridos, los dispositivos unidos a un dominio están listos para registrarse automáticamente en Azure AD. Todos los dispositivos unidos a un dominio en que se ejecuten la Actualización de aniversario de Windows 10 y Windows Server 2016 se registran automáticamente en Azure AD cuando el dispositivo se reinicie o el usuario inicie sesión. Los dispositivos nuevos se registran en Azure AD al reiniciarse después de que finaliza la operación de unión al dominio.

Los dispositivos que anteriormente estaban unidos a un área de trabajo en Azure AD (por ejemplo, para Intune) pasan a estar "*unidos a dominio, registrados en AAD*". Sin embargo, este proceso tarda algún tiempo en completarse en todos los dispositivos debido al flujo normal de actividades de dominio y de usuario.

### <a name="remarks"></a>Comentarios

- Para controlar el lanzamiento del registro automático de los equipos con Windows 10 y Windows Server 2016 unidos a un dominio, se puede usar un objeto de directiva de grupo.

- La actualización de noviembre de 2015 de Windows 10 se registra automáticamente en Azure AD **solo** si se establece el objeto de directiva de grupo del lanzamiento.

- Para el lanzamiento del registro automático de los equipos de Windows de nivel inferior, se puede implementar un [paquete de Windows Installer](#windows-installer-packages-for-non-windows-10-computers) en los equipos que se seleccionen.

- Si se inserta el objeto de directiva de grupo en dispositivos unidos a un dominio de Windows 8.1, se volverá a intentar el registro; sin embargo, se recomienda usar el [paquete de Windows Installer](#windows-installer-packages-for-non-windows-10-computers) para registrar todos los dispositivos de Windows de nivel inferior. 

### <a name="create-a-group-policy-object"></a>Creación de un objeto de directiva de grupo 

Para controlar el lanzamiento del registro automático de los equipos actuales de Windows, es preciso que implemente el objeto de la directiva de grupo **Registrar los equipos asociados a un dominio como dispositivos** en los equipos que desee registrar. Por ejemplo, puede implementar la directiva en un grupo de seguridad o en una unidad organizativa.

**Para establecer la directiva:**

1. Abra el **Administrador del servidor**y vaya a `Tools > Group Policy Management`.
2. Vaya al nodo del dominio correspondiente al dominio en el que desee activar el registro automático de los equipos actuales de Windows.
3. Haga clic con el botón derecho en **Objetos de directiva de grupo** y seleccione **Nuevo**.
4. Escriba un nombre para el objeto de directiva de grupo. Por ejemplo, *Registro automático de Azure AD*. Seleccione **Aceptar**.
5. Haga clic con el botón derecho en el nuevo objeto de directiva de grupo y seleccione **Editar**.
6. Vaya a **Configuración del equipo** > **Directivas** > **Plantillas administrativas** > **Componentes de Windows** > **Registro de dispositivos**. Haga clic con el botón derecho en **Registrar los equipos asociados a un dominio como dispositivos** y seleccione **Editar**.
   
   > [!NOTE]
   > Esta plantilla de directiva de grupo ha cambiado de nombre desde versiones anteriores de la consola de Administración de directivas de grupo. Si utiliza una versión anterior de la consola, vaya a `Computer Configuration > Policies > Administrative Templates > Windows Components > Workplace Join > Automatically workplace join client computers`. 

7. Seleccione **Habilitado** y luego **Aplicar**.
8. Seleccione **Aceptar**.
9. Vincule el objeto de la directiva de grupo a una ubicación de su elección. Por ejemplo, puede vincularlo a una unidad organizativa específica. También puede vincularlo a un grupo de seguridad específico de equipos que se registren automáticamente en Azure AD. Para establecer esta directiva para todos los equipos con Windows 10 y Windows Server 2016 unidos a un dominio en su organización, vincule el objeto de directiva de grupo al dominio.

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>Paquetes de Windows Installer para equipos sin Windows 10

Para registrar equipos de Windows de nivel inferior unidos a un dominio en un entorno federado, puede descargar e instalar este paquetes de Windows Installer (.msi) desde la opción Download Center (Centro de descarga) de la página [Microsoft Workplace Join for non-Windows 10 computers](https://www.microsoft.com/en-us/download/details.aspx?id=53554) (Microsoft Workplace Join para equipos sin Windows 10).

El paquete se puede implementar mediante un sistema de distribución de software como System Center Configuration Manager. El paquete admite las opciones de instalación silenciosa estándar mediante el parámetro *quiet*. La rama actual de System Center Configuration Manager ofrece ventajas adicionales con respecto a las versiones anteriores, como la capacidad de realizar el seguimiento de los registros completados. Para más información, consulte [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager).

El instalador crea una tarea programada en el sistema que se ejecuta en el contexto del usuario. La tarea se desencadena cuando el usuario inicia sesión en Windows. La tarea registra de forma silenciosa el dispositivo en Azure AD con las credenciales de usuario después de realizar la autenticación mediante la autenticación integrada de Windows. Para ver la tarea programada, en el dispositivo, vaya a **Microsoft** > **Workplace Join** y, después, vaya a la biblioteca del Programador de tareas.

## <a name="step-5-verify-registered-devices"></a>Paso 5: Comprobación de los dispositivos registrados

Para comprobar los dispositivos registrados correctamente de una organización, utilice el cmdlet [Get MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) en el [módulo de PowerShell de Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

La salida de este cmdlet muestra los dispositivos registrados en Azure AD. Para obtener todos los dispositivos, use el parámetro **-All** y, después, fíltrelos mediante la propiedad **deviceTrustType**. Los dispositivos unidos a un dominio tienen el valor **Unido a dominio**.

## <a name="next-steps"></a>Pasos siguientes

* [Azure Active Directory automatic device registration FAQ](active-directory-device-registration-faq.md) (Preguntas más frecuentes acerca del registro automático de dispositivos)
* [Troubleshooting auto-registration of domain joined computers to Azure AD – Windows 10 and Windows Server 2016](active-directory-device-registration-troubleshoot-windows.md) (Solución de problemas de registro automático de equipos unidos a un dominio en Azure AD: Windows 10 y Windows Server 2016)
* [Troubleshooting auto-registration of domain joined computers to Azure AD – non-Windows 10](active-directory-device-registration-troubleshoot-windows-legacy.md) (Solución de problemas de registro automático de equipos unidos a un dominio en Azure AD: sin Windows 10)
* [Acceso condicional de Azure Active Directory](active-directory-conditional-access-azure-portal.md)



<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png

