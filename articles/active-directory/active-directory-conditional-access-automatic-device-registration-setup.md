---
title: Configuración del registro automático de dispositivos unidos a un dominio de Windows con Azure Active Directory | Microsoft Docs
description: El administrador de TI puede elegir tener sus dispositivos Windows unidos a un dominio para registrarse de forma automática y silenciosa en Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2016
ms.author: markvi

---
# <a name="how-to-setup-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Configuración del registro automático de dispositivos unidos a un dominio de Windows con Azure Active Directory
Se requiere el registro de los equipos unidos a un dominio de Windows con Azure AD para habilitar el [acceso condicional basado en dispositivos de Azure Active Directory](active-directory-conditional-access.md).

Realice la actualización para permitir el uso de la cuenta profesional o educativa para obtener una mejor experiencia de SSO en las aplicaciones de Azure AD, itinerancia de la configuración en todos los dispositivos, para usar la Tienda Windows para empresas y para contar con una autenticación más segura y un inicio de sesión más cómodo con Windows Hello. 

> [!NOTE]
> La actualización de noviembre de 2015 de Windows 10 es compatible con algunas de las experiencias mejoradas de usuario; sin embargo, es la Actualización de aniversario la que es totalmente compatible con el acceso condicional basado en dispositivos.  
> Para más información sobre el acceso condicional, consulte [Acceso condicional de Azure Active Directory](active-directory-conditional-access.md).  
> Para más información sobre los dispositivos Windows 10 en el área de trabajo y las experiencias de los usuarios al registrarse con Azure AD, consulte [Windows 10 para empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md). 
> 
> 

El registro se admite en las versiones anteriores de Windows, entre ellas: 

* Windows 8.1 
* Windows 7 

En el caso de equipos de Windows Server que se utilizan como escritorio (por ejemplo, un desarrollador que utiliza una instancia de Windows Server como equipo principal), se pueden registrar las siguientes plataformas: 

* Windows Server 2016 
* Windows Server 2012 R2 
* Windows Server 2012 
* Windows Server 2008 R2 

A continuación, encontrará lo que necesita hacer en su entorno para habilitar el registro de dispositivos unidos a un dominio de Windows con Azure AD en su organización. Verá lo siguiente: 

1. Requisitos previos 
2. Implementación y puesta en servicio 
3. Solución de problemas 
4. P+F 

## <a name="prerequisites"></a>Requisitos previos
El requisito principal para habilitar el registro automático de los dispositivos unidos a un dominio con Azure AD es contar con una versión actualizada de Azure AD Connect. 

Dependiendo de cómo haya implementado Azure AD Connect, si es una instalación rápida o personalizada o una actualización local, se pueden haber configurado automáticamente los requisitos previos siguientes: 

1. Punto de conexión de servicio (SCP) en Active Directory local para la detección de información del inquilino de Azure AD por equipos que se registran en Azure AD. 
2. Reglas de transformación de emisión de AD FS para la autenticación del equipo tras el registro (se aplica a las configuraciones federadas). 

Si tiene equipos unidos a un dominio que no son de Windows 10 en su organización, necesita lo siguiente: 

1. Asegúrese de que está habilitada la directiva para permitir a los usuarios registrar dispositivos en Azure AD. 
2. Asegúrese de que Autenticación integrada de Windows (WIA) se establece como una alternativa válida a Multifactor Authentication (MFA) en AD FS. 

## <a name="service-connection-point-for-discovery-of-azure-ad-tenant"></a>Punto de conexión de servicio para la detección del inquilino de Azure AD
Debe existir un objeto de SCP que contenga información de detección sobre el inquilino de Azure AD donde se registrarán los equipos en la partición de contexto de nomenclatura de configuración del bosque del dominio en donde se unen los equipos. En una configuración de varios bosques de Active Directory, el punto de conexión de servicio debe existir en todos los bosques donde se han unido los equipos. 

El punto de conexión de servicio debe encontrarse en la ubicación siguiente de Active Directory: 

    CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Configuration Naming Context] 

> [!NOTE]
> Para un bosque con un nombre de dominio de Active Directory de ejemplo.com, el contexto de nomenclatura de configuración sería CN=Configuración, DC=ejemplo, DC=com 
> 
> 

