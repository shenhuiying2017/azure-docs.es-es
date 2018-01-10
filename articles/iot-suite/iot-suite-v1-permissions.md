---
title: Conjunto de aplicaciones de IoT de Azure y Azure Active Directory | Microsoft Docs
description: Describe la forma en que el conjunto de aplicaciones Azure IoT usa Azure Active Directory para administrar permisos.
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 246228ba-954a-4d96-b6d6-e53e4590cb4f
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: a032fc4332c697748e658ad2615ed5b0915c56c1
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Permisos en el sitio azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>Qué ocurre al iniciar sesión

La primera vez que inicie sesión en [azureiotsuite.com][lnk-azureiotsuite], el sitio determinará los niveles de permiso que tiene según el inquilino de Azure Active Directory (AAD) seleccionado actualmente y la suscripción de Azure.

1. En primer lugar, para rellenar la lista de los inquilinos que aparece junto al nombre de usuario, el sitio descubre en Azure a qué inquilinos de AAD pertenece el usuario. Actualmente, el sitio no puede obtener tokens de usuario para más de un solo inquilino a la vez. Por tanto, al cambiar inquilinos mediante la lista desplegable de la esquina superior derecha, el sitio inicia si sesión en dicho inquilino para obtener los tokens de este.

2. A continuación, el sitio descubre en Azure qué suscripciones ha asociado al inquilino seleccionado. Las suscripciones disponibles se ven cuando se crea una nueva solución preconfigurada.

3. Por último, el sitio recupera todos los recursos de las suscripciones y los grupos de recursos etiquetados como soluciones preconfiguradas y rellena los iconos de la página principal.

En las secciones siguientes se describen los roles que controlan el acceso a las soluciones preconfiguradas.

## <a name="aad-roles"></a>Roles de AAD

Los roles de AAD controlan las soluciones preconfiguradas de aprovisionamiento de capacidades y administran los usuarios en una solución preconfigurada.

Puede encontrar más información sobre los roles del administrador en AAD en [Asignación de roles de administrador en Azure Active Directory][lnk-aad-admin]. El artículo actual se centra en los roles de **administrador global** y de directorio de **usuario** que utilizan las soluciones preconfiguradas.

### <a name="global-administrator"></a>Administrador global

Puede haber muchos administradores globales por inquilino de AAD:

* Al crear un inquilino de AAD, quien lo crea es de forma predeterminada el administrador global del mismo.
* El administrador global puede aprovisionar una solución preconfigurada y se le asigna el rol **Admin** para la aplicación dentro de su inquilino de AAD.
* Si otro usuario del mismo inquilino de AAD crea una aplicación, el rol predeterminado que se concede al administrador global es **ReadOnly**.
* Un administrador global pueden asignar a los usuarios roles para aplicaciones mediante [Azure Portal][lnk-portal].

### <a name="domain-user"></a>Usuario de dominio

Puede haber muchos usuarios de dominio por inquilino de AAD:

* Un usuario de dominio puede aprovisionar una solución preconfigurada mediante el sitio [azureiotsuite.com][lnk-azureiotsuite]. De forma predeterminada, se concede al usuario de dominio el rol de **administrador** en la aplicación aprovisionada.
* Un usuario de dominio puede crear una aplicación con el script build.cmd en el repositorio [azure-iot-remote-monitoring][lnk-rm-github-repo], [azure-iot-predictive-maintenance][lnk-pm-github-repo] o [azure-iot-connected-factory][lnk-cf-github-repo]. Sin embargo, la función predeterminada concedida al usuario de dominio es **ReadOnly**, ya que un usuario de dominio no tiene permiso para asignar roles.
* Si otro usuario del inquilino de AAD crea una aplicación, al usuario de dominio se le asignará el rol **ReadOnly** de forma predeterminada para dicha aplicación.
* El usuario de dominio no puede asignar roles para aplicaciones; por consiguiente, no puede agregar usuarios o roles a usuarios para una aplicación, aunque la haya aprovisionado.

### <a name="guest-user"></a>Usuario invitado

Puede haber muchos usuarios invitados por inquilino de AAD. Los usuarios invitados tienen un conjunto de derechos limitado en el inquilino de AAD. Como consecuencia, los usuarios invitados no pueden aprovisionar una solución preconfigurada en el inquilino de AAD.

Para más información acerca de los usuarios y roles de AAD, consulte estos recursos:

* [Creación de usuarios en Azure AD][lnk-create-edit-users]
* [Asignación de usuarios a aplicaciones][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Roles de administrador de suscripciones de Azure

Los roles de administrador de Azure controlan la capacidad para asignar una suscripción de Azure a un inquilino de AD.

Encuentre más información sobre los roles de administrador de Azure en el artículo [Adición o cambio de roles de administrador de Azure que administran la suscripción o servicios][lnk-admin-roles].

## <a name="application-roles"></a>Roles de la aplicación

Los roles de aplicación controlan el acceso a los dispositivos de las soluciones preconfiguradas.

Hay dos roles definidos y una función implícita definida en una aplicación aprovisionada:

* **Admin:** tiene control total para agregar, administrar y quitar dispositivos, y modificar la configuración.
* **ReadOnly:** puede ver dispositivos, reglas, acciones, trabajos y telemetría.

Puede encontrar los permisos asignados a cada rol en el archivo de origen [RolePermissions.cs][lnk-resource-cs].

### <a name="changing-application-roles-for-a-user"></a>Cambio de roles de aplicación para un usuario

Puede utilizar el siguiente procedimiento para convertir a un usuario en Active Directory en el administrador de la solución preconfigurada.

Para cambiar los roles de los usuarios, es preciso ser administrador global de AAD:

1. Vaya a [Azure Portal][lnk-portal].
2. Seleccione **Azure Active Directory**.
3. Asegúrese de que utiliza el directorio que eligió en azureiotsuite.com al aprovisionar la solución. Si tiene varios directorios asociados a su suscripción, puede cambiar entre ellos si hace clic en el nombre de cuenta en la parte superior derecha del portal.
4. Haga clic en **Aplicaciones empresariales** y en **Todas las aplicaciones**.
4. Muestre **Todas las aplicaciones** con **Cualquier** estado. A continuación, busque una aplicación con el nombre de la solución preconfigurada.
5. Haga clic en el nombre de la aplicación que coincida con el nombre de la solución preconfigurada.
6. Haga clic en **Usuarios y grupos**.
7. Seleccione el usuario cuyos roles desea cambiar.
8. Haga clic en **Asignar**, seleccione el rol (por ejemplo **Admin**) que desea asignar al usuario y haga clic en la marca de verificación.

## <a name="faq"></a>Preguntas más frecuentes

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Soy administrador de servicios y deseo cambiar la asignación de directorio entre mi suscripción y un inquilino de AAD específico. ¿Cómo completo esta tarea?

Vea [Adición de una suscripción existente al directorio de Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory).

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>Soy miembro o usuario de dominio en el inquilino de AAD y he creado una solución preconfigurada. ¿Cómo se asigna un rol en mi aplicación?

Pida al administrador global que le nombre administrador global en el inquilino de AAD y, después, podrá asignar roles a los usuarios usted mismo. O bien, pida al administrador global que le asigne un rol directamente. Si desea cambiar el inquilino de AAD, en la que se implementó la solución preconfigurada, consulte la siguiente pregunta.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>¿Cómo cambio el inquilino de AAD al que están asignadas mi solución preconfigurada de supervisión remota y mi aplicación?

Puede ejecutar una implementación en la nube desde <https://github.com/Azure/azure-iot-remote-monitoring> e implementar de nuevo con un inquilino de ADD recién creado. Dado que, de manera predeterminada, ya es administrador global cuando crea un inquilino de AAD, tiene permisos para agregar usuarios y asignarles roles.

1. Cree un directorio de AAD en [Azure Portal][lnk-portal].
2. Vaya a <https://github.com/Azure/azure-iot-remote-monitoring>.
3. Ejecute `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (por ejemplo, `build.cmd cloud debug myRMSolution`)
4. Cuando se le solicite, establezca que **tenantid** sea el inquilino recién creado, en lugar del inquilino anterior.

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Deseo cambiar un administrador de servicios o coadministrador cuando haya iniciado sesión con una cuenta de la organización

Consulte el artículo de asistencia [Cambiar el administrador de servicios y el coadministrador cuando ha iniciado sesión con una cuenta organizativa][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>¿Por qué veo este error? "Su cuenta no tiene los permisos adecuados para crear una solución. Póngase en contacto con el administrador de cuentas o inténtelo con otra cuenta."

Observe el diagrama siguiente para obtener instrucciones:

![][img-flowchart]

> [!NOTE]
> Si sigue viendo el error después de validar que usted es un administrador global en el inquilino de AAD y un coadministrador de la suscripción, haga que el administrador de cuenta quite el usuario y vuelva a asignar los permisos necesarios en este orden. En primer lugar, agregue al usuario como administrador global y luego agregue al usuario como coadministrador de la suscripción de Azure. Si los problemas persisten, póngase en contacto con [Ayuda y soporte técnico][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>¿Por qué veo este error cuando tengo una suscripción de Azure? "Para crear soluciones preconfiguradas se requiere una suscripción de Azure. Puede crear una cuenta de evaluación gratuita en pocos minutos".

Si está seguro de que tiene una suscripción de Azure, valide la asignación del inquilino de la suscripción y asegúrese de que se ha seleccionado el inquilino correcto en la lista desplegable. Si ha validado que el inquilino deseado es el correcto, siga el diagrama anterior y valide la asignación de la suscripción y de este inquilino de AAD.

## <a name="next-steps"></a>pasos siguientes
Para obtener más información sobre el Conjunto de aplicaciones de IoT, consulte cómo puede [personalizar una solución preconfigurada][lnk-customize].

[img-flowchart]: media/iot-suite-v1-permissions/flowchart.png

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
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
