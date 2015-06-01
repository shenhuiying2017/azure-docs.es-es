<properties 
   pageTitle="Guía de solución de problemas de Azure Mobile Engagement - Inserción/Cobertura" 
   description="Solución de problemas de interacción de usuario y notificación en Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="piyushjo"/>


# Guía de solución de problemas de inserción y cobertura

Los siguientes son posibles problemas que pueden producirse con cómo Azure Mobile Engagement envía información a los usuarios.
 
## Errores de inserción

### Problema
- Las inserciones no funcionan (en la aplicación, fuera de la aplicación, o en ambos ámbitos).

### Causas
- Muchas veces, un error de inserción es una indicación de que Azure Mobile Engagement, la cobertura u otra función avanzada de Azure Mobile Engagement no está integrado correctamente o que se requiere una actualización en el SDK para solucionar un problema conocido con una nueva plataforma de sistema operativo o del dispositivo.
- Pruebe simplemente una inserción en la aplicación y una inserción fuera de la aplicación para determinar si algo es un problema dentro o fuera de la aplicación.
- Realice pruebas desde dentro de la interfaz de usuario y la API como paso de solución de problemas para ver qué información de error adicional está disponible en ambos lugares.
- Las inserciones fuera de la aplicación no funcionarán a menos que Azure Mobile Engagement y la cobertura están integrados en el SDK.
- Las inserciones fuera de la aplicación no funcionarán si los certificados no son válidos o está usando PROD frente a DEV correctamente (únicamente en iOS). (**Nota:** es posible que las notificaciones de inserción "fuera de la aplicación" no se entreguen a iOS si tiene las versiones de desarrollo (DEV) y producción (PROD) de la aplicación instaladas en el mismo dispositivo, ya que Apple puede invalidar el token de seguridad asociado con el certificado. Para resolver este problema, desinstale las versiones DEV y PROD de la aplicación y vuelva a instalar solo una versión en el dispositivo.)
- Los recuentos de inserción fuera de la aplicación se controlan de forma distinta en diferentes plataformas (iOS muestra menos información que Android si las inserciones nativas están deshabilitadas en un dispositivo, la API puede proporcionar más información que la interfaz de usuario en las estadísticas de inserción).
- Las inserciones fuera de la aplicación pueden ser bloqueadas por los clientes a nivel de sistema operativo (iOS y Android).
- Las inserciones fuera de la aplicación se mostrarán como deshabilitadas en la interfaz de usuario de Azure Mobile Engagement si no están integradas correctamente, pero pueden producir un error silenciosamente a través de la API.
- Las inserciones dentro de la aplicación no funcionarán a menos que Azure Mobile Engagement y la cobertura están integrados en el SDK.
- Las inserciones GCM y ADM no funcionarán a menos que Azure Mobile Engagement y el servidor específico estén integrados en el SDK (solo Android).
- Las inserciones dentro y fuera de la aplicación deben probarse por separado para determinar si es un problema de inserción o de cobertura.
- Las inserciones en la aplicación requieren que la aplicación esté abierta a recibirse.
- Las inserciones en la aplicación a menudo están configuradas para ser filtradas por una etiqueta de información de aplicación de participación o no participación.
- Si usa una categoría personalizada en cobertura para mostrar las notificaciones de la aplicación, deberá seguir el ciclo de vida correcto de la notificación, o bien puede que la notificación no se borre cuando el usuario la descarte.
- Si inicia una campaña sin fecha final y un dispositivo recibe la notificación de la aplicación pero no la muestra todavía, el usuario seguirá recibiendo la notificación la próxima vez que inicie sesión en la aplicación, incluso si termina la campaña manualmente.
- Para los problemas con la API de inserción, confirme que realmente desea utilizar la API de inserción en lugar de la API de cobertura (ya que esta se usa con más frecuencia) y que no se confunda los parámetros "carga" y "notificador".
- Pruebe su campaña de inserción con ambos dispositivos conectados a través de Wi-Fi y 3G para eliminar la conexión de red como posible fuente de problemas.

### Consulte también

- [Guía de solución de problemas - SDK][Link 25], [Guía de solución de problemas - Inserción][Link 23], [Documentación del SDK - iOS - Cómo preparar la aplicación para las notificaciones de inserción de Apple][Link 5]
 
