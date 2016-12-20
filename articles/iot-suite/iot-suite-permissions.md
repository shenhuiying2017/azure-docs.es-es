---
title: Conjunto de aplicaciones de IoT de Azure y Azure Active Directory | Microsoft Docs
description: Describe la forma en que el conjunto de aplicaciones Azure IoT usa Azure Active Directory para administrar permisos.
services: 
suite: iot-suite
documentationcenter: 
author: aguilaaj
manager: timlt
editor: 
ms.assetid: 246228ba-954a-4d96-b6d6-e53e4590cb4f
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2016
ms.author: araguila
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 61985bf2e112fe7b0a46ece07fd56eb14ccc3d45


---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Permisos en el sitio azureiotsuite.com
## <a name="what-happens-when-you-sign-in"></a>Qué ocurre al iniciar sesión
La primera vez que inicie sesión en [azureiotsuite.com][lnk-azureiotsuite], el sitio determinará los niveles de permiso que tiene según el inquilino de Azure Active Directory (AAD) seleccionado actualmente y la suscripción de Azure.

1. En primer lugar, el sitio descubre en Azure a qué inquilinos de AAD pertenece, con el fin de rellenar la lista de los inquilinos que aparece junto al nombre de usuario con el que ha iniciado sesión. En este momento, el sitio no puede obtener tokens de usuario para más de un solo inquilino a la vez. Como consecuencia, al cambiar a otro inquilino mediante la lista desplegable de la esquina superior derecha, el sitio vuelve a iniciar si sesión en dicho inquilino para obtener los tokens de dicho inquilino.
2. A continuación, el sitio descubre en Azure qué suscripciones ha asociado al inquilino seleccionado. Las suscripciones disponibles se ven cuando se crea una nueva solución preconfigurada.
3. Por último, el sitio recupera todos los recursos de las suscripciones y los grupos de recursos etiquetados como soluciones preconfiguradas y rellena los iconos de la página principal.

En las secciones siguientes se describen los roles que controlan el acceso a las soluciones preconfiguradas.

## <a name="aad-roles"></a>Roles de AAD
Los roles de AAD controlan las soluciones preconfiguradas de aprovisionamiento de capacidades y administran los usuarios en una solución preconfigurada.

Puede encontrar más información acerca de los roles de administrador en AAD en [Asignación de roles de administrador en Azure AD][lnk-aad-admin] pero este artículo se centra principalmente en los roles **Administrador global** y **Miembro o usuario de dominio**, tal como los usan las soluciones preconfiguradas.

**Administrador global:** puede haber muchos administradores globales por inquilino de AAD. Al crear un inquilino de AAD, quien lo crea es de forma predeterminada el administrador global del mismo. El administrador global puede aprovisionar una solución preconfigurada y se le asigna el rol **ADMINISTRATOR** de la aplicación en su inquilino de AAD. Sin embargo, si otro usuario del mismo inquilino de AAD crea una aplicación, el rol predeterminado que se concede al administrador global es **IMPLICIT READ ONLY**. Los administradores globales pueden asignar roles para aplicaciones desde el [Portal de Azure clásico][lnk-classic-portal].

**Miembro o usuario de dominio:** puede haber miembros o usuarios de dominio por inquilino de AAD. Un usuario de dominio puede aprovisionar una solución preconfigurada mediante el sitio [azureiotsuite.com][lnk-azureiotsuite]. El rol predeterminado que se les concede en la aplicación que aprovisionan es **ADMINISTRATOR**. Pueden crear una aplicación con el script build.cmd del repositorio [azure-iot-remote-monitoring][lnk-rm-github-repo] o [azure-iot-predictive-maintenance][lnk-pm-github-repo] pero el rol predeterminado que se les concede es **IMPLICIT READONLY**, ya que no tienen permiso para asignar roles. Si otro usuario en el inquilino de AAD crea una aplicación, se le asignará el rol **IMPLICIT READONLY** en dicha aplicación. No tiene la capacidad de asignar roles en aplicaciones; por consiguiente, no puede agregar usuarios o roles a usuarios en una aplicación, aunque la haya aprovisionado.

**Invitado o usuario invitado:** puede haber muchos invitados o usuarios invitados por inquilino de AAD. Los usuarios invitados tienen un conjunto de derechos limitado en el inquilino de AAD. Como consecuencia, los usuarios invitados no pueden aprovisionar una solución preconfigurada en el inquilino de AAD.

Para obtener más información, consulte los siguientes recursos:

