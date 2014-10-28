<properties pageTitle="How to configure notifications and email templates in Azure API Management" metaKeywords="" description="Learn how to configure notifications and email templates in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to configure notifications and email templates in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Configuración de notificaciones y plantillas de correo electrónico en Administración de API de Azure

Administración de API (vista previa) ofrece la posibilidad de configurar notificaciones de eventos específicos, así como plantillas de correo electrónico que se usan para comunicarse con los administradores y desarrolladores de una instancia de Administración de API. Este tema muestra cómo configurar notificaciones de los eventos disponibles y ofrece información general sobre la configuración de plantillas de correo electrónico que se usan para estos eventos.

## En este tema

-   [Configuración de notificaciones del publicador][Configuración de notificaciones del publicador]
-   [Configuración de plantillas de correo electrónico][Configuración de plantillas de correo electrónico]

## <a name="publisher-notifications"> </a>Configuración de notificaciones del publicador

Para configurar las notificaciones, haga clic en **Consola de administración** en el Portal de Azure para su servicio Administración de API. Esta operación le llevará al portal administrativo Administración de API.

> Si todavía no ha creado una instancia de servicio Administración de API, consulte [Creación de una instancia de Administración de API][Creación de una instancia de Administración de API] en el tutorial [Introducción a la Administración de API de Azure][Introducción a la Administración de API de Azure].

![API Management console][API Management console]

Haga clic en **Notificaciones** en el menú **Administración de API** de la izquierda para ver y configurar las notificaciones disponibles.

![Publisher notifications][Publisher notifications]

Se puede configurar la siguiente lista de eventos para notificaciones.

-   **Solicitudes de suscripción (se requiere aprobación)**: los destinatarios y usuarios de correo electrónico especificados recibirán notificaciones de correo electrónico sobre solicitudes de suscripción de productos de API que requieran aprobación.
-   **Nuevas suscripciones**: los destinatarios y usuarios de correo electrónico especificados recibirán notificaciones de correo electrónico sobre nuevas solicitudes de suscripción de productos de API.
-   **Solicitudes de la galería de aplicaciones**: los destinatarios y usuarios de correo electrónico especificados recibirán notificaciones de correo electrónico cuando se envíen nuevas aplicaciones a la galería de aplicaciones.
-   **CCO**: los destinatarios y usuarios de correo electrónico especificados recibirán copias ocultas de todos los correos electrónicos enviados a los desarrolladores.
-   **Nuevo problema o comentario**: los destinatarios y usuarios de correo electrónico especificados recibirán notificaciones de correo electrónico cuando se envíe un nuevo problema o comentario en el portal para desarrolladores.
-   **Cerrar mensaje de cuenta**: los destinatarios y usuarios de correo electrónico especificados recibirán notificaciones de correo electrónico cuando se cierre una cuenta.
-   **Aproximación al límite de cuota de suscripción**: los siguientes destinatarios y usuarios de correo electrónico recibirán notificaciones de correo electrónico cuando el uso de la suscripción se acerque a la cuota de uso.

En cada evento, se pueden especificar destinatarios con el cuadro de texto de dirección de correo electrónico o seleccionar usuarios de una lista.

Para especificar las direcciones de correo electrónico a las que se van a enviar notificaciones, especifíquelas en el cuadro de texto de dirección de correo electrónico. Si tiene varias direcciones de correo electrónico, sepárelas con comas.

![Notification recipients+-][Notification recipients+-]

Para especificar los usuarios a los que se va a notificar, haga clic en **Agregar destinatario**, active la casilla situada junto a los usuarios a los que se va a notificar y haga clic en **Aceptar**.

> Observe que en la lista solo aparecen los administradores.

Después de configurar los destinatarios de notificaciones, haga clic en **Guardar** para aplicar los destinatarios de notificación actualizados.

> Si hay cambios sin guardar y sale de la pestaña **Notificaciones del publicador**, el portal de Administración de API le alertará.

## <a name="email-templates"> </a>Configuración de plantillas de correo electrónico

Administración de API proporciona plantillas de correo electrónico para los mensajes de correo electrónico que se envían durante la administración y el uso del servicio. Se incluyen las siguientes plantillas de correo electrónico.

-   Envío de la galería de aplicaciones aprobado
-   Carta de despedida del desarrollador
-   Notificación de aproximación del límite de cuota del desarrollador
-   Invitación a un usuario
-   Nuevo comentario agregado a un problema
-   Nuevo problema recibido
-   Nueva suscripción activada
-   Confirmación de suscripción renovada
-   Rechazo de la solicitud de suscripción
-   Solicitud de suscripción recibida

Estas plantillas se pueden modificar tal como se desee.

Para ver y configurar las plantillas de correo electrónico de la instancia de Administración de API, haga clic en **Notificaciones** en el menú **Administración de API** de la izquierda y seleccione la pestaña **Plantillas de correo electrónico**.

![Email templates][Email templates]

Para ver o modificar una plantilla específica, selecciónela de la lista desplegable **Plantillas**.

![Email templates list][Email templates list]

Cada plantilla de correo electrónico tiene un asunto en texto sin formato y una definición del cuerpo en formato HTML. Cada elemento se puede personalizar según se desee.

![Email template editor][Email template editor]

La lista **Parámetros** contiene una lista de parámetros que, al insertarlos en el asunto o el cuerpo, sustituirán el valor designado cuando se envíe el correo electrónico. Para insertar un parámetro, sitúe el cursor en donde desee que vaya el parámetro y haga clic en la flecha a la izquierda del nombre del parámetro.

Haga clic en **Vista previa** o en **Enviar una prueba** para ver el aspecto que tendrá el correo electrónico o para enviar un correo electrónico de prueba.

> Observe que los parámetros no se sustituyen con valores reales al obtener la vista previa o enviar una prueba.
>
> Para guardar los cambios efectuados a la plantilla de correo electrónico, haga clic en **Guardar** o, para cancelarlos, haga clic en **Cancelar**.

  [Configuración de notificaciones del publicador]: #publisher-notifications
  [Configuración de plantillas de correo electrónico]: #email-templates
  [Creación de una instancia de Administración de API]: ../api-management-get-started/#create-service-instance
  [Introducción a la Administración de API de Azure]: ../api-management-get-started
  [API Management console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
  [Publisher notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
  [Notification recipients+-]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png
  [Email templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
  [Email templates list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
  [Email template editor]: ./media/api-management-howto-configure-notifications/api-management-email-template.png