## Pruebas de inserción

### Problema
- Las inserciones pueden enviarse a un dispositivo específico en función de un identificador de dispositivo.

### Causas

- Los dispositivos de prueba se configuran de manera diferente para cada plataforma, pero provocar un evento en la aplicación en un dispositivo de prueba y buscar el identificador del dispositivo en el portal debería funcionar para encontrar el identificador del dispositivo para todas las plataformas.
- Los dispositivos de prueba funcionan de manera diferente con IDFA frente a IDFV (únicamente en iOS).

### Consulte también

- [Documentación de la interfaz de usuario - Cobertura][Link 17]
 
## Personalización de inserción

### Problema
- El elemento de contenido de inserción avanzada no funciona (distintivo, anillo, vibración, imagen, etc.).
- No funcionan los vínculos de inserciones (fuera de la aplicación, en la aplicación, a un sitio web, a una ubicación de la aplicación).
- Las estadísticas de inserción muestran que no se ha enviado una inserción a tantas personas como se esperaba (demasiadas o no suficientes).
- Inserción duplicada y recibida dos veces.
- No se puede registrar el dispositivo de prueba para inserciones de Azure Mobile Engagement (con su propia aplicación de producción o desarrollo).

### Causas

- Para vincular a una ubicación específica de la aplicación se requieren "categorías" (solo Android).
- Esquemas de vinculación profundos para redirigir a los usuarios a una ubicación alternativa después de hacer clic en una notificación de inserción que necesite crearse en y ser administrada por la aplicación y el sistema operativo del dispositivo, no por Mobile Engagement directamente. (**Nota:** las notificaciones "fuera de la aplicación" no se pueden vincular directamente a ubicaciones de la aplicación con iOS de la misma manera que con Android.)
- Los servidores de imágenes externas tienen que poder usar HTTP "GET" y "HEAD" para que funcionen inserciones generales (solo Android).
- En el código, puede deshabilitar al agente de Azure Mobile Engagement cuando se abre el teclado y hacer que el código vuelva a activar al agente de Azure Mobile Engagement una vez cerrado el teclado para que el teclado no afecte al aspecto de su notificación (únicamente en iOS).
- Algunos elementos no funcionan en las simulaciones de prueba, sino solo las campañas reales (distintivo, anillo, vibración, imagen, etc.).
- No hay datos en el servidor registrados cuando usa el botón para "probar" inserciones. Los datos solo se registran para campañas de inserción real.
- Para ayudar a aislar el problema, solucione problemas con: prueba, simular y una campaña real, ya que cada uno de ellos funciona de forma ligeramente diferente.
- El período de tiempo en el que las campañas "en la aplicación" y "en cualquier momento" están programadas para ejecutarse puede afectar a los números de entrega, ya que una campaña solo se entregará a los usuarios "en la aplicación" mientras se ejecuta la campaña (y los usuarios que tienen la configuración del dispositivo establecida para recibir notificaciones "fuera de la aplicación").
- Las diferencias entre cómo controlan Android y iOS las notificaciones "fuera de la aplicación" hace difícil comparar directamente las estadísticas de inserción entre la versión iOS y Android de la aplicación. Android proporciona más información de notificación a nivel de sistema operativo que iOS. Android informa cuando se recibe, se elimina o se hace clic en una notificación nativa en el centro de notificaciones, pero iOS no indica esta información a menos que se haga clic en la notificación. 
- La razón principal por la cual los números de "inserciones" y los números de "entregas" son diferentes en las campañas de cobertura es que las notificaciones "en la aplicación" y "fuera de la aplicación" se contabilizan de manera diferente. Las notificaciones "en la aplicación" las controla Mobile Engagement, pero las notificaciones "fuera de la aplicación" las controla el centro de notificaciones en el sistema operativo del dispositivo.

### Consulte también

