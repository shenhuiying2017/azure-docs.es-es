<properties 
    pageTitle="Introducción a la autenticación basada en certificados en iOS | Microsoft Azure" 
    description="Obtenga información sobre cómo configurar la autenticación basada en certificados en soluciones con dispositivos iOS." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/22/2016" 
    ms.author="markvi" />



# Introducción a la autenticación basada en certificados en iOS (versión preliminar pública)

> [AZURE.SELECTOR]
- [iOS](active-directory-certificate-based-authentication-ios.md)
- [Android](active-directory-certificate-based-authentication-android.md)


En este tema se muestra cómo configurar y utilizar la autenticación basada en certificados (CBA) en un dispositivo iOS para usuarios de los inquilinos de los planes de Office 365 Enterprise, Empresa y Educación.

Gracias a CBA, podrá autenticarse mediante Azure Active Directory con un certificado de cliente en dispositivos Android o iOS al conectar su cuenta de Exchange Online a:

- aplicaciones móviles de Office como Microsoft Outlook y Microsoft Word,
- clientes de Exchange ActiveSync (EAS).

Al configurar esta función, no tendrá que escribir una combinación de nombre de usuario y contraseña en determinadas aplicaciones de correo electrónico y Microsoft Office de su dispositivo móvil.
 

## Requisitos y escenarios admitidos  



### Requisitos generales 


En todos los escenarios de este tema, hay que realizar las siguientes tareas:

- Acceder a las entidades de certificación para emitir certificados de cliente.

- Estas entidades deben configurarse en Azure Active Directory. En la sección [Introducción](#getting-started) puede encontrar instrucciones detalladas acerca de cómo completar la configuración.

- La entidad de certificación raíz y las intermedias deben configurarse en Azure Active Directory.

- Cada entidad de certificación debe tener una lista de revocación de certificados (CRL) a la que puede hacerse referencia a través de una dirección URL con conexión a Internet.

- El certificado de cliente debe emitirse para la autenticación de cliente.


- En Exchange Online, el certificado de cliente debe tener la dirección de correo electrónico enrutable del usuario en el valor de Nombre de la entidad de seguridad o Nombre RFC822 del campo Nombre alternativo del titular (solo para clientes de Exchange ActiveSync). Azure Active Directory asigna el valor de RFC822 al atributo de dirección de Proxy del directorio.



### Compatibilidad con aplicaciones móviles de Office 

| Aplicaciones | Soporte técnico |
| ---                       | ---          |
| Word, Excel y PowerPoint | ![Comprobar][1] |
| OneNote | ![Comprobar][1] |
| OneDrive | ![Comprobar][1] |
| Outlook | Próximamente |
| Yammer | ![Comprobar][1] |
| Skype Empresarial | Próximamente |


### Requisitos  

La versión del sistema operativo del dispositivo debe ser iOS 9 y posterior.

Se debe configurar un servidor de federación.

Se requiere Azure Authenticator para las aplicaciones de Office en iOS.

Para que Azure Active Directory revoque un certificado de cliente, el token de ADFS debe tener las siguientes notificaciones:

  - `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (el número de serie del certificado de cliente)

  - `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (la cadena del emisor del certificado de cliente)

Azure Active Directory agrega estas notificaciones para el token de actualización, en caso de que estén disponibles en el token de ADFS (o en cualquier otro token SAML). Cuando hay que validar el token de actualización, esta información se utiliza para comprobar la revocación.

Se recomienda actualizar las páginas de error de ADFS con lo siguiente:

- El requisito de instalar Azure Authenticator en iOS

- Instrucciones sobre cómo obtener un certificado de usuario

Para más información, consulte [Personalizar las páginas de inicio de sesión de AD FS](https://technet.microsoft.com/library/dn280950.aspx).



### Compatibilidad con clientes de Exchange ActiveSync 


Hay algunas aplicaciones de Exchange ActiveSync que son compatibles con iOS 9 o posterior. Para determinar si la aplicación de correo electrónico admite esta característica, póngase en contacto con el desarrollador de la aplicación.



## Introducción 


Para comenzar, tendrá que configurar las entidades de certificación en Azure Active Directory. En cada una de ellas, cargue lo siguiente:

- La parte pública del certificado en formato *.cer*.

- Las direcciones URL con conexión a Internet en las que se encuentran las listas de revocación de certificados (CRL).
 

A continuación se muestra el esquema de una entidad de certificación:

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


Para cargar la información, puede usar el módulo de Azure AD mediante Windows PowerShell. Abajo se muestran algunos ejemplos para agregar, quitar o modificar una entidad de certificación.



### Configuración del inquilino de Azure AD para la autenticación basada en certificados 

1. Inicie Windows PowerShell con privilegios de administrador.

2. Instale el módulo de Azure AD. Es preciso instalar la versión [1\.1.143.0](http://www.powershellgallery.com/packages/AzureADPreview/1.1.143.0), o una superior.

        Install-Module -Name AzureADPreview –RequiredVersion 1.1.143.0 

3. Conéctelo al inquilino de destino:

        Connect-AzureAD 

### Adición de una nueva entidad de certificación

1. Establezca varias propiedades de la entidad de certificación y agréguelas a Azure Active Directory:

        $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]" 
        $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation 
        $new_ca.AuthorityType=0 
        $new_ca.TrustedCertificate=$cert 
        New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca 

5. Obtenga las entidades de certificación:

        Get-AzureADTrustedCertificateAuthority 


### Recuperación de las entidades de certificación de la lista

Recupere las entidades de certificación almacenadas actualmente en Azure Active Directory para el inquilino:

        Get-AzureADTrustedCertificateAuthority 


### Eliminación de una entidad de certificación

1.	Recupere las entidades de certificación:

		$c=Get-AzureADTrustedCertificateAuthority 


2. Quite el certificado de la entidad de certificación:

		Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2] 


### Modificación de una entidad de certificación 

1.	Recupere las entidades de certificación:

		$c=Get-AzureADTrustedCertificateAuthority 


2. Modifique las propiedades de la entidad de certificación:

		$c[0].AuthorityType=1 

3. Establezca la **entidad de certificación**:

		Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0] 




