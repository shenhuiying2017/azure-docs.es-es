---
title: "Autenticación basada en certificados de Azure Active Directory - Introducción | Microsoft Docs"
description: "Obtenga información sobre cómo configurar la autenticación basada en certificados en su entorno"
author: MarkusVi
documentationcenter: na
manager: mtillman
ms.assetid: c6ad7640-8172-4541-9255-770f39ecce0e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 5c96f33b8f678155dc4b7a84718e5eadc541f441
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Introducción a la autenticación basada en certificados de Azure Active Directory

La autenticación basada en certificados le permite autenticarse mediante Azure Active Directory con un certificado de cliente en un dispositivo Windows, Android o iOS al conectar su cuenta de Exchange Online a:

- Aplicaciones móviles de Office como Microsoft Outlook y Microsoft Word   

- clientes de Exchange ActiveSync (EAS).

Al configurar esta función, no tendrá que escribir una combinación de nombre de usuario y contraseña en determinadas aplicaciones de correo electrónico y Microsoft Office de su dispositivo móvil.

En este tema:

- Se indican los pasos para configurar y utilizar la autenticación basada en certificados para usuarios de los inquilinos de los planes de Office 365 Enterprise, Empresa, Educación e US Government. Esta característica se encuentra disponible en versión preliminar en los planes Office 365 China, US Government Defense e US Government Federal.

- Se supone que ya tiene una [infraestructura de clave pública (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) y [AD FS](connect/active-directory-aadconnectfed-whatis.md) configurados.    


## <a name="requirements"></a>Requisitos

Para configurar la autenticación basada en certificados, deben cumplirse las siguientes condiciones:  

- La autenticación basada en certificados (CBA) solo se admite para entornos federados de aplicaciones del explorador o clientes nativos que usan autenticación moderna (ADAL). La excepción es Exchange Active Sync (EAS) para EXO, que se puede usar tanto para cuentas federadas como para cuentas administradas.

- La entidad de certificación raíz y las intermedias deben configurarse en Azure Active Directory.  

- Cada entidad de certificación debe tener una lista de revocación de certificados (CRL) a la que puede hacerse referencia a través de una dirección URL con conexión a Internet.  

- Debe tener al menos una entidad de certificación configurada en Azure Active Directory. Puede encontrar pasos relacionados en la sección [Configuración de las entidades de certificación](#step-2-configure-the-certificate-authorities).  

- En Exchange Online, el certificado de cliente debe tener la dirección de correo electrónico enrutable del usuario en el valor de Nombre de la entidad de seguridad o Nombre RFC822 del campo Nombre alternativo del titular (para clientes de Exchange ActiveSync). Azure Active Directory asigna el valor de RFC822 al atributo de dirección de Proxy del directorio.  

- El dispositivo cliente debe tener acceso al menos a una entidad de certificación que emite los certificados de cliente.  

- Se debe haber emitido al cliente un certificado de cliente para la autenticación de cliente.  




## <a name="step-1-select-your-device-platform"></a>Paso 1: Selección de la plataforma de dispositivos

Como primer paso, en la plataforma de dispositivos que le interesa, debe revisar lo siguiente:

- Compatibilidad con aplicaciones móviles de Office
- Requisitos de implementación específicos  

Existe información relacionada para las siguientes plataformas de dispositivos:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)


## <a name="step-2-configure-the-certificate-authorities"></a>Paso 2: Configuración de las entidades de certificación

Para configurar las entidades de certificación en Azure Active Directory, para cada entidad de certificación, cargue lo siguiente:

* La parte pública del certificado en formato *.cer* .
* Las direcciones URL con conexión a Internet en las que se encuentran las listas de revocación de certificados (CRL).

A continuación se presenta el esquema para una entidad de certificación:

    class TrustedCAsForPasswordlessAuth
    {
       CertificateAuthorityInformation[] certificateAuthorities;    
    }

    class CertificateAuthorityInformation

    {
        CertAuthorityType authorityType;
        X509Certificate trustedCertificate;
        string crlDistributionPoint;
        string deltaCrlDistributionPoint;
        string trustedIssuer;
        string trustedIssuerSKI;
    }                

    enum CertAuthorityType
    {
        RootAuthority = 0,
        IntermediateAuthority = 1
    }

Para la configuración, puede usar la [versión 2 de Azure Active Directory PowerShell](/powershell/azure/install-adv2?view=azureadps-2.0):  

1. Inicie Windows PowerShell con privilegios de administrador.
2. Instale el módulo de Azure AD. Es preciso instalar la versión [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33), o una superior.  

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

El primer paso de configuración consiste en establecer una conexión con el inquilino. En cuanto se establece la conexión con el inquilino, puede revisar, agregar, eliminar y modificar las entidades de certificación de confianza definidas en el directorio.

### <a name="connect"></a>Conectar

Para establecer una conexión con el inquilino, use el cmdlet [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0):

    Connect-AzureAD


### <a name="retrieve"></a>Recuperar

Para recuperar las entidades de certificación de confianza definidas en el directorio, use el cmdlet [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0).

    Get-AzureADTrustedCertificateAuthority


### <a name="add"></a>Sumar

Para crear una entidad de certificación de confianza, use el cmdlet [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) y establezca el atributo **crlDistributionPoint** en un valor correcto:

    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint=”<CRL Distribution URL>”
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca


### <a name="remove"></a>Remove

Para quitar una entidad de certificación de confianza, use el cmdlet [Remove-AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0):

    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]


