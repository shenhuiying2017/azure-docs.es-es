---
title: "Administración de cuentas administrativas de acceso de emergencia en Azure AD | Microsoft Docs"
description: "Este artículo describe cómo usar las cuentas de acceso de emergencia para ayudar a las organizaciones a restringir el acceso con privilegios en un entorno de Azure Active Directory existente."
services: active-directory
keywords: No agregue ni edite palabras clave sin consultar a su responsable de SEO Champ.
author: markwahl-msft
ms.author: billmath
ms.date: 12/13/2017
ms.topic: article-type-from-white-list
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.openlocfilehash: 1545fb9a89794a74efbb855c4480040973c3308e
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="manage-emergency-access-administrative-accounts-in-azure-ad"></a>Administración de cuentas administrativas de acceso de emergencia en Azure AD 

Para la mayoría de las actividades cotidianas, los usuarios no necesitan derechos de *administrador global*. No se debería asignar permanentemente a los usuarios a este rol, ya que podrían realizar de forma no intencionada alguna tarea que requiera permisos más elevados de los que deberían tener. Cuando los usuarios no necesitan actuar como administrador global, deberían activar la asignación de roles mediante Azure Active Directory (Azure AD) Privileged Identity Management (PIM) en sus propias cuentas o en una cuenta administrativa alternativa.

Además de que los usuarios se puedan asignar derechos de acceso administrativo a ellos mismos, debe impedir que se le bloquee de forma no intencionada de la administración de su inquilino de Azure AD ya que no podría iniciar sesión ni activar una cuenta existente de usuario individual como administrador. Puede mitigar el efecto de una falta involuntaria de acceso administrativo mediante el almacenamiento de dos o más *cuentas de acceso de emergencia* en su inquilino.

Las cuentas de acceso de emergencia pueden ayudar a las organizaciones a restringir el acceso con privilegios en un entorno de Azure Active Directory existente. Estas cuentas tienen privilegios elevados y no se asignan a usuarios específicos. Las cuentas de acceso de emergencia se limitan a escenarios "excepcionales" o de emergencia, situaciones en las que no se pueden usar las cuentas administrativas normales. Las organizaciones deben incluir como objetivo la restricción del uso de las cuentas de emergencia solo para aquellos casos en los que sean necesarias.

Puede que una organización necesite usar una cuenta de acceso de emergencia en las siguientes situaciones:

 - Las cuentas de usuario están federadas y la federación no está disponible en este momento debido a una interrupción de la red de telefonía móvil o una interrupción del proveedor de identidades. Por ejemplo, si se ha interrumpido el host del proveedor de identidades de su entorno, puede que los usuarios no puedan iniciar sesión cuando Azure AD les redirija a su proveedor de identidades. 
 - Los administradores están registrados mediante Azure Multi-Factor Authentication y todos sus dispositivos individuales están deshabilitados. Puede que los usuarios no puedan completar la autenticación multifactor para activar un rol. Por ejemplo, una interrupción de la red de telefonía móvil les impide responder a llamadas telefónicas o recibir mensajes de texto, los únicos dos mecanismos de autenticación que registraron para sus dispositivos. 
 - La persona con el acceso administrativo global más reciente ha dejado la organización. Azure AD impide que se pueda eliminar la última cuenta de *administrador global*, pero no impide que esta se pueda eliminar o deshabilitar de forma local. Es posible que alguna de estas situaciones impida a la organización recuperar la cuenta.

## <a name="initial-configuration"></a>Configuración inicial

Cree dos o más cuentas de acceso de emergencia. Estas deben ser cuentas que estén solo en la nube, que usen el dominio \*.onmicrosoft.com y que no estén federadas ni sincronizadas desde un entorno local. 

Las cuentas no se deberían asociar a ningún usuario individual de la organización. Las organizaciones se deben asegurar de que las credenciales de estas cuentas están protegidas y solo las conocen los usuarios que están autorizados para usarlas. 

> [!NOTE]
> La contraseña de una cuenta de acceso de emergencia se encuentra normalmente dividida en dos o tres partes, escrita en diferentes fragmentos de papel que se almacenan en cajas de seguridad a prueba de incendios que están en ubicaciones separadas seguras. 
>
> Asegúrese de que las cuentas de acceso de emergencia no están conectadas a ningún teléfono móvil suministrado por un empleado, ni a tokens de hardware que viajen con empleados individuales ni con otras credenciales específicas de un empleado. Esta precaución debe incluir también los casos en los que un empleado individual pueda no estar disponible cuando se necesiten las credenciales. 

### <a name="initial-configuration-with-permanent-assignments"></a>Configuración inicial con asignaciones permanentes

Una opción es hacer a los usuarios miembros permanentes del rol *administrador global*. Esta opción sería adecuada para organizaciones que no tienen suscripciones de Azure AD Premium P2.