## Prueba de aplicaciones móviles de Office  

Para probar la autenticación basada en certificados en su aplicación móvil de Office, siga estos pasos:

1.	En el dispositivo de prueba, instale una aplicación móvil de Office (por ejemplo, OneDrive) del App Store.

2.	Compruebe que el certificado de usuario se ha aprovisionado en el dispositivo de prueba.

3.	Inicie la aplicación.

4.	Escriba su nombre de usuario y, luego, seleccione el certificado de usuario que quiera utilizar.

Debe haber iniciado sesión correctamente.





## Prueba de aplicaciones cliente de Exchange ActiveSync

Para acceder a Exchange ActiveSync mediante la autenticación basada en certificados, debe estar disponible en la aplicación un perfil de EAS que contenga el certificado de cliente. El perfil de EAS debe contener la información siguiente:

- El certificado de cliente que se usará para la autenticación

- El punto de conexión de EAS debe ser outlook.office365.com, ya que, actualmente, esta característica es compatible con el entorno multiempresa en línea de Exchange.

Puede configurar un perfil de EAS y colocarlo en el dispositivo utilizando un servicio MDM como Intune o agregando el certificado manualmente al perfil de EAS del dispositivo.

### Prueba de aplicaciones cliente de EAS en iOS 

Para probar la autenticación basada en certificados con la aplicación de correo electrónico nativa de iOS 9 o posterior, siga estos pasos:

1.	Configure un perfil de EAS que cumpla los requisitos anteriores.

2.	Instale el perfil en el dispositivo iOS (con un servicio MDM como Intune o la aplicación Apple Configurator).

3.	Cuando el perfil se haya instalado correctamente, abra la aplicación Mail nativa y compruebe que se está sincronizando el correo electrónico.



## Revocación

Para revocar un certificado de cliente, Azure Active Directory recupera la lista de revocación de certificados (CRL) de las direcciones URL cargadas como parte de la información de la entidad de certificación y la almacena en caché. La última marca de tiempo de publicación (propiedad **Effective Date**) de la CRL se utiliza para garantizar que esta es aún es válida. De forma periódica, se hace referencia a la CRL para revocar el acceso a los certificados que forman parte de la lista.

Si se requiere realizar una revocación más instantánea (por ejemplo, si un usuario pierde un dispositivo), se puede invalidar el token de autorización del usuario. Para ello, establezca el valor del campo **StsRefreshTokenValidFrom** de este usuario concreto mediante Windows PowerShell. Tiene que actualizar el campo **StsRefreshTokenValidFrom** para cada usuario cuyo acceso desee revocar.
 
Para asegurarse de que la revocación persiste, debe establecer la **fecha de vigencia** de la CRL en una fecha posterior al valor que establece **StsRefreshTokenValidFrom** y asegurarse de que el certificado en cuestión se encuentra en la CRL.
 
En los siguientes pasos se describe el proceso de actualización e invalidación del token de autorización mediante el establecimiento del campo **StsRefreshTokenValidFrom**.

1. Conéctese con credenciales de administrador al servicio MSOL:

		$msolcred = get-credential 
		connect-msolservice -credential $msolcred 

1.	Recupere el valor actual de StsRefreshTokensValidFrom de un usuario:

		$user = Get-MsolUser -UserPrincipalName test@yourdomain.com` 
		$user.StsRefreshTokensValidFrom 


1.	Configure que el campo StsRefreshTokensValidFrom del usuario tenga el mismo valor que la marca de tiempo actual.

		Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")


La fecha establecida debe ser futura. De lo contrario, no se establece la propiedad **StsRefreshTokensValidFrom**. Si la fecha es futura, el valor de **StsRefreshTokensValidFrom** se establece en la hora actual (no la fecha que indica el comando Set-MsolUser).



<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png

<!---HONumber=AcomDC_0727_2016-->