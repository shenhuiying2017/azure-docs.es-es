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
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: 6f2fd5e4c1a51fe9d1652c9970bcd8d76b25ab60
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Administración de cuentas de usuario en Azure API Management
En Administración de API, los desarrolladores son los usuarios de las API que se exponen mediante Administración de API. En esta guía se muestra cómo crear desarrolladores e invitarlos a usar las API y los productos que usted pone a su disposición con la instancia de API Management. Para obtener información acerca de cómo administrar cuentas de usuario mediante programación, consulte la documentación de [Entidad de usuario](https://msdn.microsoft.com/library/azure/dn776330.aspx) en la referencia sobre [REST de API Management](https://msdn.microsoft.com/library/azure/dn776326.aspx).

## <a name="create-developer"></a>Creación de un desarrollador
Para crear un nuevo desarrollador, haga clic en **Publisher portal** (Portal del publicador) en Azure Portal para su servicio de API Management. De este modo, se abre el portal del publicador de API Management. Si todavía no ha creado una instancia del servicio API Management, consulte [Creación de una instancia del servicio API Management][Create an API Management service instance].

![Portal del publicador][api-management-management-console]

Haga clic en **Usuarios** en el menú **API Management** de la izquierda y haga clic en **Agregar usuario**.

![Create developer][api-management-create-developer]

Escriba el **Correo electrónico**, la **Contraseña** y el **nombre** del nuevo desarrollador y haga clic en **Guardar**.

![Create developer][api-management-add-new-user]

De forma predeterminada, las cuentas de desarrollador recién creadas se encuentran en estado **Activo** y se asocian al grupo **Desarrolladores**.

![New developer][api-management-new-developer]

Las cuentas de desarrollador que se encuentran en estado **activo** se pueden usar para obtener acceso a todas las API para las que tienen suscripciones. Para asociar el desarrollador recién creado a otros grupos, consulte [Asociación de grupos a desarrolladores][How to associate groups with developers].

## <a name="invite-developer"></a>Invitación a un desarrollador
Para invitar a un desarrollador, haga clic en **Usuarios** en el menú **API Management** de la izquierda y haga clic en **Invitar a usuario**.

![Invite developer][api-management-invite-developer]

Especifique el nombre y la dirección de correo electrónico del desarrollador y haga clic en **Invitar**.

![Invite developer][api-management-invite-developer-window]

Se mostrará un mensaje de confirmación, pero el desarrollador recién invitado no aparecerá en la lista hasta que acepte la invitación. 

![Invite confirmation][api-management-invite-developer-confirmation]

Cuando se invita a un desarrollador, se le envía un correo electrónico. Este correo electrónico se genera con una plantilla y se puede personalizar. Para obtener más información, consulte [Configuración de plantillas de correo electrónico][Configure email templates].

Una vez aceptada la invitación, la cuenta se activa.

## <a name="block-developer"></a> Desactivación o reactivación de una cuenta de desarrollador
De forma predeterminada, las cuentas de desarrollador recién creadas o a las que se ha invitado se encuentran en estado **Activo**. Para desactivar una cuenta de desarrollador, haga clic en **Bloquear**. Para reactivar una cuenta de desarrollador bloqueada, haga clic en **Activar**. Una cuenta de desarrollador bloqueada no puede obtener acceso al portal para desarrolladores ni llamar a ninguna API. Para eliminar una cuenta de usuario, haga clic en **Eliminar**.

![Block developer][api-management-new-developer]

## <a name="reset-a-user-password"></a>Restablecimiento de la contraseña del usuario
Para restablecer la contraseña de una cuenta de usuario, haga clic en el nombre de la cuenta.

![Restablecer contraseña][api-management-view-developer]

Haga clic en **Restablecer contraseña** para enviar un vínculo al usuario para restablecer su contraseña.

![Restablecimiento de contraseña][api-management-reset-password]

Para trabajar con cuentas de usuario mediante programación, consulte la documentación de [Entidad de usuario](https://msdn.microsoft.com/library/azure/dn776330.aspx) en la referencia sobre [REST de API Management](https://msdn.microsoft.com/library/azure/dn776326.aspx). Para restablecer la contraseña de una cuenta de usuario a un valor específico, puede utilizar la operación [Actualizar un usuario](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser) y especificar la contraseña que desea utilizar.

## <a name="pending-verification"></a>Pendiente de comprobación
![Pendiente de comprobación][api-management-pending-verification]

## <a name="next-steps"></a>Pasos siguientes
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
