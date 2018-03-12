---
title: Informe de seguridad de usuarios marcados en riesgo en el portal de Azure Active Directory | Microsoft Docs
description: Aprenda sobre el informe de seguridad de usuarios marcados en riesgo en el portal de Azure Active Directory
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/24/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: ed6201e9edcef39b14b948b6b2f6e0b5da01ec60
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Remedie los usuarios marcados como en riesgo en el portal de Azure Active Directory

Con los informes de seguridad de Azure Active Directory (Azure AD), puede obtener información acerca de la probabilidad de que haya cuentas de usuario en peligro en su entorno. Un [usuario marcado como en riesgo](active-directory-identityprotection.md#users-flagged-for-risk) es un indicador de que una cuenta de usuario puede estar en peligro.

Microsoft se compromete a ayudar a mantener los entornos seguros. Como parte de este compromiso, Microsoft supervisa continuamente aquellas actividades que son inusuales o que coinciden con patrones de ataque conocidos. 


Si se detectan actividades inusuales que puedan indicar un acceso no autorizado a las cuentas de algunos de sus usuarios, recibirá las notificaciones pertinentes, lo que le permitirá realizar las acciones necesarias. El hecho de enviarle notificaciones no significa que los propios sistemas de Microsoft hayan corrido peligro.
 

## <a name="azure-active-directory-report-access"></a>Acceso a informes de Azure Active Directory

Los usuarios marcados por su riesgo se pueden examinar a través de un informe de Azure Active Directory en línea. Si no es suscriptor de Azure, puede realizar el proceso de suscripción sin costo alguno en [http://aka.ms/AccessAAD](http://aka.ms/AccessAAD).  
Una vez que lo complete, puede utilizar sus credenciales de Office 365 para acceder al Centro de administración de Azure. Tenga en cuenta que en el nivel de suscripción básico el nivel de detalle que se proporciona es básico. Los suscriptores Premium de Azure tienen a su disposición más datos y análisis. Para más información, consulte [Informe de seguridad de usuarios marcados en riesgo en el portal de Azure Active Directory](active-directory-reporting-security-user-at-risk.md).

Una vez que haya activado el acceso a Azure AD, se le redirigirá al [portal de Azure AD](https://portal.azure.com). Para ir directamente al informe, vaya a la siguiente dirección URL: [https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UsersAtRisk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UsersAtRisk).

**Para acceder a los informes de Usuarios marcados como en riesgo en el centro de administración de Office 365:**

1.  En el menú de navegación de la izquierda, haga clic en **Centros de administración**. 
2.  Haga clic en **Azure AD**.
3.  Inicie sesión en el **Centro de administración de Azure Active Directory**.
4.  Si en la parte superior de la página se muestra una pancarta que dice **Check out the new portal** (Restaure el portal nuevo).
4.  En el panel de navegación izquierdo, haga clic en **Azure Active Directory**. 
5.  En el panel de navegación, en **Seguridad**, haga clic en **Usuarios marcados en riesgo**.

Examine la información que se muestra aquí. Debe restablecer la contraseña de todas las cuentas que se enumeren aquí. 

## <a name="remediation-actions"></a>Acciones de corrección

Realice las acciones siguientes para ayudar a corregir las cuentas afectadas y proteger su entorno:

1.  [Valide](http://aka.ms/MFAValid) la información correcta de la autenticación multifactor y del restablecimiento de contraseña de autoservicio. 
2.  [Habilite](http://aka.ms/MFAuth) la autenticación multifactor para todos los usuarios. 
3.  Mediante este [script de corrección](http://aka.ms/remediate), puede realizar los pasos siguientes en todas las cuentas afectadas: 

    a. Restablezca la contraseña para proteger la cuenta y terminar las sesiones activas.

    b. Quite los delegados del buzón.

    c. Deshabilite las reglas de reenvío de correo a dominios externos.

    d. Quite la propiedad de reenvío de correo global del buzón.

    e. Habilite MFA en la cuenta del usuario.

    f. Establezca que la complejidad de la contraseña de la cuenta sea alta.

    g. Habilite la auditoría del buzón.

    h. Genere un registro de auditoría para que el administrador lo revise.

4. Investigue el inquilino de Office 365 y otras infraestructuras de TI, lo que incluye una revisión de todos los valores de inquilino, las cuentas de usuario y los valores de configuración por usuario para su posible modificación. Compruebe los indicadores de los métodos de persistencia, así como los indicadores que un intruso puede haber aprovechado como punto inicial de apoyo para obtener las credenciales de la VPN o acceso a otros recursos de la organización. 

5.  Como parte de la investigación, considere si debe enviar notificaciones a las autoridades gubernamentales, lo que incluye el cumplimiento de la ley.

Además, debe:

- Leer e implementar esta [guía](http://aka.ms/fixaccount) para afrontar actividades inusuales. 
- [Habilitar la canalización de la auditoría](http://aka.ms/improvesecurity) para que le ayude a analizar la actividad de su empresa. Una vez completado, el almacén de auditoría empieza a rellenarse con todos los registros de actividad. En este momento, también puede aprovechar la [Búsqueda e investigación del Centro de seguridad y cumplimiento](http://aka.ms/sccsearch). 
- Utilice este [script](http://aka.ms/mailboxaudit1) para habilitar la auditoría de los buzones de todas sus cuentas. 
- Revise los permisos de delegado y las reglas de reenvío de correo de todos los buzones. Este [script de PowerShell](http://aka.ms/delegateforwardrules) se puede usar para realizar esta tarea. 



## <a name="next-steps"></a>Pasos siguientes

- Para más información acerca de Azure Active Directory Identity Protection, consulte [Azure Active Directory Identity Protection](active-directory-identityprotection.md).