### <a name="modfiy"></a>Modificar

Para modificar una entidad de certificación de confianza, use el cmdlet [Set-AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0):

    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]


## <a name="step-3-configure-revocation"></a>Paso 3: Configuración de revocación

Para revocar un certificado de cliente, Azure Active Directory recupera la lista de revocación de certificados (CRL) de las direcciones URL cargadas como parte de la información de la entidad de certificación y la almacena en caché. La última marca de tiempo de publicación (propiedad**Effective Date** ) de la CRL se utiliza para garantizar que esta es aún es válida. De forma periódica, se hace referencia a la CRL para revocar el acceso a los certificados que forman parte de la lista.

Si se requiere realizar una revocación más instantánea (por ejemplo, si un usuario pierde un dispositivo), se puede invalidar el token de autorización del usuario. Para ello, establezca el valor del campo **StsRefreshTokenValidFrom** de este usuario concreto mediante Windows PowerShell. Tiene que actualizar el campo **StsRefreshTokenValidFrom** para cada usuario cuyo acceso desee revocar.

Para asegurarse de que la revocación persiste, debe establecer la **fecha de vigencia** de la CRL en una fecha posterior al valor que establece **StsRefreshTokenValidFrom** y asegurarse de que el certificado en cuestión se encuentra en la CRL.

En los siguientes pasos se describe el proceso de actualización e invalidación del token de autorización mediante el establecimiento del campo **StsRefreshTokenValidFrom** .

**Para configurar la revocación:**

1. Conéctese con credenciales de administrador al servicio MSOL:

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. Recupere el valor actual de StsRefreshTokensValidFrom de un usuario:

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. Configure que el campo StsRefreshTokensValidFrom del usuario tenga el mismo valor que la marca de tiempo actual.

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

La fecha establecida debe ser futura. De lo contrario, no se establece la propiedad **StsRefreshTokensValidFrom** . Si la fecha es futura, el valor de **StsRefreshTokensValidFrom** se establece en la hora actual (no la fecha que indica el comando Set-MsolUser).


## <a name="step-4-test-your-configuration"></a>Paso 4: Prueba de la configuración

### <a name="testing-your-certificate"></a>Prueba del certificado

Como primera prueba de configuración, debe tratar de iniciar sesión en [Outlook Web Access](https://outlook.office365.com) o [SharePoint Online](https://microsoft.sharepoint.com) con el **explorador del dispositivo**.

Si el inicio de sesión se realiza correctamente, sabrá que:

- El certificado de usuario se ha aprovisionado en el dispositivo de prueba.
- AD FS está configurado correctamente.  


### <a name="testing-office-mobile-applications"></a>Prueba de aplicaciones móviles de Office

**Para probar la autenticación basada en certificados en su aplicación móvil de Office:**

1. En el dispositivo de prueba, instale una aplicación móvil de Office (por ejemplo, OneDrive).
3. Inicie la aplicación.
4. Escriba su nombre de usuario y, luego, seleccione el certificado de usuario que quiera utilizar.

Debe haber iniciado sesión correctamente.

### <a name="testing-exchange-activesync-client-applications"></a>Prueba de aplicaciones cliente de Exchange ActiveSync

Para acceder a Exchange ActiveSync (EAS) mediante la autenticación basada en certificados, debe estar disponible en la aplicación un perfil de EAS que contenga el certificado de cliente.

El perfil de EAS debe contener la información siguiente:

- El certificado de cliente que se usará para la autenticación

- El punto de conexión de EAS (por ejemplo, outlook.office365.com)

Puede configurar un perfil de EAS y colocarlo en el dispositivo utilizando el servicio Administración de dispositivos móviles (MDM) como Intune o agregando el certificado manualmente al perfil de EAS del dispositivo.  

### <a name="testing-eas-client-applications-on-android"></a>Prueba de aplicaciones cliente de EAS en Android

**Para probar la autenticación de certificados:**  

1. Configure un perfil de EAS en la aplicación que cumpla los requisitos anteriores.  
2. Abra la aplicación y verifique que el correo electrónico se esté sincronizando.
