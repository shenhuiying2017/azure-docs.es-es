---
title: Azure AD Privileged Identity Management | Microsoft Docs
description: "Un tema que explica qué es Privileged Identity Management de Azure AD y cómo usar PIM para mejorar la seguridad de la nube."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: c548ed2e-06e3-4eaf-a63d-0f02ee72da25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 71eb6a2fecbd76c9715b9a556a7ffd31b5b4950e
ms.openlocfilehash: 6b788b3120565895e908bdfc4c9267e715bc5bec


---
# <a name="azure-ad-privileged-identity-management"></a>Administración de identidades con privilegios de Azure AD
Con Privileged Identity Management de Azure Active Directory (AD), puede administrar, controlar y supervisar el acceso dentro de su organización. Esto incluye el acceso a los recursos de Azure AD y de otros servicios en línea de Microsoft, como Office 365 o Microsoft Intune.  

> [!NOTE]
> Privileged Identity Management solo está disponible con la edición Premium P2 de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).

Las organizaciones buscan reducir el número de personas que tienen acceso a información segura o a recursos, ya que de esta manera se reduce la posibilidad de que usuarios malintencionados obtengan acceso a ellos. Sin embargo, los usuarios siguen teniendo la necesidad de llevar a cabo operaciones con privilegios en Azure, Office 365 o SaaS. Las organizaciones proporcionan a los usuarios acceso con privilegios a Azure AD sin supervisar lo que hacen con ellos. Administración de identidades con privilegios de Azure AD le ayuda a resolver este riesgo.  

Privileged Identity Management de Azure AD le ayuda a:  

* Ver qué usuarios son administradores de Azure AD
* Habilitar el acceso administrativo a petición y "justo a tiempo" en servicios de Microsoft Online Services, como Office 365 e Intune
* Obtener informes sobre el historial de acceso de administrador y sobre los cambios en las asignaciones de administrador
* Obtener alertas sobre el acceso a un rol con privilegios

Azure AD Privileged Identity Management puede administrar los roles de organización integrados de Azure AD, que incluyen los siguientes:  

* Administrador global
* Administrador de facturación
* Administrador de servicios  
* Administrador de usuarios
* Administrador de contraseñas

## <a name="just-in-time-administrator-access"></a>Acceso de administrador justo a tiempo
Históricamente, se podía asignar a un usuario un rol de administrador a través del Portal de Azure clásico o Windows PowerShell. Por lo tanto, ese usuario se convierte en **administrador permanente**, siempre activo en su rol asignado. Privileged Identity Management de Azure AD presenta el concepto de **administrador apto**. Los administradores aptos deben ser usuarios que necesiten acceso con privilegios de vez en cuando, pero no todos los días. El rol está inactivo hasta que el usuario necesita acceso, luego realiza un proceso de activación y se convierte en un administrador activo durante una cantidad de tiempo predeterminada.

## <a name="enable-privileged-identity-management-for-your-directory"></a>Habilitación de Privileged Identity Management para el directorio
Para empezar a usar Privileged Identity Management de Azure AD, acceda al [Portal de Azure](https://portal.azure.com/).

> [!NOTE]
> Necesita ser un administrador global con una cuenta profesional (por ejemplo, @yourdomain.com),), no una cuenta Microsoft (por ejemplo, @outlook.com),), para habilitar Azure AD Privileged Identity Management en un directorio.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/) como administrador global de su directorio.
2. Si su organización tiene más de un directorio, seleccione su nombre de usuario en la esquina superior derecha del Portal de Azure. Seleccione el directorio donde va a usar Privileged Identity Management de Azure AD.
3. Seleccione **Más servicios** y use el cuadro de texto Filtro para buscar **Azure AD Privileged Identity Management**.
4. Active **Anclar al panel** y haga clic en **Crear**. Se abre la aplicación Privileged Identity Management.

