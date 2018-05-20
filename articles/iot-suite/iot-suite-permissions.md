---
title: Aceleradores de soluciones de Azure IoT y Azure Active Directory | Microsoft Docs
description: Describe la forma en que los aceleradores de soluciones de Azure IoT usan Azure Active Directory para administrar permisos.
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 246228ba-954a-4d96-b6d6-e53e4590cb4f
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: b7360ca4df63cac114b0eb1f93375367da6735cc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Permisos en el sitio azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>Qué ocurre al iniciar sesión

La primera vez que inicie sesión en [azureiotsuite.com][lnk-azureiotsuite], el sitio determinará los niveles de permiso que tiene según el inquilino de Azure Active Directory (AAD) seleccionado actualmente y la suscripción de Azure.

1. En primer lugar, para rellenar la lista de los inquilinos que aparece junto al nombre de usuario, el sitio descubre en Azure a qué inquilinos de AAD pertenece el usuario. Actualmente, el sitio no puede obtener tokens de usuario para más de un solo inquilino a la vez. Por tanto, al cambiar inquilinos mediante la lista desplegable de la esquina superior derecha, el sitio inicia si sesión en dicho inquilino para obtener los tokens de este.

2. A continuación, el sitio descubre en Azure qué suscripciones ha asociado al inquilino seleccionado. Las suscripciones disponibles se ven al crear un nuevo acelerador de soluciones.

3. Por último, el sitio recupera todos los recursos de las suscripciones y los grupos de recursos etiquetados como aceleradores de soluciones y rellena los iconos de la página principal.

En las secciones siguientes se describen los roles que controlan el acceso a los aceleradores de soluciones.

## <a name="aad-roles"></a>Roles de AAD

Los roles de AAD controlan los aceleradores de soluciones de aprovisionamiento de capacidades y administran los usuarios en un acelerador de soluciones.

Puede encontrar más información sobre los roles del administrador en AAD en [Asignación de roles de administrador en Azure Active Directory][lnk-aad-admin]. El artículo actual se centra en los roles de **administrador global** y de directorio de **usuario** que utilizan los aceleradores de soluciones.

### <a name="global-administrator"></a>Administrador global

Puede haber muchos administradores globales por inquilino de AAD:

* Al crear un inquilino de AAD, quien lo crea es de forma predeterminada el administrador global del mismo.
* El administrador global puede aprovisionar aceleradores de soluciones básicos y estándar.

### <a name="domain-user"></a>Usuario de dominio

Puede haber muchos usuarios de dominio por inquilino de AAD:

* Un usuario de dominio puede aprovisionar un acelerador de soluciones básico mediante el sitio [azureiotsuite.com][lnk-azureiotsuite].
* Un usuario de dominio puede crear un acelerador de soluciones básico mediante la CLI.

### <a name="guest-user"></a>Usuario invitado

Puede haber muchos usuarios invitados por inquilino de AAD. Los usuarios invitados tienen un conjunto de derechos limitado en el inquilino de AAD. Como consecuencia, los usuarios invitados no pueden aprovisionar un acelerador de soluciones en el inquilino de AAD.

Para más información acerca de los usuarios y roles de AAD, consulte estos recursos:

* [Creación de usuarios en Azure AD][lnk-create-edit-users]
* [Asignación de usuarios a aplicaciones][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Roles de administrador de suscripciones de Azure

Los roles de administrador de Azure controlan la capacidad para asignar una suscripción de Azure a un inquilino de AD.

Encuentre más información sobre los roles de administrador de Azure en el artículo [Adición o cambio de roles de administrador de Azure que administran la suscripción o servicios][lnk-admin-roles].

## <a name="faq"></a>Preguntas más frecuentes

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Soy administrador de servicios y deseo cambiar la asignación de directorio entre mi suscripción y un inquilino de AAD específico. ¿Cómo completo esta tarea?

Vea [Adición de una suscripción existente al directorio de Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory).

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Deseo cambiar un administrador de servicios o coadministrador cuando haya iniciado sesión con una cuenta de la organización

Consulte el artículo de asistencia [Cambiar el administrador de servicios y el coadministrador cuando ha iniciado sesión con una cuenta organizativa][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>¿Por qué veo este error? "Su cuenta no tiene los permisos adecuados para crear una solución. Póngase en contacto con el administrador de cuentas o inténtelo con otra cuenta."

Observe el diagrama siguiente para obtener instrucciones:

![][img-flowchart]

> [!NOTE]
> Si sigue viendo el error después de validar que usted es un administrador global en el inquilino de AAD y un coadministrador de la suscripción, haga que el administrador de cuenta quite el usuario y vuelva a asignar los permisos necesarios en este orden. En primer lugar, agregue al usuario como administrador global y luego agregue al usuario como coadministrador de la suscripción de Azure. Si los problemas persisten, póngase en contacto con [Ayuda y soporte técnico][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>¿Por qué veo este error cuando tengo una suscripción de Azure? "Para crear soluciones preconfiguradas se requiere una suscripción de Azure. Puede crear una cuenta de evaluación gratuita en pocos minutos".

Si está seguro de que tiene una suscripción de Azure, valide la asignación del inquilino de la suscripción y asegúrese de que se ha seleccionado el inquilino correcto en la lista desplegable. Si ha validado que el inquilino deseado es el correcto, siga el diagrama anterior y valide la asignación de la suscripción y de este inquilino de AAD.

## <a name="next-steps"></a>Pasos siguientes
Para seguir obteniendo más información sobre los aceleradores de soluciones de IoT, va cómo puede [personalizar un acelerador de soluciones][lnk-customize].

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com/
[lnk-create-edit-users]: ../active-directory/active-directory-create-users.md
[lnk-assign-app-roles]: ../active-directory/active-directory-coreapps-assign-user-azure-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
