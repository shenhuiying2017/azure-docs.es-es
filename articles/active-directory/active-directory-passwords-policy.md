---
title: 'Directiva: SSPR de Azure AD | Microsoft Docs'
description: "Opciones de directiva de autoservicio de restablecimiento de contraseña de Azure AD"
services: active-directory
keywords: "Administración de contraseñas de Active Directory, administración de contraseñas, autoservicio de restablecimiento de contraseña de Azure AD"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: f03c01158bf1a95ec03454b092bbe0314c4ea6be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Restricciones y directivas de contraseñas en Azure Active Directory

En este artículo se describen las directivas de contraseña y los requisitos de complejidad asociados a las cuentas de usuario almacenadas en el inquilino de Azure AD.

## <a name="administrator-password-policy-differences"></a>Diferencias entre directivas de contraseña del administrador

Microsoft aplica una directiva de restablecimiento de contraseña predeterminada sólida de **dos puertas** para cualquier rol de administrador de Azure (ejemplo: administrador global, administrador del departamento de soporte técnico, administrador de contraseñas, etc).

De este modo se impide que los administradores usen preguntas de seguridad y se aplica lo siguiente.

La directiva de dos puertas, que requiere dos fragmentos de los datos de autenticación (dirección de correo electrónico **y** número de teléfono), se aplica en las siguientes circunstancias

* Todos los roles de administrador de Azure
  * Administrador del departamento de soporte técnico
  * Administrador del soporte técnico del servicio
  * Administrador de facturación
  * Soporte para asociados de nivel 1
  * Soporte para asociados de nivel 2
  * Administrador de servicios de Exchange
  * Administrador de servicios de Lync
  * Administrador de cuenta de usuario
  * Escritores de directorios
  * Administrador global y administrador de empresa
  * Administrador de servicios de SharePoint
  * Administrador de cumplimiento
  * Administrador de aplicaciones
  * Administrador de seguridad
  * Administrador de roles con privilegios
  * Administrador de servicios de Intune
  * Administrador del servicio de proxy de la aplicación
  * Administrador de servicios de CRM
  * Administrador de servicios de Power BI
  
* Una vez transcurridos 30 días en una versión de prueba **O**
* El dominio personal está presente (contoso.com) **O**
* Azure AD Connect sincroniza identidades desde el directorio local

### <a name="exceptions"></a>Excepciones
La directiva de una puerta, que requiere un fragmento de los datos de autenticación (dirección de correo electrónico **o** número de teléfono), se aplica en las siguientes circunstancias

* Primeros 30 días de una versión de prueba **O**
* El dominio personal no está presente (* .onmicrosoft) **Y** Azure AD Connect no sincroniza identidades


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Directivas de UserPrincipalName que se aplican a todas las cuentas de usuario

Cada cuenta de usuario que necesita iniciar sesión en Azure AD debe tener un valor de atributo de nombre principal de usuario (UPN) único asociado a esa cuenta. En la siguiente tabla se describen las directivas que se aplican tanto a cuentas de usuario de Active Directory locales sincronizadas con la nube como a cuentas de usuario solo en la nube.

| Propiedad | Requisitos de UserPrincipalName |
| --- | --- |
| Caracteres permitidos |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
| Caracteres no permitidos |<ul> <li>Cualquier carácter "@" que no separa el nombre de usuario del dominio.</li> <li>No puede contener un carácter de punto "." inmediatamente antes del símbolo "@"</li></ul> |
| Restricciones de longitud |<ul> <li>La longitud total no debe superar los 113 caracteres</li><li>64 caracteres antes del símbolo "@"</li><li>48 caracteres después del símbolo "@"</li></ul> |

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>Directivas de contraseña que se aplican solo a cuentas de usuario en la nube

En la tabla siguiente se describe la configuración de políticas de contraseña disponible que se puede aplicar a cuentas de usuario creadas y administradas en Azure AD.

| Propiedad | Requisitos |
| --- | --- |
| Caracteres permitidos |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Caracteres no permitidos |<ul><li>Caracteres Unicode</li><li>Espacios</li><li> **Solo contraseñas seguras**: no pueden contener un carácter de punto "." inmediatamente antes que el símbolo "@"</li></ul> |
| Restricciones de contraseña |<ul><li>8 caracteres como mínimo y 16 caracteres como máximo.</li><li>**Solo contraseñas seguras**: requiere al menos 3 de los 4 requisitos siguientes:<ul><li>Caracteres en minúsculas</li><li>Caracteres en mayúsculas</li><li>Números (0 al 9)</li><li>Símbolos (consulte las restricciones de contraseña anteriores)</li></ul></li></ul> |
| Duración de las contraseñas |<ul><li>Valor predeterminado: **90** días </li><li>El valor se puede configurar mediante el cmdlet Set-MsolPasswordPolicy del Módulo Azure Active Directory para Windows PowerShell.</li></ul> |
| Notificación de la expiración de contraseñas |<ul><li>Valor predeterminado: **14** días (antes de que caduque la contraseña)</li><li>El valor se puede configurar mediante el cmdlet Set-MsolPasswordPolicy.</li></ul> |
| Expiración de las contraseñas |<ul><li>Valor predeterminado: **false** días (indica que la caducidad de contraseña está habilitada) </li><li>El valor se puede configurar para cuentas de usuario individuales mediante el cmdlet Set-MsolUser. </li></ul> |
| Historial de **cambio** de contraseña |**No** puede volver a utilizarse la última contraseña usada al **cambiar** de contraseña. |
| Historial de **restablecimientos** de contraseña | **Puede** utilizar la última contraseña al **restablecer** una contraseña olvidada. |
| Bloqueo de cuenta |Después de 10 intentos de inicio de sesión incorrectos (contraseña incorrecta), el usuario se bloqueará durante un minuto. Más intentos de inicio de sesión incorrectos bloquean el usuario para mayores duraciones. |

