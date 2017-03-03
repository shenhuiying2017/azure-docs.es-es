---
title: "Instalación silenciosa del conector del Proxy de la aplicación de Azure AD | Microsoft Docs"
description: "Explica cómo realizar una instalación no atendida del conector de Proxy de aplicación de Azure AD para proporcionar acceso remoto seguro a las aplicaciones locales."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: 3aa1c7f2-fb2a-4693-abd5-95bb53700cbb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 081e45e0256134d692a2da7333ddbaafc7366eaa
ms.openlocfilehash: cf00d47efc613f7bdc152c1b5f0d0830fb44a785
ms.lasthandoff: 02/06/2017


---
# <a name="how-to-silently-install-the-azure-ad-application-proxy-connector"></a>Cómo instalar de forma silenciosa el conector de Proxy de aplicación de Azure AD
Quiere poder enviar un script de instalación a varios servidores de Windows o servidores de Windows Server que no tienen habilitada la interfaz de usuario. En este tema se explica cómo crear un script de Windows PowerShell que permite instalación desatendida para instalar y registrar el conector de Proxy de aplicación de Azure AD.

Esta capacidad resulta útil cuando desea hacer lo siguiente:

* Instalar el conector en máquinas sin ninguna capa de interfaz de usuario o cuando no puede usar RDP en la máquina.
* Instalar y registrar muchos conectores a la vez.
* Integrar la instalación del conector y el registro como parte de otro procedimiento.
* Crear una imagen de servidor estándar que contiene los bits de conector, pero no está registrada.

## <a name="enabling-access"></a>Habilitación del acceso
El proxy de la aplicación funciona mediante la instalación de un pequeño servicio de Windows Server denominado conector dentro de la red. Para que el conector de Proxy de aplicación funcione debe estar registrado con el directorio de Azure AD mediante un administrador global y una contraseña. Normalmente esta información se especifica durante la instalación del conector en un cuadro de diálogo emergente. Si lo prefiere, puede usar Windows PowerShell para crear un objeto de credenciales para escribir la información de registro, o bien puede crear su propio token y usarlo con esta finalidad.

## <a name="step-1--install-the-connector-without-registration"></a>Paso 1: Instalar el conector sin registro
Instale los MSI del conector sin registrar el conector de la siguiente manera:

1. Abra el símbolo del sistema.
2. Ejecute el siguiente comando en el que el modificador /q significa instalación desatendida (la instalación no le pedirá que acepte el contrato de licencia de usuario final).
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="step-2-register-the-connector-with-azure-active-directory"></a>Paso 2: Seleccionar el conector con Azure Active Directory
Para ello, puede usar cualquiera de los métodos siguientes:

* Registro del conector mediante un objeto de credenciales de Windows PowerShell
* Registrar el conector utilizando un token creado sin conexión

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registro del conector mediante un objeto de credenciales de Windows PowerShell
1. Cree el objeto de credenciales de Windows PowerShell ejecutando el siguiente código, donde \<username\> y \<password\> se deben sustituir por el nombre de usuario y la contraseña del directorio:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Vaya a **C:\Archivos de programa\Microsoft AAD App Proxy Connector** y ejecute el script con el objeto de credenciales de PowerShell que creó, donde $cred es el nombre de dicho objeto:
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred

### <a name="register-the-connector-using-a-token-created-offline"></a>Registrar el conector utilizando un token creado sin conexión
1. Cree un token sin conexión mediante la clase AuthenticationContext con los valores del código, por ejemplo:

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }


2. Una vez que tenga el token, cree SecureString con dicho token:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Ejecute el siguiente comando de Windows PowerShell, reemplazando \<tenant GUID\> por su identificador de directorio:

   `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`

## <a name="next-steps"></a>Pasos siguientes 
* [Publicar aplicaciones mediante su propio nombre de dominio](active-directory-application-proxy-custom-domains.md)
* [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
* [Solucionar los problemas que tiene con el Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)



