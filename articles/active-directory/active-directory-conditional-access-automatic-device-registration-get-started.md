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
ms.date: 12/14/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: da1a8c45a1753d617e0cb62f99bc47d22838a8e8
ms.openlocfilehash: ac58f3f5498edbd6f7b19db647a9513a485d6dbf


---
# <a name="how-to-configure-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Configuración del registro automático de dispositivos unidos a un dominio de Windows con Azure Active Directory

Los equipos deben estar registrados en Azure Active Directory (Azure AD) para usar el [acceso condicional basado en dispositivos de Azure Active Directory](active-directory-conditional-access.md). Este artículo le proporciona los pasos para configurar el registro automático de dispositivos unidos a un dominio de Windows con Azure AD en su organización.

> [!NOTE]
> La actualización de noviembre de Windows 10 ofrece algunas de las experiencias de usuario mejoradas en Azure AD, pero la Actualización de aniversario de Windows 10 es totalmente compatible con el acceso condicional basado en dispositivos. Para más información sobre el acceso condicional, consulte [Acceso condicional de Azure Active Directory](active-directory-conditional-access.md). Para más información sobre los dispositivos Windows 10 en el lugar de trabajo y cómo los usuarios registran un dispositivo de Windows 10 con Azure AD, consulte [Windows 10 para empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md).
> 
> 

Para dispositivos que ejecutan Windows puede registrar algunas versiones anteriores de Windows, entre las que se incluyen:

- Windows 8.1
- Windows 7

Para dispositivos que ejecutan Windows Server, puede registrar las siguientes plataformas:

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2



## <a name="prerequisites"></a>Requisitos previos

El requisito principal para el registro automático de dispositivos unidos a un dominio con Azure AD es contar con una versión actualizada de Azure Active Directory Connect (Azure AD Connect).

Dependiendo de cómo haya implementado Azure AD Connect, si es una instalación rápida o personalizada o una actualización local, se pueden haber configurado automáticamente los requisitos previos siguientes:

- **Punto de conexión de servicio en Active Directory local** para la detección de información del inquilino de Azure AD por equipos que se registran en Azure AD.
 
- **Reglas de transformación de emisión de Active Directory Federation Services (AD FS)** para la autenticación del equipo tras el registro (se aplica a las configuraciones federadas).

Si algunos dispositivos de la organización no son dispositivos unidos a un dominio de Windows 10, realice los pasos siguientes:

* Establecer una directiva de Azure AD para permitir a los usuarios registrar dispositivos.
* Establecer Autenticación integrada de Windows (WIA) como una alternativa válida a Multi-Factor Authentication (MFA) en AD FS.

## <a name="step-1-configure-service-connection-point"></a>Paso 1: Configurar el punto de conexión de servicio 

Debe existir un objeto de punto de conexión de servicio (SCP) en la partición del contexto de nomenclatura de configuración del dominio de la máquina. El punto de conexión de servicio contiene información para la detección del inquilino de Azure AD donde se registran los equipos. En una configuración de Active Directory de varios bosques, el punto de conexión debe existir en todos los bosques que tienen equipos unidos al dominio.

El SCP se encuentra en:  

**CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[el contexto de nomenclatura de configuración]**

Para un bosque con un nombre de dominio de Active Directory de *ejemplo.com*, el contexto de nomenclatura de configuración sería:  

**CN=Configuración, DC=ejemplo, DC=com**

Con el siguiente script de Windows PowerShell, puede comprobar la existencia del objeto y recuperar los valores de detección: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com";

    $scp.Keywords;

La salida de **$scp.Keywords** muestra la información del inquilino de Azure AD, por ejemplo:

azureADName:microsoft.com  
azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Si el punto de conexión de servicio no existe, créelo mediante la ejecución del siguiente script de PowerShell en el servidor de Azure AD Connect:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;



**Comentarios:**

- Al ejecutar **$aadAdminCred = Get-Credential**, debe escribir un nombre de usuario. Utilice el siguiente formato para el nombre de usuario: **user@example.com** 