- [Guía práctica - Primera inserción][Link 27], [Guía de solución de problemas - Inserción][Link 23], [Información del protocolo HTTP](http://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html)
- [Desarrolladores de Apple - Información de la interfaz de usuario de "No participar"](http://support.apple.com/kb/HT3576), [Desarrolladores de Apple - Información de desarrollo de "No participar"](https://developer.apple.com/notifications/), [para desarrolladores de Apple - "descartar" solución de problemas de](https://developer.apple.com/library/ios/technotes/tn2265/), [Desarrolladores de Apple - Esquema de dirección URL](https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/Introduction/Introduction.html) 
- [Desarrolladores de Android - Información de la interfaz de usuario de "No participar"](http://developer.android.com/about/versions/jelly-bean.html), [Desarrolladores de Android - Patrones "No participar"](http://developer.android.com/design/patterns/notifications.html), [Desarrolladores de Android - Notificador "No participar"](http://developer.android.com/guide/topics/ui/notifiers/notifications.html), [Desarrolladores de Android - Vista previa de "No participar"](https://developer.android.com/preview/notifications.html), [Desarrolladores de Android - Referencia de "No participar"](http://developer.android.com/reference/android/app/Notification.html), [Desarrolladores de Android - Esquema de filtro de intento](http://developer.android.com/guide/components/intents-filters.html#DataTest), [Desarrolladores de Android - Guía de referencia del filtro de intento](http://developer.android.com/reference/android/content/Intent.html#toUri)

## Orientación de la inserción

### Problema
- El destino integrado no funciona según lo esperado.
- La orientación de la etiqueta de información de la aplicación no funciona como se esperaba.
- La orientación de geolocalización no funcionan según lo esperado.
- Las opciones de idioma no funcionan según lo esperado.

### Causas

- Asegúrese de que ha cargado etiquetas de información de aplicación a través de la interfaz de usuario o la API de Azure Mobile Engagement.
- La limitación de la velocidad de inserción o la cuota de inserción en el nivel de la aplicación o la limitación de la audiencia en el nivel de campaña puede evitar que una persona reciba una inserción específica, incluso si cumple sus otros criterios de orientación. 
- Establecer un "idioma" es diferente de orientar en función del país o la configuración regional, que también es diferente a orientar según la geolocalización basada en una ubicación de teléfono o de GPS.
- El mensaje en el "idioma predeterminado" se envía a los clientes que no tienen su dispositivo establecido en uno de los idiomas alternativos que especifica.

### Consulte también

- [Documentación de la interfaz de usuario - Cobertura][Link 17], [Documentación de la interfaz de usuario - Configuración][Link 20], [Documentación de la API - Cobertura][Link 4], [Documentación de la API - Inserción][Link 4], [Documentación de la API - Dispositivo][Link 4]
 
## Programación de la inserción

### Problema
- La programación de la inserción no funciona como se esperaba (envío demasiado tiempo o retrasado).

### Causas

- Las zonas horarias puede generar problemas con la programación, especialmente cuando se usa la zona horaria de los usuarios finales.
- Las funciones avanzadas de inserción pueden retrasar las inserciones.
- La orientación basada en la configuración del teléfono (en lugar de en etiquetas de información de la aplicación) puede retrasar las inserciones debido a que Azure Mobile Engagement puede tener que solicitar datos del teléfono en tiempo real antes de enviar una inserción.
- Las campañas creadas sin una fecha de finalización almacenan la inserción de forma local en el dispositivo y la muestran la próxima vez que se abre la aplicación incluso si la campaña ha finalizado manualmente.
- Iniciar más de una campaña al mismo tiempo puede provocar que se tarde más tiempo en analizar su base de usuarios (intente iniciar solo una campaña a la vez con un máximo de cuatro, y efectúe la orientación solamente a los usuarios activos para que los usuarios antiguos no se tengan que analizar).
- Si utiliza la opción "Omitir audiencia, la inserción se enviará a los usuarios a través de la API" de la sección "Campaña" de una campaña de cobertura, la campaña NO se enviará automáticamente, deberá enviarla de forma manual mediante la API de cobertura.
- Si usa una categoría personalizada en cobertura para mostrar las notificaciones de la aplicación, deberá seguir el ciclo de vida correcto de una notificación, o puede que la notificación no se borre cuando el usuario la descarte.

### Consulte también

- [Guía práctica de cobertura - Programar campañas][Link 3], [Documentación de interfaz de usuario - Cobertura - Nueva campaña de inserción][Link 27]
 
<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

<!--HONumber=54-->