Puede comprobar la existencia del objeto y los valores para la detección del inquilino de Azure AD mediante el siguiente script de PowerShell (reemplace el contexto de nomenclatura de configuración del ejemplo por el suyo): 

    $scp = New-Object System.DirectoryServices.DirectoryEntry; 

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com"; 

    $scp.Keywords; 

La salida de $scp.Keywords muestra la información del inquilino de Azure AD de la forma siguiente: 

    azureADName:microsoft.com 

    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47 

Si no existe el punto de conexión de servicio, puede crearlo mediante la ejecución del siguiente script de PowerShell en el servidor de Azure AD Connect: 

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1"; 

    $aadAdminCred = Get-Credential; 

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred; 


> [!NOTE]
> Al ejecutar el cmdlet $aadAdminCred = Get-Credential, use el formato user@example.com para el nombre de usuario de la credencial que se especifica cuando aparece la ventana emergente de Get-Credential.  
> Al ejecutar el cmdlet Initialize-ADSyncDomainJoinedComputerSync, reemplace [nombre de cuenta del conector] por la cuenta de dominio que se usa como la cuenta de conector de Active Directory.  
> El cmdlet anterior utiliza el módulo de PowerShell de Active Directory que se basa en Servicios web de Active Directory (ADWS) en controladores de dominio (DC). Se admite ADWS en Windows Server 2008 R2 o controladores de dominio superiores. Si solo dispone de controladores de dominio de Windows Server 2008 o inferiores, puede utilizar la API System.DirectoryServices a través de PowerShell para crear el punto de conexión de servicio y asignar los valores adecuados de palabras clave. 
> 
> 

## <a name="ad-fs-rules-for-instant-computer-registration-(federated-orgs)"></a>Reglas de AD FS para el registro instantáneo del equipo (organizaciones federadas)
En una configuración federada de Azure AD, los equipos se basarán en AD FS (o en el servidor de federación local) para autenticar a Azure AD para el registro en el servicio de registro de dispositivos de Azure (Azure DRS). 

> [!NOTE]
> En una configuración no federado (es decir, la contraseña de usuario aplica un algoritmo hash sincronizado con Azure AD), los equipos unidos a un dominio de Windows 10 y Windows Server 2016 se autentican con Azure DRS mediante una credencial que escriben en sus cuentas locales del equipo, que se llevan a Azure AD a través de Azure AD Connect. Para los equipos que no son Windows 10 o Windows Server 2016 en una configuración no federada, consulte la sección relativa al paquete de Windows Installer para el registro de equipos unidos a un dominio que no son Windows 10 o Windows Server 2016 en Implementación y puesta en servicio, en este mismo documento, para ver las opciones que debe habilitar la entidad emisora de certificados basada en el dispositivo en su organización. 
> 
> 

Para equipos Windows 10 y Windows Server 2016, Azure AD Connect asocia el objeto de dispositivo en Azure AD con el objeto de la cuenta del equipo local. Para que esto funcione, deben existir las siguientes notificaciones durante la autenticación para que Azure DRS complete el registro y cree el objeto de dispositivo en primer lugar: 

* `http://schemas.microsoft.com/ws/2012/01/accounttype` : contiene el valor de "DJ" que identifica la autenticación principal como un equipo unido a un dominio. 
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid` : contiene el valor del atributo objectGUID de la cuenta de equipo local. 
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` : contiene el SID principal del equipo, correspondiente al valor del atributo objectSid de la cuenta de equipo local. 
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` : contiene el valor adecuado que permite a Azure AD confiar en el token emitido desde AD FS o el STS local. Esto es importante en una configuración de Active Directory de varios bosques donde los equipos pueden unirse a un bosque diferente que el que se conecta a Azure AD, donde se encuentra el STS de AD FS o el local. En el caso de AD FS, el valor debe ser `http://<domain-name>/adfs/services/trust/` donde "\<domain-name\>" es el nombre de dominio validado en Azure AD. 

Para crear estas reglas manualmente, en AD FS puede usar el siguiente script de PowerShell en una sesión conectada al servidor. Tenga en cuenta que la primera línea debe reemplazarse por el nombre de dominio validado en Azure AD para su organización. 

