---
title: Azure AD SSPR data requirements (Requisitos de datos de SSPR de Azure AD) | Microsoft Docs
description: "Requisitos de datos del autoservicio de restablecimiento de contraseña de Azure AD y cómo cumplirlos"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 929843825d19c003b5a97363a03ffdd3ae2a2f7d
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Implementación del restablecimiento de contraseña sin necesidad de registro del usuario final

Para implementar el restablecimiento de contraseña de autoservicio (SSPR) de Azure Active Directory (Azure AD), los datos de autenticación deben estar presentes. Algunas organizaciones hacen que los usuarios introduzcan sus propios datos de autenticación. Pero muchas organizaciones prefieren sincronizarse con los datos que ya existen en Active Directory. Los datos sincronizados se ponen a disposición de Azure AD y SSPR sin necesidad de la interacción del usuario:
   * Dé formato correctamente a los datos en su directorio local.
   * Configure [Azure AD Connect mediante la configuración rápida](./connect/active-directory-aadconnect-get-started-express.md).

Para funcionar correctamente, los números de teléfono deben tener el formato *+códigoPaís númeroTeléfono* (ejemplo: +1 4255551234).

> [!NOTE]
> Debe haber un espacio entre el código de país y el número de teléfono.
>
> El restablecimiento de contraseña no admite extensiones telefónicas. Incluso con el formato +1 4255551234X12345, las extensiones se quitan antes de realizarse la llamada.

## <a name="fields-populated"></a>Campos rellenos

Si usa la configuración predeterminada en Azure AD Connect, se realizan las siguientes asignaciones:

| Active Directory local | Azure AD | Información de contacto de autenticación de Azure AD |
| --- | --- | --- |
| telephoneNumber | Teléfono del trabajo | Teléfono alternativo |
| mobile | Teléfono móvil | Teléfono |

Estos campos pueden aparecer vacíos hasta que un usuario confirma sus datos de autenticación.

Un administrador global puede establecer manualmente la información de contacto de autenticación para el usuario, como se muestra en la captura de pantalla siguiente.

![Contacto][Contact]

## <a name="security-questions-and-answers"></a>Preguntas y respuestas de seguridad

Las preguntas y respuestas de seguridad se almacenan de forma segura en el inquilino de Azure AD y los usuarios pueden tener acceso a ellas solo a través del [portal de registro de SSPR](https://aka.ms/ssprsetup). Los administradores no pueden ver ni modificar el contenido de las preguntas y respuestas de otros usuarios.

### <a name="what-happens-when-a-user-registers"></a>Qué ocurre cuando se registra un usuario

Cuando se registre un usuario, la página de registro establecerá los campos siguientes:

* **Teléfono de autenticación**
* **Correo electrónico de autenticación**
* **Preguntas y respuestas de seguridad**

Si ha especificado un valor para **Teléfono móvil** o **Correo electrónico alternativo**, los usuarios podrán usarlos inmediatamente para restablecer sus contraseñas, aunque no se hayan registrado para el servicio. Además, los usuarios ven esos valores cuando se registran por primera vez y pueden modificarlos si lo desean. Cuando están correctamente registrados, dichos valores se conservarán en los campos **Teléfono de autenticación** y **Correo electrónico de autenticación**, respectivamente.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Establecimiento y lectura de datos de autenticación mediante PowerShell

Los siguientes campos pueden establecerse mediante PowerShell:

* **Correo electrónico alternativo**
* **Teléfono móvil**
* **Teléfono del trabajo**: solo puede establecerse si no se sincroniza un directorio local

### <a name="use-powershell-version-1"></a>Uso de PowerShell versión 1

Para empezar, debe [descargar e instalar el módulo de Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Una vez instalado, puede usar los pasos siguientes para configurar cada campo.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Establecimiento de datos de autenticación con PowerShell versión 1

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Lectura de datos de autenticación con PowerShell versión 1

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Lectura de las opciones de teléfono de autenticación y correo electrónico de autenticación

Para leer el **teléfono de autenticación** y el **correo electrónico de autenticación** cuando emplee PowerShell versión 1, utilice los siguientes comandos:

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Uso de PowerShell versión 2

Para empezar, debe [descargar e instalar el módulo de PowerShell de Azure AD versión 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). Una vez instalado, puede usar los pasos siguientes para configurar cada campo.

Para instalar rápidamente desde versiones recientes de PowerShell compatibles con Install-Module, ejecute los siguientes comandos. (La primera línea comprueba si el módulo ya está instalado).

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Establecimiento de datos de autenticación con PowerShell versión 2

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Lectura de datos de autenticación con PowerShell versión 2

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>pasos siguientes

* [¿Cómo se realiza un lanzamiento correcto de SSPR?](active-directory-passwords-best-practices.md)
* [Restablecimiento o modificación de la contraseña](active-directory-passwords-update-your-own-password.md)
* [Registro para el autoservicio de restablecimiento de contraseñas](active-directory-passwords-reset-register.md)
* [¿Tiene alguna pregunta acerca de las licencias?](active-directory-passwords-licensing.md)
* [¿Qué métodos de autenticación están disponibles para los usuarios?](active-directory-passwords-how-it-works.md#authentication-methods)
* [¿Cuáles son las opciones de directiva con SSPR?](active-directory-passwords-policy.md)
* [¿Qué es la escritura diferida de contraseñas y por qué nos interesa?](active-directory-passwords-writeback.md)
* [¿Cómo se informa sobre la actividad de SSPR?](active-directory-passwords-reporting.md)
* [¿Cuáles son todas las opciones en SSPR y qué significan?](active-directory-passwords-how-it-works.md)
* [Creo que algo se ha roto. ¿Cómo se solucionan problemas en SSPR?](active-directory-passwords-troubleshoot.md)
* [Tengo una pregunta que no se ha comentado en ningún otro sitio](active-directory-passwords-faq.md)

[Contact]: ./media/active-directory-passwords-data/user-authentication-contact-info.png "Los administradores globales pueden modificar la información de contacto de autenticación de un usuario"
