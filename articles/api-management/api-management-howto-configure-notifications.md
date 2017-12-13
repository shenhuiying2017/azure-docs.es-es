---
title: "Configuración de notificaciones y plantillas de correo electrónico en Azure API Management | Microsoft Docs"
description: "Obtenga información acerca de cómo configurar notificaciones y plantillas de correo electrónico en Azure API Management."
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
ms.openlocfilehash: ec560bbab3caf4cde090ed3c9a47ccc0afcb2492
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Configuración de notificaciones y plantillas de correo electrónico en Azure API Management
Administración de API ofrece la posibilidad de configurar notificaciones de eventos específicos, así como plantillas de correo electrónico que se usan para comunicarse con los administradores y desarrolladores de una instancia de API Management. Este tema muestra cómo configurar notificaciones de los eventos disponibles y ofrece información general sobre la configuración de plantillas de correo electrónico que se usan para estos eventos.

## <a name="publisher-notifications"></a>Configuración de notificaciones del publicador
Para configurar las notificaciones, haga clic en **Portal para editores** en Azure Portal en su servicio API Management. De este modo, se abre el portal del publicador de API Management.

![Portal del publicador][api-management-management-console]

> [!NOTE] 
> Si todavía no ha creado una instancia del servicio API Management, consulte [Creación de una instancia del servicio API Management][Create an API Management service instance].

Haga clic en **Notificaciones** en el menú **API Management** de la izquierda para ver las notificaciones disponibles.

![Notificaciones del publicador][api-management-publisher-notifications]

Se puede configurar la siguiente lista de eventos para notificaciones.

* **Solicitudes de suscripción (se requiere aprobación)** : los destinatarios y usuarios de correo electrónico especificados recibirán notificaciones por correo electrónico sobre solicitudes de suscripción de productos de API que requieran aprobación.
* **Nuevas suscripciones** : los destinatarios y usuarios de correo electrónico especificados recibirán notificaciones por correo electrónico sobre nuevas suscripciones de productos de API.
* **Solicitudes de la galería de aplicaciones** : los destinatarios y usuarios de correo electrónico especificados recibirán notificaciones por correo electrónico cuando se envíen nuevas aplicaciones a la galería de aplicaciones.
* **CCO** : los destinatarios y usuarios de correo electrónico especificados recibirán copias carbón ocultas de todos los correos electrónicos enviados a los desarrolladores.
* **Nuevo problema o comentario** : los destinatarios y usuarios de correo electrónico especificados recibirán notificaciones por correo electrónico cuando se envíe un nuevo problema o comentario en el portal para desarrolladores.
* **Cerrar mensaje de cuenta** : los destinatarios y usuarios de correo electrónico especificados recibirán notificaciones por correo electrónico cuando se cierre una cuenta.
* **Aproximación al límite de cuota de suscripción** : los siguientes destinatarios y usuarios de correo electrónico recibirán notificaciones por correo electrónico cuando el uso de la suscripción se acerque a la cuota de uso.

En cada evento, se pueden especificar destinatarios con el cuadro de texto de dirección de correo electrónico o seleccionar usuarios de una lista.

Para especificar las direcciones de correo electrónico a las que se van a enviar notificaciones, especifíquelas en el cuadro de texto de dirección de correo electrónico. Si tiene varias direcciones de correo electrónico, sepárelas con comas.

![Notification recipients][api-management-email-addresses]

Para especificar los usuarios a los que se va a notificar, haga clic en **Agregar destinatario**, active la casilla situada junto a los usuarios a los que se va a notificar y haga clic en **Aceptar**.

> [!NOTE] 
> En la lista solo aparecen los administradores.


Después de configurar los destinatarios de las notificaciones, haga clic en **Guardar** para aplicar los destinatarios de notificación actualizados.

> [!NOTE] 
> Si hay cambios sin guardar y sale de la pestaña **Notificaciones del publicador** , el portal del publicador le avisará.


## <a name="email-templates"></a>Configuración de plantillas de correo electrónico
Administración de API proporciona plantillas de correo electrónico para los mensajes de correo electrónico que se envían durante la administración y el uso del servicio. Se incluyen las siguientes plantillas de correo electrónico.

* Envío de la galería de aplicaciones aprobado
* Carta de despedida del desarrollador
* Notificación de aproximación del límite de cuota del desarrollador
* Invitación a un usuario
* Nuevo comentario agregado a un problema
* Nuevo problema recibido
* Nueva suscripción activada
* Confirmación de suscripción renovada
* Rechazo de la solicitud de suscripción
* Solicitud de suscripción recibida

Estas plantillas se pueden modificar tal como se desee.

Para ver y configurar las plantillas de correo electrónico de la instancia de API Management, haga clic en **Notificaciones** en el menú **API Management** de la izquierda y seleccione la pestaña **Plantillas de correo electrónico**.

![Email templates][api-management-email-templates]

Para ver o modificar una plantilla específica, selecciónela en de la lista desplegable **Plantillas** .

![Email templates list][api-management-email-templates-list]

Cada plantilla de correo electrónico tiene un asunto en texto sin formato y una definición del cuerpo en formato HTML. Cada elemento se puede personalizar según se desee.

![Email template editor][api-management-email-template]

La lista **Parámetros** contiene una lista de parámetros que, al insertarlos en el asunto o en el cuerpo, se reemplazarán por el valor designado cuando se envíe el correo electrónico. Para insertar un parámetro, sitúe el cursor en donde desee que vaya el parámetro y haga clic en la flecha a la izquierda del nombre del parámetro.

Haga clic en **Vista previa** o en **Enviar una prueba** para ver el aspecto que tendrá el correo electrónico o para enviar un correo electrónico de prueba.

> [!NOTE] 
> Los parámetros no se reemplazan por valores reales al obtener la vista previa o enviar una prueba.

Para guardar los cambios efectuados en la plantilla de correo electrónico, haga clic en **Guardar** o, si desea cancelarlos, haga clic en **Cancelar**.
 

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