> [!NOTE]
> Si no dispone de AD FS como servidor de federación local, siga las instrucciones de su proveedor para crear las reglas apropiadas para emitir estas notificaciones. 
> 
> 

    $validatedDomain = "example.com"      # Replace example.com with your validated domain name in Azure AD 

    $rule1 = '@RuleName = "Issue object GUID" 

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);' 

    $rule2 = '@RuleName = "Issue account type for domain joined computers" 

      c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");' 

    $rule3 = '@RuleName = "Pass through primary SID" 

      c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

      c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

      => issue(claim = c2);' 

    $rule4 = '@RuleName = "Issue AccountType with the value User when it’s not a computer account" 

      NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]) 

      => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");' 

    $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID" 

      c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] && 

      c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));' 

    $rule6 = '@RuleName = "Update issuer for DJ computer auth" 

      c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$validatedDomain+'/adfs/services/trust/");' 

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5 + $rule6 

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

> [!NOTE]
> Los equipos con Windows 10 y Windows Server 2016 unidos a un dominio se autenticarán con la autenticación integrada de Windows en un punto de conexión activo de WS-Trust hospedado por AD FS. Debe asegurarse de que este punto de conexión esté habilitado. Si usa el proxy de autenticación web, asegúrese también de que este punto de conexión se publique a través del proxy. El punto de conexión es adfs/services/trust/13/windowstransport. Debe aparecer como habilitado en la consola de administración de AD FS en Servicio > Puntos de conexión. Si no dispone de AD FS como el servidor de federación local, siga las instrucciones de su proveedor para asegurarse de que el punto de conexión correspondiente está habilitado. 
> 
> 

## <a name="ensure-ad-fs-is-set-up-to-support-authentication-of-device-for-registration"></a>Asegúrese de que AD FS está configurado para admitir la autenticación del dispositivo para el registro
Debe asegurarse de que Autenticación integrada de Windows (WIA) se establece como una alternativa válida a Multi-Factor Authentication (MFA) en AD FS para el registro de dispositivos.

Para ello debe tener una regla de transformación de emisiones que se pasa en el método de autenticación.

1. Abra la consola de administración de AD FS y vaya a **AD FS>Relaciones de confianza > Relaciones de confianza para usuario autenticado**. 
2. Haga clic con el botón derecho en el objeto de confianza para usuario de confianza Plataforma de identidad de Microsoft Office 365 y seleccione **Edit Claim Rules**(Editar reglas de notificaciones).
3. En la pestaña **Reglas de transformación de emisión**, seleccione **Agregar regla**.
4. Seleccione **Enviar notificaciones con una regla personalizada** en la lista de plantillas **Regla de notificaciones**. 
5. Seleccione **Siguiente**.
6. En el cuadro de texto **Nombre de regla de notificación**, **escriba Regla de notificaciones del método de autenticación**.
7. En el cuadro de texto **Regla de notificación**, escriba `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
   => issue(claim = c);`.
8. En el servidor de federación, abra Windows PowerShell.
9. Escriba el siguiente comando: 
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

**\<RPObjectName>\** es el nombre del objeto para usuario de confianza para su objeto de confianza para usuario de confianza de Azure Active Directory. Este objeto normalmente se denomina Plataforma de identidad de Microsoft Office 365.

## <a name="deployment-and-roll-out"></a>Implementación y puesta en servicio
Una vez completados los requisitos previos, los equipos unidos a dominio están preparados para registrarse con Azure AD. 

Los equipos unidos a un dominio de Actualización de aniversario de Windows 10 y Windows Server 2016 se registrarán automáticamente en Azure AD en el siguiente reinicio o inicio de sesión del usuario en Windows. Los nuevos equipos que están unidos al dominio se registrarán con Azure AD en el reinicio posterior a la operación de unión al dominio. 

> [!NOTE]
> En Windows 10, los equipos unidos a un dominio se registrarán automáticamente con Azure AD solo si se establece el objeto de directiva de grupo del lanzamiento. Para más información, consulte la siguiente sección. 
> 
> 

Para controlar la puesta en servicio del registro automático de los equipos unidos a un dominio de Windows 10 y Windows Server 2016, se puede usar un objeto de directiva de grupo con este fin. Para la puesta en servicio del registro automático de los equipos unidos a un dominio que no sean de Windows 10, hay un paquete de Windows Installer que puede implementar en equipos seleccionados. 