* [Creación o edición de usuarios en Azure AD][lnk-create-edit-users]
* [Asignación de roles de aplicación en AAD][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Roles de administrador de suscripciones de Azure
Los roles de administrador de Azure controlan la capacidad para asignar una suscripción de Azure a un inquilino de AD.

Puede encontrar más información acerca de los roles de coadministrador de Azure, administrador de servicios y administrador de cuentas en el artículo [Incorporación o cambio del coadministrador, administrador de servicio y administrador de cuenta de Azure][lnk-admin-roles].

## <a name="application-roles"></a>Roles de la aplicación
Los roles de aplicación controlan el acceso a los dispositivos de las soluciones preconfiguradas.

En la aplicación que se crea cuando se aprovisiona una solución preconfigurada, hay dos roles definidos y un rol implícito definido.

* **ADMINISTRATOR:** tiene control total para agregar, administrar y quitar dispositivos
* **READ ONLY:** tiene capacidad para ver dispositivos
* **IMPLICIT READ ONLY:** es el mismo que Read Only, pero se concede a todos los usuarios de su inquilino de AAD. Esto se realizó para facilitar el desarrollo. Para quitar este rol, modifique el archivo de origen [RolePermissions.cs][lnk-resource-cs].

### <a name="changing-application-roles-for-a-user"></a>Cambio de roles de aplicación para un usuario
Puede utilizar el siguiente procedimiento para convertir a un usuario en Active Directory en el administrador de la solución preconfigurada.

Para cambiar los roles de los usuarios, es preciso ser administrador global de AAD:

1. Vaya al [Portal de Azure clásico][lnk-classic-portal].
2. Seleccione **Active Directory**.
3. Haga clic en el nombre de su inquilino de AAD (es decir, el directorio que eligió en azureiotsuite.com al aprovisionar la solución).
4. Haga clic en **Aplicaciones**.
5. Haga clic en el nombre de la aplicación que coincida con el nombre de la solución preconfigurada. Si no ve la aplicación en la lista, cambie la lista desplegable **Mostrar** por **Aplicaciones que tiene mi compañía** y haga clic en la marca de verificación.
6. Haga clic en **Usuarios**.
7. Seleccione el usuario cuyos roles desea cambiar.
8. Haga clic en **Asignar**, seleccione el rol (por ejemplo **Admin**) que desea asignar al usuario y haga clic en la marca de verificación.

## <a name="faq"></a>P+F
### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-do-this"></a>Soy administrador de servicios y deseo cambiar la asignación de directorio entre mi suscripción y un inquilino de AAD específico. ¿Cómo se hace?
1. Vaya al [Portal de Azure clásico][lnk-classic-portal] y haga clic en **Configuración** en la lista de servicios situada a la izquierda.
2. Seleccione la suscripción a la que desea cambiar la asignación de directorio.
3. Haga clic en **Editar directorio**.
4. En la lista desplegable, seleccione el **directorio** que le desea usar. Haga clic en la flecha hacia adelante.
5. Confirme la asignación de directorio y los coadministradores afectados. Tenga en cuenta que si se mueve desde otro directorio, se quitan todos los coadministradores del directorio original.

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>Soy miembro o usuario de dominio en el inquilino de AAD y he creado una solución preconfigurada. ¿Cómo se asigna un rol en mi aplicación?
Pida a un administrador global que le asigne como administrador global en el inquilino de AAD para obtener permisos para asignar roles a usuarios, o bien pida a un administrador global que le asigne un rol. Si desea cambiar el inquilino de AAD, en la que se implementó la solución preconfigurada, consulte la siguiente pregunta.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>¿Cómo cambio el inquilino de AAD al que están asignadas mi solución preconfigurada de supervisión remota y mi aplicación?
Puede ejecutar una implementación en la nube desde <https://github.com/Azure/azure-iot-remote-monitoring> e implementar de nuevo con un inquilino de ADD recién creado. Dado que para crear un inquilino de AAD nuevo es preciso ser administrador global, tendrá acceso para agregar usuarios y asignar roles a dichos usuarios.

1. Cree un nuevo directorio de AAD en el [Portal de administración de Azure][lnk-classic-portal].
2. Vaya a <https://github.com/Azure/azure-iot-remote-monitoring>.
3. Ejecute `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (por ejemplo, `build.cmd cloud debug myRMSolution`)
4. Cuando se le solicite, establezca que **tenantid** sea el inquilino recién creado, en lugar del inquilino anterior.

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Deseo cambiar un administrador de servicios o coadministrador cuando haya iniciado sesión con una cuenta de la organización
Consulte el artículo de asistencia [Cambiar el administrador de servicios y el coadministrador cuando ha iniciado sesión con una cuenta organizativa][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>¿Por qué veo este error? "Su cuenta no tiene los permisos adecuados para crear una solución. Póngase en contacto con el administrador de cuentas o inténtelo con otra cuenta."
Observe el diagrama siguiente:

![][img-flowchart]

> [!NOTE]
> Si sigue viendo el error después de validar que usted es un administrador global en el inquilino de AAD y un coadministrador de la suscripción, haga que el administrador de cuenta quite el usuario y vuelva a asignar los permisos necesarios en este orden: agregue al usuario como administrador global y, a continuación, agregue al usuario como coadministrador de la suscripción de Azure. Si los problemas persisten, póngase en contacto con [Ayuda y soporte técnico][lnk-help-support].
> 
> 

**¿Por qué veo este error cuando tengo una suscripción de Azure?** *Para crear soluciones preconfiguradas se requiere una suscripción de Azure. Puede crear una cuenta de evaluación gratuita en pocos minutos.*

Si está seguro de que tiene una suscripción de Azure, valide la asignación del inquilino de la suscripción y asegúrese de que se ha seleccionado el inquilino correcto en la lista desplegable. Si ha validado que el inquilino deseado es el correcto, siga el diagrama anterior y validar la asignación de la suscripción y de este inquilino de AAD.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre el Conjunto de aplicaciones de IoT, consulte cómo puede [personalizar una solución preconfigurada][lnk-customize].

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-aad-admin]: https://azure.microsoft.com/documentation/articles/active-directory-assign-admin-roles/
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-create-edit-users]: https://azure.microsoft.com/documentation/articles/active-directory-create-users/
[lnk-assign-app-roles]: https://azure.microsoft.com/documentation/articles/active-directory-application-manifest/
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md



<!--HONumber=Nov16_HO3-->


