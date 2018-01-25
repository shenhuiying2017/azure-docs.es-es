---
title: "Políticas del autoservicio de restablecimiento de contraseña: Azure Active Directory"
description: "Opciones de directiva de autoservicio de restablecimiento de contraseña de Azure AD"
services: active-directory
keywords: "Administración de contraseñas de Active Directory, administración de contraseñas, autoservicio de restablecimiento de contraseña de Azure AD"
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
ms.custom: it-pro;seohack1
ms.openlocfilehash: ade7f1d3c868c2ce6ccedbbf11aaf7dc54706cff
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Restricciones y directivas de contraseñas en Azure Active Directory

En este artículo se describen las directivas de contraseña y los requisitos de complejidad asociados a las cuentas de usuario almacenadas en el inquilino de Azure Active Directory (Azure AD).

## <a name="administrator-password-policy-differences"></a>Diferencias entre directivas de contraseña del administrador

Microsoft aplica una directiva segura de restablecimiento de contraseña de *dos puertas* predeterminada para cualquier rol de administrador de Azure. 

Con una directiva de dos puertas, los administradores no tienen posibilidad de usar preguntas de seguridad.

 Una directiva de dos puertas requiere dos elementos de datos de autenticación, como una dirección de correo electrónico *y* un número de teléfono. Se aplica una directiva de dos puertas en las siguientes circunstancias:

* Todos los roles de administrador siguientes se ven afectados:
  * Administrador del departamento de soporte técnico
  * Administrador del soporte técnico del servicio
  * Administrador de facturación
  * Soporte para asociados de nivel 1
  * Soporte para asociados de nivel 2
  * Administrador de servicios de Exchange
  * Administrador de servicios de Lync
  * Administrador de cuenta de usuario
  * Escritores de directorios
  * Administrador global y administrador de la compañía
  * Administrador de servicios de SharePoint
  * Administrador de cumplimiento
  * Administrador de aplicaciones
  * Administrador de seguridad
  * Administrador de roles con privilegios
  * Administrador de servicios de Intune
  * Administrador del servicio de proxy de la aplicación
  * Administrador de servicios de CRM
  * Administrador de servicios de Power BI
  
* Una vez transcurridos 30 días en una versión de prueba

  o

* Hay un dominio individualizado presente, como contoso.com

  o

* Azure AD Connect sincroniza identidades desde el directorio local

### <a name="exceptions"></a>Excepciones
Una directiva de una puerta requiere un elemento de datos de autenticación, como una dirección de correo electrónico *o* un número de teléfono. Se aplica una directiva de una puerta en las siguientes circunstancias:

* Está dentro de los primeros 30 días de una suscripción de prueba

  o

* No hay un dominio individualizado presente (*.onmicrosoft.com) 

  y 

  Azure AD Connect no sincroniza identidades


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Directivas de UserPrincipalName que se aplican a todas las cuentas de usuario

Cada cuenta de usuario que necesita iniciar sesión en Azure AD debe tener un valor de atributo de nombre principal de usuario (UPN) único asociado a esa cuenta. En la siguiente tabla se describen las directivas que se aplican tanto a cuentas de usuario de Active Directory locales (sincronizadas con la nube) como a cuentas de usuario solo en la nube:

| Propiedad | Requisitos de UserPrincipalName |
| --- | --- |
| Caracteres permitidos |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
| Caracteres no permitidos |<ul> <li>Cualquier carácter "@" que no separa el nombre de usuario del dominio.</li> <li>No puede contener un carácter de punto "." inmediatamente antes del símbolo "@".</li></ul> |
| Restricciones de longitud |<ul> <li>La longitud total no debe superar los 113 caracteres.</li><li>Puede haber hasta 64 caracteres antes del símbolo "@".</li><li>Puede haber hasta 48 caracteres después del símbolo "@".</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Directivas de contraseña que solo se aplican a cuentas de usuario en la nube

En la tabla siguiente se describe la configuración de directivas de contraseña disponible que se puede aplicar a cuentas de usuario creadas y administradas en Azure AD:

| Propiedad | Requisitos |
| --- | --- |
| Caracteres permitidos |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Caracteres no permitidos |<ul><li>Caracteres Unicode.</li><li>Espacios.</li><li> Solo contraseñas seguras: no pueden contener un carácter de punto "." inmediatamente antes del símbolo "@".</li></ul> |
| Restricciones de contraseña |<ul><li>Un mínimo de 8 caracteres y un máximo de 16 caracteres.</li><li>Solo contraseñas seguras: requiere al menos tres de los cuatro requisitos siguientes:<ul><li>Caracteres en minúsculas.</li><li>Caracteres en mayúsculas.</li><li>Números (0-9).</li><li>Símbolos (vea las anteriores restricciones de contraseña).</li></ul></li></ul> |
| Duración de las contraseñas |<ul><li>Valor predeterminado: **90** días.</li><li>El valor se puede configurar con el cmdlet `Set-MsolPasswordPolicy`del módulo Active Directory para Windows PowerShell.</li></ul> |
| Notificación de la expiración de contraseñas |<ul><li>Valor predeterminado: **14** días (antes de que expire la contraseña).</li><li>El valor se puede configurar con el cmdlet `Set-MsolPasswordPolicy`.</li></ul> |
| Expiración de las contraseñas |<ul><li>Valor predeterminado: **false** días (indica que la expiración de la contraseña está habilitada).</li><li>El valor se puede configurar para cuentas de usuario individuales mediante el cmdlet `Set-MsolUser`.</li></ul> |
| Historial de cambios de contraseña |La última contraseña *no* puede usarse de nuevo cuando el usuario cambia una contraseña. |
| Historial de restablecimientos de contraseña | La última contraseña *puede* usarse de nuevo cuando el usuario restablece una contraseña olvidada. |
| Bloqueo de cuenta |Después de 10 intentos de inicio de sesión incorrectos con una contraseña incorrecta, el usuario se bloquea durante un minuto. Más intentos de inicio de sesión incorrectos bloquean el usuario durante mayor cantidad de tiempo. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Establecer directivas de expiración de contraseñas en Azure AD

