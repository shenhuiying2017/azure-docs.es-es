---
title: "Control de acceso basado en rol (RBAC) de Azure para controlar los derechos de acceso para crear y administrar solicitudes de soporte técnico | Microsoft Docs"
description: "Control de acceso basado en rol (RBAC) de Azure para controlar los derechos de acceso para crear y administrar solicitudes de soporte técnico"
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: microsoft-docs
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ms.openlocfilehash: 20ebd324cbf379980b43d255d468673de2b6d950
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Control de acceso basado en rol (RBAC) de Azure para controlar los derechos de acceso para crear y administrar solicitudes de soporte técnico

El [control de acceso basado en rol (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) permite realizar una administración detallada del acceso en Azure.
La creación de solicitudes de soporte técnico en Azure Portal, [portal.azure.com](https://portal.azure.com), usa el modelo RBAC de Azure para definir quién puede crear y administrar solicitudes de soporte técnico.
El acceso se concede mediante la asignación del rol RBAC adecuado a los usuarios, grupos y aplicaciones de un determinado ámbito, que puede ser una suscripción, un grupo de recursos o un recurso.

Veamos un ejemplo: como propietario de un grupo de recursos con permisos de lectura en el ámbito de la suscripción puede administrar todos los recursos en el grupo de recursos, como sitios web, máquinas virtuales y subredes.
Sin embargo, al intentar crear una solicitud de soporte técnico en el recurso de la máquina virtual, aparece el siguiente error

![Error de suscripción](./media/create-manage-support-requests-using-access-control/subscription-error.png)

En la administración de solicitudes de soporte técnico, se necesita permiso de escritura o un rol que tenga la acción de soporte técnico Microsoft.Support/* en el ámbito Suscripción para poder crear y administrar solicitudes de soporte técnico.

En el siguiente artículo se explica cómo se puede usar el control de acceso basado en rol (RBAC) de Azure para crear y administrar solicitudes de soporte técnico en Azure Portal.

## <a name="getting-started"></a>Introducción

Con el ejemplo anterior podría crear una solicitud de soporte técnico para el recurso, si el propietario de la suscripción le asigna un rol RBAC personalizado en la suscripción.
Se pueden crear [roles RBAC personalizados](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) mediante Azure PowerShell, la interfaz de la línea de comandos (CLI) de Azure y la API de REST.

La propiedad Actions de un rol personalizado especifica las operaciones de Azure para las que el rol concede acceso.
Para crear un rol personalizado para la administración de la solicitud de soporte técnico, el rol debe tener la acción Microsoft.Support/*

A continuación, verá un ejemplo de un rol personalizado que se puede usar para crear y administrar solicitudes de soporte técnico.
Hemos denominado a este rol "Support Request Contributor" y así es cómo nos referimos al rol personalizado en este artículo.

``` Json
{
    "Name":  "Support Request Contributor",
    "Id":  "1f2aad59-39b0-41da-b052-2fb070bd7942",
    "IsCustom":  true,
    "Description":  "Lets you create and manage support tickets.",
    "Actions":  [
                    "Microsoft.Support/*"
                ],
    "NotActions":  [
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Siga los pasos que se describen en [este vídeo](https://www.youtube.com/watch?v=-PaBaDmfwKI) para aprender a crear un rol personalizado para su suscripción.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Creación y administración de solicitudes de soporte en Azure Portal

Veamos un ejemplo: es el propietario de la suscripción "Visual Studio MSDN Subscription".
Joe es alguien de su mismo nivel que es el propietario de los recursos de algunos de los grupos de recursos de esta suscripción y tiene permiso de lectura en la suscripción.
Desea conceder a Joe acceso a la capacidad de crear y administrar las incidencias de soporte técnico de los recursos de esta suscripción.

1. El primer paso es ir a la suscripción y en "Configuración" verá una lista de usuarios. Haga clic en el usuario Joe, que tiene acceso de lectura en la suscripción y asígnele un nuevo rol personalizado.

    ![Agregar rol](./media/create-manage-support-requests-using-access-control/add-role.png)

2. Haga clic en "Agregar" en la hoja "Usuarios". Seleccione el rol personalizado "Support Request Contributor" en la lista de roles

    ![Agregar rol de colaborador de soporte técnico](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. Después de seleccionar el nombre del rol, haga clic en "Agregar usuarios" y escriba las credenciales de correo electrónico de Joe. Haga clic en "Seleccionar"

    ![Agregar usuarios](./media/create-manage-support-requests-using-access-control/add-users.png)

4. Haga clic en "Aceptar" para continuar

    ![Agregar acceso](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Ahora verá el usuario con el rol personalizado recién agregado "Support Request Contributor" en la suscripción de la que es propietario

    ![Usuario agregado](./media/create-manage-support-requests-using-access-control/user-added.png)

    Cuando Joe inicia sesión en el portal, ve la suscripción a la que se le agregó.

7. Joe hace clic en "Nueva solicitud de soporte técnico" en la hoja "Ayuda y soporte técnico" y puede crear solicitudes de soporte técnico para "Visual Studio Ultimate con MSDN"

    ![Nueva solicitud de soporte](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Haga clic en "Todos admiten solicitudes" Juan puede ver la lista de solicitudes de soporte creada para esta suscripción ![caso la vista de detalles](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Eliminación del acceso a solicitud de soporte técnico en el Azure Portal

Al igual que se puede conceder acceso a un usuario para crear y administrar solicitudes de soporte técnico, también se puede quitar el acceso al usuario.
Para quitar la capacidad de crear y administrar solicitudes de soporte técnico, vaya a la suscripción, haga clic en "Configuración" y haga clic en el usuario (en este caso, Joe).
Haga clic con el botón derecho en el nombre del rol, "Support Request Contributor", y después en "Quitar"

![Quitar el acceso a la solicitud de soporte técnico](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

Cuando Joe inicia sesión en el portal e intenta crear una solicitud de soporte técnico, aparece el siguiente error

![Error de suscripción-2](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Joe no ve ninguna de las solicitudes de soporte técnico al hacer clic en "All support requests" (Todas las solicitudes de soporte técnico)

![vista de detalles del caso-2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