Para reducir el riesgo de ataques como resultado de una contraseña que se ha puesto en peligro, Azure AD recomienda que requiera autenticación multifactor a todos los usuarios individuales. Este grupo debe incluir a los administradores y a todos aquellos (por ejemplo, los agentes financieros) cuyas cuentas, si se ponen en peligro, tendrían un impacto significativo. 

Sin embargo, si su organización no tiene dispositivos compartidos, puede que la autenticación multifactor no sea posible para estas cuentas de acceso de emergencia. Si va a configurar una directiva de acceso condicional para requerir [el registro mediante autenticación multifactor para cada administrador](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) de Azure AD y de otras aplicaciones conectadas de software como servicio (SaaS), tendrá que configurar las exclusiones de directiva para excluir a las cuentas de acceso de emergencia de este requisito.

### <a name="initial-configuration-with-approvals"></a>Configuración inicial con aprobaciones

Otra opción consiste en configurar a los usuarios como aptos y como aprobadores para activar el rol de *administrador global*. Esta opción requiere que su organización tenga suscripciones de Azure AD Premium P2. También requeriría una opción de la autenticación multifactor que sea adecuada para su uso compartido entre varios usuarios y el entorno de red. Estos requisitos se deben a que la activación del rol de *administrador global* requiere que los usuarios hayan realizado previamente la autenticación multifactor. Para más información, consulte [Exigencia de MFA en Privileged Identity Management de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa).

No se recomienda usar una autenticación multifactor que esté asociada con dispositivos personales para cuentas de acceso de emergencia. En caso de emergencia real, la persona que necesita acceso a un dispositivo registrado con autenticación multifactor puede que no sea la persona que tiene el dispositivo personal. 

Tenga en cuenta también el panorama de posibles amenazas. Por ejemplo, puede surgir una circunstancia imprevista, como un desastre natural, durante la cual puede que las redes de telefonía móvil u otras redes no estén disponibles. Es importante garantizar que todos los dispositivos registrados se almacenen en una ubicación conocida y segura que tenga varias formas de comunicarse con Azure AD.

## <a name="ongoing-monitoring"></a>Supervisión continua

Supervise el [inicio de sesión en Azure AD y los registros de auditoría](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) para analizar los inicios de sesión y actividades de auditoría de las cuentas de acceso de emergencia. Normalmente, esas cuentas no deben iniciar sesión y no deberían realizar cambios, por lo que su uso es anómalo y requiere una investigación de seguridad.

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>Se debe efectuar una validación de comprobación de cuentas en intervalos regulares.

Efectúe la validación de la cuenta en los siguientes casos como mínimo:
- Cada 90 días.
- Cuando se ha producido un cambio reciente en el personal de TI, como un cambio de trabajo, una salida o un nuevo empleado.
- Cuando se han cambiado las suscripciones de Azure AD de la organización.

Para formar a los miembros del personal para que usen las cuentas de acceso de emergencia, haga lo siguiente:

* Asegúrese de que el personal de supervisión de seguridad sea consciente de que la actividad de comprobación de las cuentas es continua.
* Compruebe que las cuentas de usuarios en la nube pueden iniciar sesión y activar sus roles, y que aquellos usuarios que tengan que realizar estos pasos en caso de emergencia conozcan el proceso.
* Asegúrese de que estos no han registrado la autenticación multifactor ni el restablecimiento de contraseña de autoservicio (SSPR) en ningún dispositivo de usuario individual o con información personal. 
* Si las cuentas se han registrado para la autenticación multifactor en un dispositivo, para su uso durante la activación de rol, asegúrese de que este dispositivo sea accesible para todos los administradores que podrían tener que usarlo en caso de emergencia. Compruebe también que el dispositivo se ha registrado mediante dos mecanismos que no compartan un modo de avería común. Por ejemplo, el dispositivo puede comunicarse con Internet a través de la red inalámbrica de la oficina y a través de una red de un proveedor de telefonía móvil.
* Actualice las credenciales de la cuenta.

## <a name="next-steps"></a>pasos siguientes
- [Agregue un usuario basado en la nube](add-users-azure-active-directory.md) y [Asigne un nuevo usuario al rol de administrador global](active-directory-users-assign-role-azure-portal.md).
- [Suscríbase a Azure Active Directory Premium](active-directory-get-started-premium.md), si no lo ha hecho anteriormente.
- [Solicite Azure Multi-Factor Authentication a los usuarios individuales asignados como administradores](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states).
- [Configure otras protecciones adicionales para los administradores globales de Office 365](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560), si lo usa.
- [Realice una revisión de acceso de los administradores globales](active-directory-privileged-identity-management-how-to-start-security-review.md) y [cambie los administradores globales existentes a roles de administrador más específicos](active-directory-assign-admin-roles-azure-portal.md).