- Al ejecutar el cmdlet **Initialize-ADSyncDomainJoinedComputerSync** reemplace [*nombre de cuenta del conector*] por la cuenta de dominio que se usa en la cuenta de conector de Active Directory.
  
- El cmdlet usa el módulo de PowerShell de Active Directory, que se basa en Servicios web de Active Directory (ADWS) en un controlador de dominio. Servicios Web de Active Directory es compatible con los controladores de dominio de Windows Server 2008 R2 y versiones posteriores. Para los controladores de dominio de Windows Server 2008 o versiones anteriores, use la API System.DirectoryServices a través de PowerShell para crear el punto de conexión de servicio y, después, asigne los valores de palabras clave.
 
 



##<a name="step-2-register-your-devices"></a>Paso 2: Registrar los dispositivos

Los pasos adecuados para registrar el dispositivo dependen de si su organización está federada o no. 


### <a name="device-registration-in-non-federated-organizations"></a>Registro de dispositivos en organizaciones no federadas

El registro de dispositivos en una organización no federada solo se admite si se cumple lo siguiente:

- Está ejecutando Windows 10 y Windows Server 2016 en el dispositivo
- Los dispositivos están unidos a un dominio
- Se habilita la sincronización de contraseña mediante Azure AD Connect

Si se cumplen todos estos requisitos, no tendrá que hacer nada para registrar los dispositivos.  


### <a name="device-registration-in-federated-organizations"></a>Registro de dispositivos en organizaciones federadas

En una configuración de Azure AD federada, los dispositivos usan AD FS (o el servidor de federación local) para autenticarse en Azure AD. Se registran en el servicio de registro de dispositivos de Azure Active Directory.

Para equipos con Windows 10 y Windows Server 2016, Azure AD Connect asocia el objeto de dispositivo de Azure AD con el objeto de cuenta del equipo local. Deben existir las siguientes notificaciones durante la autenticación en Azure AD Device Registration Service para que se complete el registro y se cree el objeto de dispositivo:

- **http://schemas.microsoft.com/ws/2012/01/accounttype** contiene el valor de DJ, que identifica el autenticador principal como un equipo unido a dominio.

- **http://schemas.microsoft.com/identity/claims/onpremobjectguid** contiene el valor del atributo **objectGUID** de la cuenta de equipo local.
 
- **http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid** contiene el identificador de seguridad (SID) principal del equipo, que corresponde al valor del atributo **objectSid** de la cuenta de equipo local.

- **http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid** contiene el valor que Azure AD usa para confiar en el token emitido por AD FS o por el servicio local Security Token Service (STS). Esto es importante si tiene varios dominios comprobados en Azure AD. En el caso de AD FS, use el valor **http://\<*domain-name*\>/adfs/services/trust/**, donde **\<domain-name\>** es el nombre de dominio comprobado en Azure AD.