> [!NOTE]
> La directiva de grupo para el control del lanzamiento también desencadena el registro de equipos unidos a un dominio de Windows 8.1. Puede optar por la directiva para el registro de equipos unidos a un dominio de Windows 8.1 o, si tiene una mezcla de versiones de Windows, incluidas las versiones 7 o Windows Server, puede habilitar el registro de todos los equipos que no sean Windows 10/Windows Server 2016 mediante el paquete de Windows Installer. 
> 
> 

### <a name="group-policy-object-to-control-roll-out-of-automatic-registration"></a>Objeto de directiva de grupo para controlar la puesta en servicio del registro automático
Para controlar el lanzamiento del registro automático de equipos unidos a un dominio con Azure AD, puede implementar la directiva de grupo Registrar los equipos asociados a un dominio como dispositivos en los equipos que desee registrar. Por ejemplo, puede implementar la directiva en función de un grupo de seguridad o una unidad organizativa (UO). 

Para establecer la directiva, realice los pasos siguientes: 

1. Abra el Administrador del servidor y vaya a **Herramientas > Administración de directivas de grupo**. 
2. En Administración de directivas de grupo, vaya al nodo de dominio que corresponde al dominio en el que desea habilitar la el registro automático de los equipos de Windows 10 o Windows Server 2016. 
3. Haga clic con el botón derecho en **Objetos de directiva de grupo** y seleccione **Nuevo**. 
4. Asigne un nombre a su objeto de directiva de grupo, por ejemplo, *Registro automático a Azure AD*. Haga clic en Aceptar. 
5. Haga clic con el botón derecho en el nuevo objeto de directiva de grupo y luego seleccione **Editar**. 
6. Vaya a **Configuración del equipo > Directivas > Plantillas administrativas > Componentes de Windows > Registro de dispositivos**, haga clic con el botón derecho en **Registrar los equipos asociados a un dominio como dispositivos** y, finalmente, seleccione **Editar**. 
   
   > [!NOTE]
   > Esta plantilla de directiva de grupo ha cambiado de nombre desde versiones anteriores de la consola de Administración de directivas de grupo. Si está ejecutando una versión anterior de la consola, la directiva se encontrará en Configuración del equipo > Directivas > Plantillas administrativas > Componentes de Windows > Workplace Join con el nombre de unión automática al área de trabajo de equipos cliente. 
   > 
   > 
7. Seleccione el botón de opción **Habilitado** y, luego, haga clic en **Aplicar**. 
8. Haga clic en **Aceptar**. 
9. Vincule el objeto de la directiva de grupo a una ubicación de su elección. Por ejemplo, una determinada unidad organizativa (OU) donde se encuentran equipos o un grupo de seguridad específico que contiene equipos que se registrarán automáticamente con Azure AD. Para habilitar esta directiva para todos los equipos de Windows 10 y Windows Server 2016 unidos a un dominio en su organización, vincule el objeto de la directiva de grupo al dominio. 

## <a name="msi-package-for-non-windows-10-computers"></a>Paquete MSI para equipos que no son Windows 10
Para registrar los equipos unidos a un dominio que ejecuten Windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 o Windows Server 2012 R2, hay disponible un paquete de Windows Installer (.msi) que puede descargar:

* [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
* [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

Debe implementar este paquete mediante un sistema de distribución de software como System Center Configuration Manager. El paquete es compatible con las opciones de instalación silenciosa estándar mediante el parámetro /quiet. Si usa System Center Configuration Manager 2016, disfrutará de ventajas adicionales como la capacidad de realizar el seguimiento de los registros completados. Para más información, consulte [System Center 2016](https://www.microsoft.com/cloud-platform/system-center-2016). 

El instalador crea una tarea programada en el sistema que se ejecuta en el contexto del usuario y se desencadena en el inicio de sesión de usuario en Windows. La tarea registra de forma silenciosa el dispositivo con Azure AD con las credenciales de usuario después de realizar la autenticación mediante la autenticación integrada de Windows. La tarea programada se puede encontrar en la Biblioteca del Programador de tareas en **Microsoft > Unión al área de trabajo**. 

## <a name="additional-topics"></a>Otros temas
* [Acceso condicional de Azure Active Directory](active-directory-conditional-access.md)

<!--HONumber=Oct16_HO2-->


