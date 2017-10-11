---
title: Centros de notificaciones de Azure
description: "Aprenda a agregar funcionalidades de notificación push con Azure Notification Hubs."
author: ysxu
manager: erikre
editor: 
services: notification-hubs
documentationcenter: 
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 1/17/2017
ms.author: yuaxu
ms.openlocfilehash: a1be0b13cd1feb582a23965df142e44d90ac6851
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="azure-notification-hubs"></a>Centros de notificaciones de Azure
## <a name="overview"></a>Información general
Azure Notification Hubs proporciona un motor de inserción fácil de usar, multiplataforma y escalado horizontalmente. Con una única llamada de API multiplataforma puede enviar fácilmente notificaciones push específicas y personalizadas a cualquier plataforma móvil desde cualquier back-end local o en la nube.

Notification Hubs funciona muy bien tanto para escenarios empresariales como de consumidores. Estos son algunos ejemplos de para qué utilizan los clientes Notification Hubs:

* Enviar notificaciones de noticias de última hora a millones de usuarios con baja latencia.
* Enviar cupones basados en la ubicación a segmentos de usuarios interesados.
* Enviar notificaciones relacionadas con eventos a usuarios o grupos para aplicaciones de medios, deportivas, de finanzas o de juegos.
* Insertar contenido promocional en aplicaciones para ponerse en contacto y comercializar con clientes.
* Informar a los usuarios sobre eventos empresariales como, por ejemplo, nuevos mensajes o elementos de trabajo.
* Enviar códigos para Multi-Factor Authentication.

## <a name="what-are-push-notifications"></a>¿Qué son las notificaciones de inserción?
Las notificaciones push son una forma de comunicación de aplicación a usuario en la que los usuarios de aplicaciones móviles reciben una notificación con determinada información que desean, por lo general, en un cuadro de diálogo o una ventana emergente. Normalmente, los usuarios pueden elegir ver o descartar el mensaje y, si eligen lo primero, se abrirá la aplicación móvil que les ha comunicado la notificación.

Las notificaciones push son vitales para las aplicaciones de consumidor, ya que aumentan el uso y la interacción de las aplicaciones, así como para las aplicaciones empresariales porque comunican información empresarial actualizada. Es la mejor forma de comunicación de aplicación a usuario porque ahorra energía para los dispositivos móviles, es flexible para los remitentes de notificaciones y está disponible cuando las aplicaciones correspondientes no están activas.