Consulte [Incorporación de su nombre de dominio personalizado a Azure Active Directory](active-directory-add-domain.md) para más información sobre nombres de dominios comprobados.  
Para obtener una lista de los dominios comprobados de la compañía, puede usar el cmdlet [Get-MsolDomain](https://docs.microsoft.com/powershell/msonline/v1/get-msoldomain). 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)


Los equipos con Windows 10 y Windows Server 2016 unidos a un dominio se autenticarán con la autenticación integrada de Windows en un punto de conexión activo de WS-Trust hospedado por AD FS. Debe asegurarse de que este punto de conexión esté habilitado. Si usa el proxy de autenticación web, asegúrese también de que este punto de conexión se publique a través del proxy. El punto de conexión es **adfs/services/trust/13/windowstransport**. 

Debe habilitarse en la consola de administración de AD FS en **Servicio > Puntos de conexión**. Si no dispone de AD FS como el servidor de federación local, siga las instrucciones de su proveedor para asegurarse de que el punto de conexión correspondiente está habilitado. 



> [!NOTE]
> Si no usa AD FS como servidor de federación local, siga las instrucciones de su proveedor para crear las reglas apropiadas para emitir estas notificaciones.
> 
> 




**Para crear las reglas manualmente, en AD FS:**

- Seleccione uno de los siguientes scripts de Windows PowerShell 
- Ejecute el script de Windows PowerShell en una sesión que esté conectada al servidor. 
- Reemplace la primera línea por el nombre de dominio validado de su organización en Azure AD.




#### <a name="setting-ad-fs-rules-in-a-single-domain-environment"></a>Configuración de reglas de AD FS en un entorno de dominio único

Use el siguiente script para agregar las reglas de AD FS si solo tiene **un dominio comprobado**:


    <#----------------------------------------------------------------------
    |   Modify the Azure AD Relying Party to include the claims needed
    |   for DomainJoin++. The rules include:
    |   -ObjectGuid
    |   -AccountType
    |   -ObjectSid
    +---------------------------------------------------------------------#>

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

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 


#### <a name="setting-ad-fs-rules-in-a-multi-domain-environment"></a>Configuración de reglas de AD FS en un entorno de varios dominios

Si tiene más de un dominio comprobado, realice los pasos siguientes:

1. Quite la regla **IssuerID** existente creada con Azure AD Connect.  
Este es un ejemplo de esta regla: c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 


2. Ejecute este script: 

        <#----------------------------------------------------------------------  
        |   Modify the Azure AD Relying Party to include the claims needed  
        |   for DomainJoin++. The rules include:
        |   -ObjectGuid
        |   -AccountType
        |   -ObjectSid
        +---------------------------------------------------------------------#>

        $VerifiedDomain = 'example.com'      # Replace example.com with one of your verified domains

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

        $rule4 = '@RuleName = "Issue AccountType with the value User when its not a computer account" 

        NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]) 

        => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");' 

        $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID" 

        c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));' 

        $rule6 = '@RuleName = "Update issuer for DJ computer auth" 

        c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$VerifiedDomain+'/adfs/services/trust/");' 

        $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

        $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4+ $rule5+  $rule6 

        $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

        Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 



## <a name="step-3-setup-ad-fs-for-authentication-of-device-registration"></a>Paso 3: Configurar AD FS para la autenticación del registro de dispositivos

Asegúrese de que WIA está definido como una alternativa válida a Multi-Factor Authentication para el registro de dispositivos en AD FS. Para ello, debe tener una regla de transformación de emisiones que se pasa en el método de autenticación.

1. En la consola de administración de AD FS, vaya a **AD FS** > **Relaciones de confianza** > **Relaciones de confianza para usuario autenticado**.
2. Haga clic con el botón derecho en el objeto de confianza para usuario de confianza de la Plataforma de identidad de Microsoft Office 365 y seleccione **Editar reglas de notificación**.
3. En la pestaña **Reglas de transformación de emisión**, seleccione **Agregar regla**.
4. Seleccione **Enviar notificaciones con una regla personalizada** en la lista de plantillas **Regla de notificación**.
5. Seleccione **Siguiente**.
6. En el cuadro **Nombre de regla de notificación**, escriba **Regla de notificaciones del método de autenticación**.
7. En el cuadro **Regla de notificación** escriba esta regla:  
**c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);**
8. En el servidor de federación, escriba este comando de PowerShell:
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

**\<RPObjectName\>** es el nombre del objeto de usuario de confianza para su objeto de confianza de usuario de confianza de Azure AD. Normalmente, este objeto se llama **Plataforma de identidad de Microsoft Office 365**.



##<a name="step-4-deployment-and-rollout"></a>Paso 4: Implementar y lanzar

Una vez completados los requisitos previos, los equipos unidos a dominio están preparados para registrarse en Azure AD.

Los equipos con la Actualización de aniversario de Windows 10 o con Windows Server 2016 unidos a un dominio se registrarán automáticamente en Azure AD en el siguiente reinicio o cuando el usuario inicie sesión en Windows. Los nuevos equipos que se usan al dominio se registrarán en Azure AD cuando el dispositivo se reinicie después de la operación de unión al dominio.

> [!NOTE]
> Los equipos con Windows 10 unidos a un dominio en los que se ejecute la actualización de noviembre de Windows 10 solo se registrarán automáticamente en Azure AD si se establece el objeto de directiva de grupo del lanzamiento.
> 
> 