Un administrador global de un servicio en la nube de Microsoft puede usar el módulo Microsoft Azure Active Directory para Windows PowerShell a fin de configurar las contraseñas de usuario de modo que no expiren. También puede usar cmdlets de Windows PowerShell para quitar la configuración de no expirar nunca o ver qué contraseñas de usuario están configuradas para que no expiren nunca. 

Esta guía se aplica a otros proveedores (como Intune y Office 365) que también dependen de Azure AD para los servicios de identidad y directorio. La expiración de contraseñas es la única parte de la directiva que se puede cambiar.

> [!NOTE]
> Solo las contraseñas de cuentas de usuario que no están sincronizadas a través de la sincronización de directorios pueden configurarse para que no caduquen. Para obtener más información sobre la sincronización de directorios, vea el artículo sobre cómo [conectar AD con Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>
>

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Establecer o comprobar directivas de contraseña mediante PowerShell

Para empezar, debe [descargar e instalar el módulo de Azure AD PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Una vez instalado, puede realizar los pasos siguientes para configurar cada campo.

### <a name="how-to-check-the-expiration-policy-for-a-password"></a>Comprobación de la directiva de expiración de una contraseña
1. Conéctese a Windows PowerShell con sus credenciales de administrador de la compañía.
2. Ejecute uno de los siguientes comandos:

   * Para ver si la contraseña de un único usuario está establecida para que no expire nunca, ejecute el cmdlet siguiente con el UPN (por ejemplo, *aprilr@contoso.onmicrosoft.com*) o el identificador de usuario que quiere comprobar: `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`.
   * Para ver la configuración **La contraseña nunca expira** de todos los usuarios, ejecute el siguiente cmdlet`Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`.

### <a name="set-a-password-to-expire"></a>Configuración de una contraseña para que caduque

1. Conéctese a Windows PowerShell con sus credenciales de administrador de la compañía.
2. Ejecute uno de los siguientes comandos:

   * Para establecer la contraseña de un usuario para que expire, ejecute el cmdlet siguiente con el UPN o el identificador de usuario del usuario: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * Para establecer las contraseñas de todos los usuarios de la organización de modo que caduquen, use el siguiente cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>Configure una contraseña para que no caduque nunca

1. Conéctese a Windows PowerShell con sus credenciales de administrador de la compañía.
2. Ejecute uno de los siguientes comandos:

   * Para establecer la contraseña de un usuario para que no expire nunca, ejecute el cmdlet siguiente con el UPN o el identificador de usuario del usuario: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * Para configurar las contraseñas de todos los usuarios de una organización para que nunca caduquen, ejecute el siguiente cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

   > [!WARNING]
   > La antigüedad de las contraseñas establecidas en `-PasswordNeverExpires $true` sigue basándose en el atributo `pwdLastSet`. Si establece las contraseñas de usuario para que no expiren nunca y pasan más de 90 días, las contraseñas expiran. En función del `pwdLastSet` atributo, si cambia la expiración a `-PasswordNeverExpires $false`, todas las contraseñas que tengan una `pwdLastSet` cuya antigüedad sea superior a 90 días requieren que el usuario las cambie la próxima vez que se inicie sesión. Este cambio puede afectar a gran cantidad de usuarios. 

## <a name="next-steps"></a>pasos siguientes

En los siguientes artículos se proporciona información adicional sobre el restablecimiento de contraseña con Azure AD:

* [¿Cómo se realiza un lanzamiento correcto de SSPR?](active-directory-passwords-best-practices.md)
* [Restablecimiento o modificación de la contraseña](active-directory-passwords-update-your-own-password.md).
* [Registro para el autoservicio de restablecimiento de contraseñas](active-directory-passwords-reset-register.md).
* [¿Tiene alguna pregunta acerca de las licencias?](active-directory-passwords-licensing.md)
* [¿Qué datos usa SSPR y cuáles se deben rellenar en lugar de los usuarios?](active-directory-passwords-data.md)
* [¿Qué métodos de autenticación están disponibles para los usuarios?](active-directory-passwords-how-it-works.md#authentication-methods)
* [¿Qué es la escritura diferida de contraseñas y por qué nos interesa?](active-directory-passwords-writeback.md)
* [¿Cómo se informa sobre la actividad de SSPR?](active-directory-passwords-reporting.md)
* [¿Cuáles son todas las opciones en SSPR y qué significan?](active-directory-passwords-how-it-works.md)
* [Creo que algo se ha roto. ¿Cómo se solucionan problemas en SSPR?](active-directory-passwords-troubleshoot.md)
* [Tengo una pregunta que no se ha comentado en ningún otro sitio](active-directory-passwords-faq.md)
