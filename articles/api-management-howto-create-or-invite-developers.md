<properties pageTitle="How manage developer accounts in Azure API Management" metaKeywords="" description="Learn how to create or invite developers in Azure API Management" metaCanonical="" services="" documentationCenter="API Management" title="How manage developer accounts in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Administración de cuentas de desarrollador en Administración de API de Azure

En Administración de API (vista previa), los desarrolladores son los usuarios de las API que se exponen mediante Administración de API. En esta guía se muestra cómo crear desarrolladores e invitarlos a usar las API y los productos que usted pone a su disposición con la instancia de Administración de API.

## En este tema

-   [Creación de un desarrollador][Creación de un desarrollador]
-   [Invitación a un desarrollador][Invitación a un desarrollador]
-   [Desactivación o reactivación de una cuenta de desarrollador][Desactivación o reactivación de una cuenta de desarrollador]
-   [Pasos siguientes][Pasos siguientes]

## <a name="create-developer"> </a>Creación de un desarrollador

Para crear un desarrollador, haga clic en la **consola de administración** en el Portal de Azure para su servicio Administración de API. Esta operación le llevará al portal administrativo Administración de API.

> Si todavía no ha creado una instancia de servicio Administración de API, consulte [Creación de una instancia de Administración de API][Creación de una instancia de Administración de API] en el tutorial [Introducción a la Administración de API de Azure][Introducción a la Administración de API de Azure].

![API Management console][API Management console]

Haga clic en **Desarrolladores** en el menú **Administración de API** de la izquierda y haga clic en **Agregar usuario**.

![Create developer][Create developer]

Introduzca el **correo electrónico**, la **contraseña** y el **nombre** del nuevo desarrollador y haga clic en **Guardar**.

![Create developer][1]

De forma predeterminada, las cuentas de desarrollador recién creadas se encuentran en estado **Activo** y están asociadas al grupo **Desarrolladores**.

![New developer][New developer]

Las cuentas de desarrollador que se encuentran en estado **activo** se pueden usar para obtener acceso a todas las API para las que tienen suscripciones. Para asociar el desarrollador recién creado a otros grupos, consulte [Asociación de grupos a desarrolladores][Asociación de grupos a desarrolladores].

## <a name="invite-developer"> </a>Invitación a un desarrollador

Para invitar a un desarrollador, haga clic en **Desarrolladores** en el menú **Administración de API** de la izquierda y haga clic en **Invitar a usuario**.

![Invite developer][Invite developer]

Especifique el nombre y la dirección de correo electrónico del desarrollador y haga clic en **Invitar**.

![Invite developer][2]

Se mostrará un mensaje de confirmación, pero el desarrollador recién invitado no aparecerá en la lista hasta que haya aceptado la invitación.

![Invite confirmation][Invite confirmation]

> Cuando se invita a un desarrollador, se le envía un correo electrónico. Este correo electrónico se genera con una plantilla y se puede personalizar. Para obtener más información, consulte [Configuración de plantillas de correo electrónico][Configuración de plantillas de correo electrónico]

Una vez aceptada la invitación, la cuenta se activa.

## <a name="block-developer"> </a> Desactivación o reactivación de una cuenta de desarrollador

De forma predeterminada, las cuentas de desarrollador recién creadas o a las que se ha invitado se encuentran en estado **Activo**. Para desactivar una cuenta de desarrollador, haga clic en **Bloquear**. Para reactivar una cuenta de desarrollador bloqueada, haga clic en **Activar**. Una cuenta de desarrollador bloqueada no puede obtener acceso al portal para desarrolladores ni llamar a ninguna API.

![Block developer][New developer]

## <a name="next-steps"> </a>Pasos siguientes

Una vez creada una cuenta de desarrollador, se puede asociar a roles y suscribirse a productos y API. Para obtener más información, consulte [Creación y uso de grupos][Creación y uso de grupos].

  [Creación de un desarrollador]: #create-developer
  [Invitación a un desarrollador]: #invite-developer
  [Desactivación o reactivación de una cuenta de desarrollador]: #block-developer
  [Pasos siguientes]: #next-steps
  [Creación de una instancia de Administración de API]: ../api-management-get-started/#create-service-instance
  [Introducción a la Administración de API de Azure]: ../api-management-get-started
  [API Management console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
  [Create developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
  [1]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
  [New developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
  [Asociación de grupos a desarrolladores]: ../api-management-howto-create-groups/#associate-group-developer
  [Invite developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
  [2]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
  [Invite confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
  [Configuración de plantillas de correo electrónico]: ../api-management-howto-configure-notifications/#email-templates
  [Creación y uso de grupos]: ../api-management-howto-create-groups