## <a name="set-password-expiration-policies-in-azure-active-directory"></a>Establecimiento de directivas de caducidad de contraseña en Azure Active Directory

Un administrador global de un servicio en la nube de Microsoft puede usar el módulo de Microsoft Azure Active Directory para Windows PowerShell para configurar las contraseñas de usuario de modo que no caduquen. También puede usar cmdlets de Windows PowerShell para quitar la configuración de nunca caduca o para ver qué contraseñas de usuario están configuradas para que no caduquen. Esta guía se aplica a otros proveedores como Microsoft Intune y Office 365, que también dependen de Microsoft Azure Active Directory para los servicios de identidad y directorio.

> [!NOTE]
> Solo las contraseñas de cuentas de usuario que no están sincronizadas a través de la sincronización de directorios pueden configurarse para que no caduquen. Para obtener más información sobre la sincronización de directorios, consulte [Conectar AD con Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>
>

## <a name="set-or-check-password-policies-using-powershell"></a>Establecimiento o comprobación de directivas de contraseña mediante PowerShell

Para empezar, debe [descargar e instalar el módulo de Azure AD PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Una vez instalado, puede seguir los pasos siguientes para configurar cada campo.

### <a name="how-to-check-expiration-policy-for-a-password"></a>Comprobación de la directiva de caducidad de una contraseña
1. Conéctese a Windows PowerShell con sus credenciales de administrador de empresa.
2. Ejecute uno de los siguientes comandos:

   * Para ver si se ha configurado una sola contraseña de usuario para que nunca caduque, ejecute el cmdlet siguiente con el nombre principal de usuario (UPN) (por ejemplo, aprilr@contoso.onmicrosoft.com) o el identificador de usuario del usuario que desea comprobar: `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * Para ver la configuración "La contraseña nunca caduca" de todos los usuarios, ejecute el siguiente cmdlet: `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

### <a name="set-a-password-to-expire"></a>Configuración de una contraseña para que caduque

1. Conéctese a Windows PowerShell con sus credenciales de administrador de empresa.
2. Ejecute uno de los siguientes comandos:

   * Para establecer la contraseña de un usuario para que caduque, ejecute el cmdlet siguiente con el nombre principal de usuario (UPN) o el identificador de usuario del usuario: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * Para establecer las contraseñas de todos los usuarios de la organización de modo que caduquen, use el siguiente cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>Configure una contraseña para que no caduque nunca

1. Conéctese a Windows PowerShell con sus credenciales de administrador de empresa.
2. Ejecute uno de los siguientes comandos:

   * Para configurar la contraseña de un usuario para que nunca caduque, ejecute el cmdlet siguiente con el nombre principal de usuario (UPN) o el identificador de usuario del usuario: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * Para configurar las contraseñas de todos los usuarios de una organización para que nunca caduquen, ejecute el siguiente cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>Pasos siguientes

Los vínculos siguientes proporcionan información adicional sobre el restablecimiento de contraseñas con Azure AD:

* [**Inicio rápido**](active-directory-passwords-getting-started.md): preparativos para el autoservicio de administración de contraseñas de Azure AD 
* [**Licencias**](active-directory-passwords-licensing.md): configuración de licencias de Azure AD
* [**Datos**](active-directory-passwords-data.md): información sobre los datos necesarios y cómo se usan para administrar contraseñas
* [**Implementación**](active-directory-passwords-best-practices.md): planee e implemente SSPR en sus usuarios mediante las instrucciones que se encuentran aquí.
* [**Personalización**](active-directory-passwords-customize.md): personalización de la experiencia de SSPR para la empresa
* [**Informes**](active-directory-passwords-reporting.md): detectan si los usuarios acceden a la funcionalidad de SSPR, cuándo lo hacen y dónde.
* [**Profundización técnica**](active-directory-passwords-how-it-works.md): conozca lo que hay detrás para comprender cómo funciona.
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md): ¿Cómo? ¿Por qué? ¿Qué? ¿Dónde? ¿Quién? ¿Cuándo? : respuestas a las preguntas que siempre se ha hecho.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): información para resolver problemas habituales de SSPR