Si usted es la primera persona que usa Azure AD Privileged Identity Management en su directorio, el [Asistente para seguridad](active-directory-privileged-identity-management-security-wizard.md) le guía en la experiencia de asignación inicial. Después de eso, se convierte automáticamente en el primer **administrador de seguridad** y **administrador de rol con privilegios** del directorio.

El administrador de rol con privilegios es el único que puede administrar el acceso de otros administradores. También puede [conceder a otros usuarios la capacidad de administrar en PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="privileged-identity-management-dashboard"></a>Panel de Administración de identidades con privilegios
Privileged Identity Management de Azure AD proporciona un panel que ofrece información importante, por ejemplo:

* Alertas que destacan oportunidades para mejorar la seguridad
* El número de usuarios que están asignados a cada rol con privilegios  
* El número de administradores aptos y permanentes
* Revisiones de servicios continuas

![Panel de PIM - captura de pantalla][2]

## <a name="privileged-role-management"></a>Administración de roles con privilegios
Con Privileged Identity Management de Azure AD, puede administrar los administradores agregando o quitando administradores permanentes o aptos en cada rol.

![Adición o eliminación de administradores de PIM - captura de pantalla][3]

## <a name="configure-the-role-activation-settings"></a>Configuración de las opciones de activación de rol
Mediante las [opciones de los roles](active-directory-privileged-identity-management-how-to-change-default-settings.md) se pueden configurar las propiedades de activación de roles aptos como:

* La duración del período de activación del rol
* La notificación de activación del rol
* La información que un usuario debe proporcionar durante el proceso de activación del rol  

![Configuración de PIM - activación de administrador - captura de pantalla][4]

Tenga en cuenta que en la imagen, los botones de **Multi-Factor Authentication** están deshabilitados. Para algunos roles con privilegios elevados, se requiere MFA para aumentar la protección.

## <a name="role-activation"></a>Activación de rol
Para [activar un rol](active-directory-privileged-identity-management-how-to-activate-role.md), un administrador apto solicita una "activación" limitada en tiempo para el rol. Se puede solicitar la activación mediante la opción **Activar mi rol** en Administración de identidades con privilegios de Azure AD.

Un administrador que quiera activar un rol necesita inicializar Privileged Identity Management de Azure AD en el Portal de Azure.

La activación del rol es personalizable. En la configuración de PIM, se puede determinar la duración de la activación, así como la información que el administrador debe proporcionar para activar el rol.

![Activación de rol de solicitud de administrador de PIM - captura de pantalla][5]

## <a name="review-role-activity"></a>Revisión de la actividad de un rol
Hay dos maneras de realizar un seguimiento de la forma en que tanto empleados como administradores usan los roles con privilegios. La primera opción es utilizar el [historial de auditoría](active-directory-privileged-identity-management-how-to-use-audit-log.md). Los registros del historial de auditoría realizan un seguimiento de los cambios en las asignaciones de roles con privilegios y el historial de activación de roles.

![Historial de activación de PIM - captura de pantalla][6]

La segunda opción consiste en configurar [revisiones de acceso](active-directory-privileged-identity-management-how-to-start-security-review.md)regulares. Estas revisiones de acceso las puede realizar un revisor asignado (por ejemplo, un jefe de grupo), o bien los propios empleados. Esta es la mejor forma de supervisar quién requiere acceso aún, y quién no.

## <a name="azure-ad-pim-at-subscription-expiration"></a>PIM de Azure AD en la caducidad de la suscripción
Antes de pasar a disponibilidad general, PIM de Azure AD estaba en versión preliminar y no se realizaba ninguna comprobación de licencia para que un inquilino obtuviera una versión preliminar.  Ahora que Azure AD PIM ha pasado a disponibilidad general, debe haber una suscripción de prueba o de pago en el inquilino para poder seguir usando PIM después de diciembre de 2016.  Si su organización no adquiere Azure AD Premium P2 o la suscripción expira, PIM de Azure AD ya no estará disponible en el inquilino.  Encontrará más información en [requisitos de la suscripción de PIM de Azure AD](./privileged-identity-management/subscription-requirements.md)

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png



<!--HONumber=Dec16_HO4-->