Más información acerca de las notificaciones push de algunas plataformas populares:
* [iOS](https://developer.apple.com/notifications/)
* [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
* [Windows](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>Funcionamiento de las notificaciones de inserción
Las notificaciones push se entregan a través de unas infraestructuras específicas para la plataforma llamadas *Sistemas de notificación de plataforma* (PNS). Ofrecen funcionalidades de inserción esenciales para la entrega de un mensaje a un dispositivo con un identificador proporcionado y no tienen ninguna interfaz común. Para enviar una notificación a todos los clientes mediante las versiones de iOS, Android y Windows de una aplicación, el desarrollador debe trabajar con APNS (Apple Push Notification Service), FCM (Firebase Cloud Messaging), WNS (Servicio de notificaciones de Windows) al procesar por lotes los envíos.

A continuación se muestra cómo funciona la inserción, a nivel general:

1. La aplicación cliente decide si desea recibir inserciones, por lo que se pone en contacto con el PNS correspondiente para recuperar su identificador de inserción único y temporal. El tipo de identificador depende del sistema (por ejemplo, WNS tiene URI mientras APNS tiene tokens).
2. La aplicación cliente almacena este identificador en el back-end o el proveedor de la aplicación.
3. Para enviar una notificación push, el back-end de la aplicación se pone en contacto con el PNS a través del identificador para dirigirse a una aplicación cliente específica.
4. El PNS reenvía la notificación al dispositivo que especifica el identificador.

![][0]

## <a name="the-challenges-of-push-notifications"></a>Los desafíos de las notificaciones de inserción
A pesar de que los PNS son muy potentes, de todos modos dejan mucho trabajo al desarrollador de aplicaciones para poder implementar incluso escenarios comunes de notificaciones push, como la difusión o el envío de notificaciones push a usuarios segmentados.

La inserción es una de las características más solicitadas en servicios de dispositivos móviles en la nube, ya que su funcionamiento requiere infraestructuras complejas que no están relacionadas con la lógica de negocios principal de la aplicación. Algunos de los desafíos que presenta la infraestructura son:

* **Dependencia de la plataforma**: 

  * El back-end debe tener una lógica, dependiente de la plataforma, compleja y difícil de mantener para enviar notificaciones a dispositivos de distintas plataformas, ya que los PNS no están unificados.
* **Escala**:

  * Según las directrices de PNS, se deben actualizar los tokens de dispositivo cada vez que se inicia la aplicación. Esto significa que el back-end debe tratar con una gran cantidad de tráfico y acceso de bases de datos solo para mantener actualizados los tokens. Cuando el número de dispositivos aumenta a centenares y miles de millones, la creación y el mantenimiento de esta infraestructura supone un costo enorme.
  * La mayoría de los PNS no son compatibles con la difusión a varios dispositivos. Esto significa que una simple difusión a un millón de dispositivos genera un millón de llamadas a los PNS. El escalado de esta cantidad de tráfico con una latencia mínima no es algo trivial.
* **Enrutamiento**:
  
  * Aunque los PNS proporcionan una manera de enviar mensajes a dispositivos, la mayoría de las notificaciones de aplicaciones se dirigen a usuarios o grupos de interés. Esto significa que el back-end debe mantener un registro para asociar dispositivos a grupos de interés, usuarios, propiedades, etc. Esta sobrecarga se agrega al tiempo de comercialización total y a los costos de mantenimiento de una aplicación.

## <a name="why-use-notification-hubs"></a>¿Por qué usar los Centros de notificaciones?
Notification Hubs elimina todas las complejidades asociadas con la habilitación de una inserción propia. Su infraestructura de notificaciones push multiplataforma y escalada horizontalmente reduce los códigos relativos a la inserción y simplifica el back-end. Con Notification Hubs, los dispositivos solo son responsables de registrar identificadores de PNS con un centro, mientras que el back-end envía mensajes a usuarios o grupos de interés, tal como se muestra en la ilustración siguiente:

![][1]

Notification Hubs es un motor de inserción listo para usar que presenta las siguientes ventajas:

* **Multiplataforma**

  * Compatibilidad para las principales plataformas push incluidos iOS, Android, Windows, Kindle y Baidu.
  * Una interfaz común para insertar en todas las plataformas en formatos específicos de la plataforma o independientes de esta sin ningún tipo de trabajo específico de la plataforma.
  * Administración de controladores de dispositivos en un único lugar.
* **Back-ends cruzados**
  
  * En la nube o local
  * .NET, Node.js, Java, etc.
* **Conjunto completo de patrones de entrega**:

  * *Difusión para una o varias plataformas*: puede difundir al instante a millones de dispositivos entre plataformas con una sola llamada API.
  * *Inserción en dispositivo*: puede destinar las notificaciones a dispositivos individuales.
  * *Inserción en usuario*: las características de etiquetas y plantillas le ayudan a llegar a todos los dispositivos multiplataforma de un usuario.
  * *Inserción en segmento con etiquetas dinámicas*: las característica de etiquetas le ayudan a segmentar dispositivos e insertar en ellos en función de sus necesidades, tanto si va a enviar a un segmento como si lo envía a una expresión de segmentos (p. ej. AND activo reside en Seattle NO es un usuario nuevo). En lugar de limitarse a la publicación-suscripción, puede actualizar las etiquetas de dispositivo en cualquier lugar y en cualquier momento.
  * *Inserción localizada*: la característica de plantillas le ayuda a obtener la localización sin alterar el código de back-end.
  * *Inserción silenciosa*: puede habilitar el modelo de inserción a extracción enviando notificaciones silenciosas a dispositivos y desencadenándolos para que realicen ciertas extracciones o acciones.
  * *Inserción programada*: puede programar el envío de notificaciones en cualquier momento.
  * *Inserción directa*: puede omitir el registro de dispositivos con nuestro servicio y procesar por lotes directamente las inserciones a una lista de identificadores de dispositivo.
  * *Inserción personalizada*: las variables de inserción de dispositivo le ayudan a enviar notificaciones push personalizadas específicas de dispositivo con pares de clave-valor personalizados.
* **Telemetría enriquecida**
  
  * La telemetría de inserción general, dispositivo, error y operación está disponible en Azure Portal y mediante programación.
  * La telemetría por mensaje realiza el seguimiento de cada inserción desde la llamada de la solicitud inicial a nuestro servicio, procesando por lotes correctamente el envío de las inserciones.
  * Platform Notification System Feedback (Comentarios del Sistema de notificación de plataforma) comunica todos los comentarios de los Sistemas de notificación de plataforma para ayudar en la depuración.
* **Escalabilidad** 
  
  * Enviar mensajes rápidos a millones de dispositivos sin tener que volver a diseñar la arquitectura o realizar el particionamiento del dispositivo.
* **Seguridad**

  * Firma de acceso compartido (SAS) o autenticación federada.

## <a name="integration-with-app-service-mobile-apps"></a>Integración con las Aplicaciones móviles del Servicio de aplicaciones
Para facilitar una experiencia perfecta y unificadora entre servicios de Azure, [Aplicaciones móviles del Servicio de aplicaciones] tiene compatibilidad integrada con notificaciones push mediante centros de notificaciones. [Aplicaciones móviles del Servicio de aplicaciones] ofrecen una plataforma de desarrollo de aplicaciones móviles altamente escalable y disponible globalmente para desarrolladores empresariales e integradores de sistemas que proporciona un amplio conjunto de funcionalidades a desarrolladores móviles.

Los desarrolladores de aplicaciones móviles pueden usar centros de notificaciones con el siguiente flujo de trabajo:

1. Recuperar controlador PNS de dispositivo
2. Registrar el dispositivo con Notification Hubs a través de la API adecuada del registro del SDK de cliente de Mobile Apps
   * Tenga en cuenta que las aplicaciones móviles eliminan todas las etiquetas en los registros por motivos de seguridad. Trabaje con centros de notificaciones desde su back-end directamente para asociar etiquetas a dispositivos.
3. Enviar notificaciones desde su back-end de aplicación con los centros de notificaciones

Estas son algunas ventajas para los desarrolladores de esta integración:

* **SKD de cliente de Mobile Apps**: estos SDK multiplataforma ofrecen API simples para el registro y se comunican con el centro de notificaciones vinculado con la aplicación móvil automáticamente. Los desarrolladores no tienen que indagar en las credenciales de los Centros de notificaciones y trabajar con un servicio adicional.

  * *Inserción en usuario*: los SDK etiquetan automáticamente el dispositivo específico con un identificador de usuario autenticado de Mobile Apps para habilitar la inserción en un escenario de usuario.
  * *Inserción en dispositivo*: los SDK utilizan automáticamente el identificador de instalación de Mobile Apps como GUID para registrarse con Notification Hubs, lo que permite que los desarrolladores no tengan que mantener varios GUID de servicio.
* **Modelo de instalación**: Mobile Apps funciona con el modelo de inserción más reciente de Notification Hubs para representar todas las propiedades de inserción asociadas a un dispositivo en una instalación de JSON que se alinea con Servicios de notificaciones de inserción y es sencillo de usar.
* **Flexibilidad**: los desarrolladores siempre pueden elegir trabajar con Notification Hubs directamente, incluso con la integración implementada.
* **Experiencia integrada en [Azure Portal]**: la inserción como funcionalidad se representa visualmente en Mobile Apps y los desarrolladores pueden trabajar fácilmente con el centro de notificaciones asociado a través de Mobile Apps.

## <a name="next-steps"></a>Pasos siguientes
Más información acerca de los Centros de notificaciones en estos temas:

* **[Cómo utilizan los clientes los Notification Hubs]**
* **[Tutoriales y guías sobre los Notification Hubs]**
* **Tutoriales de introducción sobre los Notification Hubs**: [iOS], [Android], [Windows Universal], [Windows Phone], [Kindle], [Xamarin.iOS] y [Xamarin.Android]

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png
[Cómo utilizan los clientes los Notification Hubs]: http://azure.microsoft.com/services/notification-hubs
[Tutoriales y guías sobre los Notification Hubs]: http://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
[Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
[Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
[Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
[Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
[Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
[Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
[Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
[Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
[Aplicaciones móviles del Servicio de aplicaciones]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure Portal]: https://portal.azure.com
[tags]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)
