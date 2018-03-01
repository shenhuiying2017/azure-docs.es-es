---
title: "Administración de cuentas de usuario en Azure API Management | Microsoft Docs"
description: "Más información acerca de cómo crear usuarios o invitarlos en Azure API Management"
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 501210c3fab2659deb9594e1bbd9aa51912187e9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Administración de cuentas de usuario en Azure API Management
En Administración de API, los desarrolladores son los usuarios de las API que se exponen mediante Administración de API. En esta guía se muestra cómo crear desarrolladores e invitarlos a usar las API y los productos que usted pone a su disposición con la instancia de API Management. Para obtener información acerca de cómo administrar cuentas de usuario mediante programación, consulte la documentación de [Entidad de usuario](https://msdn.microsoft.com/library/azure/dn776330.aspx) en la referencia sobre [REST de API Management](https://msdn.microsoft.com/library/azure/dn776326.aspx).

## <a name="prerequisites"></a>requisitos previos

Complete las tareas de este artículo: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-developer"></a>Creación de un desarrollador

Para agregar un nuevo usuario, siga los pasos descritos en esta sección:

1. Seleccione la pestaña **Usuarios** a la izquierda de la pantalla.
2. Presione **+Agregar**.
3. Especifique la información correspondiente al usuario.
4. Presione **Agregar**.

    ![Agregar un nuevo usuario](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

De forma predeterminada, las cuentas de desarrollador recién creadas se encuentran en estado **Activo** y se asocian al grupo **Desarrolladores**. Las cuentas de desarrollador que se encuentran en estado **activo** se pueden usar para obtener acceso a todas las API para las que tienen suscripciones. Para asociar el desarrollador recién creado a otros grupos, consulte [Asociación de grupos a desarrolladores][How to associate groups with developers].

## <a name="invite-developer"></a>Invitación a un desarrollador
Para invitar a un desarrollador, siga los pasos descritos en esta sección:

1. Seleccione la pestaña **Usuarios** a la izquierda de la pantalla.
2. Presione **+Invitar**.

Se mostrará un mensaje de confirmación, pero el desarrollador recién invitado no aparecerá en la lista hasta que acepte la invitación. 

Cuando se invita a un desarrollador, se le envía un correo electrónico. Este correo electrónico se genera con una plantilla y se puede personalizar. Para obtener más información, consulte [Configuración de plantillas de correo electrónico][Configure email templates].

Una vez aceptada la invitación, la cuenta se activa.

## <a name="block-developer"></a> Desactivación o reactivación de una cuenta de desarrollador

De forma predeterminada, las cuentas de desarrollador recién creadas o a las que se ha invitado se encuentran en estado **Activo**. Para desactivar una cuenta de desarrollador, haga clic en **Bloquear**. Para reactivar una cuenta de desarrollador bloqueada, haga clic en **Activar**. Una cuenta de desarrollador bloqueada no puede obtener acceso al portal para desarrolladores ni llamar a ninguna API. Para eliminar una cuenta de usuario, haga clic en **Eliminar**.

Para bloquear a un usuario, siga los siguientes pasos.

1. Seleccione la pestaña **Usuarios** a la izquierda de la pantalla.
2. Haga clic en el usuario que quiera bloquear.
3. Presione **Bloquear**.

## <a name="reset-a-user-password"></a>Restablecimiento de la contraseña del usuario

Para trabajar con cuentas de usuario mediante programación, consulte la documentación de [Entidad de usuario](https://msdn.microsoft.com/library/azure/dn776330.aspx) en la referencia sobre [REST de API Management](https://msdn.microsoft.com/library/azure/dn776326.aspx). Para restablecer la contraseña de una cuenta de usuario a un valor específico, puede utilizar la operación [Actualizar un usuario](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser) y especificar la contraseña que desea utilizar.

## <a name="next-steps"> </a>Pasos siguientes
Una vez creada una cuenta de desarrollador, se puede asociar a roles y suscribirse a productos y API. Para obtener más información, consulte [Creación y uso de grupos][How to create and use groups].

[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png


[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