Para controlar el lanzamiento del registro automático de los equipos con Windows 10 y Windows Server 2016 unidos a un dominio, se puede usar un objeto de directiva de grupo. 

Para el lanzamiento del registro automático de los equipos sin Windows 10 unidos a un dominio, hay un paquete de Windows Installer que puede implementar en los equipos seleccionados.

> [!NOTE]
> En el caso de los equipos que no tengan Windows 10 o Windows Server 2016, se recomienda usar el paquete de Windows Installer como se describe en este mismo documento.
> 
> 

### <a name="create-a-group-policy-object-to-control-the-rollout-of-automatic-registration"></a>Creación de un objeto de directiva de grupo para controlar el lanzamiento del registro automático

Para controlar el lanzamiento del registro automático de equipos unidos a un dominio con Azure AD, puede implementar la directiva de grupo **Registrar los equipos asociados a un dominio como dispositivos** en los equipos que desee registrar. Por ejemplo, puede implementar la directiva en un grupo de seguridad o en una unidad organizativa.

**Para establecer la directiva:**

1. Abra el Administrador del servidor y vaya a **Herramientas** > **Administración de directivas de grupo**.
2. Vaya al nodo del dominio correspondiente al dominio donde desee activar el registro automático de los equipos con Windows 10 o Windows Server 2016.
3. Haga clic con el botón derecho en **Objetos de directiva de grupo** y seleccione **Nuevo**.
4. Escriba un nombre para el objeto de directiva de grupo. Por ejemplo, *Registro automático de Azure AD*. Seleccione **Aceptar**.
5. Haga clic con el botón derecho en el nuevo objeto de directiva de grupo y seleccione **Editar**.
6. Vaya a **Configuración del equipo** > **Directivas** > **Plantillas administrativas** > **Componentes de Windows** > **Registro de dispositivos**. Haga clic con el botón derecho en **Registrar los equipos asociados a un dominio como dispositivos** y seleccione **Editar**.
   
   > [!NOTE]
   > Esta plantilla de directiva de grupo ha cambiado de nombre desde versiones anteriores de la consola de Administración de directivas de grupo. Si usa una versión anterior de la consola, vaya a **Configuración del equipo** > **Directivas** > **Plantillas administrativas** > **Componentes de Windows** > **Unión al área de trabajo** > **Unir equipos cliente al área de trabajo automáticamente**.
   > 
   > 
7. Seleccione **Habilitado** y luego **Aplicar**.
8. Seleccione **Aceptar**.
9. Vincule el objeto de la directiva de grupo a una ubicación de su elección. Por ejemplo, puede vincularlo a una unidad organizativa específica. También puede vincularlo a un grupo de seguridad específico de equipos que se registren automáticamente en Azure AD. Para establecer esta directiva para todos los equipos con Windows 10 y Windows Server 2016 unidos a un dominio en su organización, vincule el objeto de directiva de grupo al dominio.

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>Paquetes de Windows Installer para equipos sin Windows 10
Para registrar equipos unidos a un dominio que ejecuten Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 en un entorno federado, puede descargar e instalar estos archivos de paquete de Windows Installer (.msi):

* [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
* [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

Debe implementar el paquete mediante un sistema de distribución de software como System Center Configuration Manager. El paquete admite las opciones de instalación silenciosa estándar mediante el parámetro *quiet*. System Center Configuration Manager 2016 ofrece ventajas adicionales respecto a versiones anteriores, como la capacidad de realizar el seguimiento de los registros completados. Para más información, consulte [System Center 2016](https://www.microsoft.com/en-us/cloud-platform/system-center).

El instalador crea una tarea programada en el sistema que se ejecuta en el contexto del usuario. La tarea se desencadena cuando el usuario inicia sesión en Windows. La tarea registra de forma silenciosa el dispositivo en Azure AD con las credenciales de usuario después de realizar la autenticación mediante IWA. Para ver la tarea programada, vaya a **Microsoft** > **Unión al área de trabajo** y, a continuación, vaya a la biblioteca del programador de tareas.

## <a name="next-steps"></a>Pasos siguientes
* [Acceso condicional de Azure Active Directory](active-directory-conditional-access.md)




<!--HONumber=Dec16_HO4-->


