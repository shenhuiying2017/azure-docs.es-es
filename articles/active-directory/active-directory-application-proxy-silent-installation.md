---
title: "Instalación silenciosa del conector del proxy de la aplicación de Azure AD | Microsoft Docs"
description: "Explica cómo realizar una instalación no atendida del conector de Proxy de aplicación de Azure AD para proporcionar acceso remoto seguro a las aplicaciones locales."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: 3aa1c7f2-fb2a-4693-abd5-95bb53700cbb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: daveba
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: a89a64499fba5ad0adf55863d809857f00edb2d5
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Creación de un script de instalación desatendida para el conector del proxy de la aplicación de Azure AD

Este tema le ayuda a crear un script de Windows PowerShell que hace posible la instalación desatendida y el registro para el conector del proxy de la aplicación de Azure AD.

Esta capacidad resulta útil cuando desea hacer lo siguiente:

* Instale el conector en los servidores de Windows que no tienen interfaz de usuario habilitada o a los que no puede acceder con Escritorio remoto.
* Instalar y registrar muchos conectores a la vez.
* Integrar la instalación del conector y el registro como parte de otro procedimiento.
* Crear una imagen de servidor estándar que contiene los bits de conector, pero no está registrada.

Para que el [conector de Proxy de aplicación](application-proxy-understand-connectors.md) funcione debe estar registrado con el directorio de Azure AD mediante un administrador global y una contraseña. Normalmente, esta información se especifica durante la instalación del conector en un cuadro de diálogo emergente, pero también puede usar PowerShell para automatizar este proceso.

Hay dos pasos para una instalación desatendida. Primero, instale el conector. Después, registre el conector con Azure AD. 

## <a name="install-the-connector"></a>Instalación del conector
Siga estos pasos para instalar el conector sin registrarlo:

1. Abra el símbolo del sistema.
2. Ejecute el siguiente comando, en el que el parámetro /q significa instalación desatendida. Una instalación desatendida no le pide que acepte el contrato de licencia para el usuario final.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Registro del conector con Azure AD
Hay dos métodos posibles para registrar el conector:

* Registro del conector mediante un objeto de credenciales de Windows PowerShell
* Registro del conector mediante un token creado sin conexión

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registro del conector mediante un objeto de credenciales de Windows PowerShell
1. Cree un objeto de credenciales de Windows PowerShell `$cred` que contiene un nombre de usuario y una contraseña administrativos para el directorio. Ejecute el comando siguiente, reemplazando *\<username\>* y *\<password\>* por los valores correspondientes:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Vaya a **C:\Archivos de programa\Microsoft AAD App Proxy Connector** y ejecute el siguiente script mediante el objeto `$cred` que creó:
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred

### <a name="register-the-connector-using-a-token-created-offline"></a>Registro del conector mediante un token creado sin conexión
1. Cree un token sin conexión mediante la clase AuthenticationContext con los valores de este fragmento de código:

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.microsoftonline.com/common/oauth2/token?api-version=1.0");

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

## <a name="next-steps"></a>pasos siguientes 
* [Publicar aplicaciones mediante su propio nombre de dominio](active-directory-application-proxy-custom-domains.md)
* [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
* [Solucionar los problemas que tiene con el Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)


