<properties 
   pageTitle="Guía de solución de problemas de Azure Mobile Engagement" 
   description="Guía de solución de problemas de Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Azure Mobile Engagement: Guía de solución de problemas

## Introducción

Las siguientes guías de solución de problemas tratan los problemas más comunes de Azure Mobile Engagement, con la excepción de las preguntas sobre la [facturación][Link 11] y la asistencia para el registro en la [vista previa][Link 7]. Si no puede resolver el problema con estas guías de solución de problemas, puede abrir una solicitud de servicio de Azure (si tiene un [contrato de soporte técnico de Azure](http://azure.microsoft.com/support/options/)) o puede solicitar asistencia en el [foro de MSDN][Link 8]. Asegúrese de incluir toda la información de solución de problemas de <a href="#SR" title="SR">Información de SR</a> al abrir una solicitud de servicio para ayudar al soporte técnico a investigar el problema.

## <a name="#ANALYTICS">Análisis, supervisión, segmentación y paneles</a>

Problemas con cómo reúne Azure Mobile Engagement información acerca de sus aplicaciones, dispositivos y usuarios.

**Lista de síntomas:**

1. <a href="#ANALYTICS1">Información no encontrada o retrasada</a> 
2. <a href="#ANALYTICS2">No puede buscar los elementos de la interfaz de usuario</a>
3. <a href="#ANALYTICS3">Solución de problemas de bloqueos</a>
 
<a name="#ANALYTICS1">**Síntoma**:
1.    Información no encontrada o retrasada:</a>

- La información se retrasa en aparecer en el análisis, la segmentación o el panel.
- Falta información en la supervisión.
- Falta información de análisis, de la segmentación o del panel.
- Alcanzar límites de segmentación.

**Causas:**

- Puede utilizar la API de análisis, API de supervisión y la API de segmentos para ver si los datos que faltan en la interfaz de usuario están visibles a través de las API.
- Si el SDK de Azure Mobile Engagement no está integrado correctamente en la aplicación no podrá ver la información de análisis, segmentación, supervisión o paneles.
- Los segmentos no se pueden cambiar una vez creados, solo se pueden "clonar" (copiar) o "destruir" (eliminar).
Los segmentos solo pueden contener 10 criterios.
- La mejor manera de probar la información que falta en la supervisión (configurar un dispositivo de prueba, desinstalar y/o volver a instalar la aplicación en el dispositivo de prueba).
- La información se actualiza cada 24 horas para analizarla, segmentarla o para los paneles.
- Es posible que la información de los segmentos nuevos no se muestre hasta 24 horas después de que se creen (incluso si el segmento se basa en información anterior).
- Filtrar los datos de análisis en la interfaz de usuario mostrará todos los ejemplos de este tipo, independientemente de la versión de la aplicación (por ejemplo, los "bloqueos" filtrados por nombre se mostrarán desde la versión 1 y 2 de la aplicación).
- El período de tiempo de análisis se basa en la fecha de la configuración de dispositivo de los usuarios, por lo que un usuario cuyo teléfono tiene la fecha establecida incorrectamente podría aparecer en el período de tiempo incorrecto.
- No se registra ningún dato del servidor cuando utiliza el botón para "probar" inserciones, los datos solo se registran para campañas de inserción real.

**Consulte también:** 

- [Guía de solución de problemas: SDK][Link 2], [Documentación de la API][Link 4], [Documentación de la interfaz de usuario - Segmentos][Link 1]

<a name="#ANALYTICS2">**Síntoma**:
2. No puede buscar los elementos de la interfaz de usuario:</a>

- No se pueden crear segmentos basados en determinados criterios de etiqueta de información de la aplicación personalizada o integrada.
- No se puede encontrar determinados criterios de etiquetas de información de aplicación personalizada o integrada en análisis, supervisión o paneles.
- No se pueden interpretar los datos de análisis, supervisión, segmentación o paneles.

**Causas:**

- Algunos elementos integrados y etiquetas de información de aplicación solo están disponibles como criterios de inserción, pero puede que no se agreguen a un segmento o no estén visibles desde el análisis, la supervisión o el panel. 
- Para los elementos integrados y las etiquetas de información de aplicación que no se pueden agregar a un segmento, necesitará configurar la lista de criterios de destinatarios en cada campaña para realizar la misma función que la orientación en función de un segmento.
- Consulte los menús contextuales en las secciones de análisis, supervisión, segmentación y paneles de la interfaz de usuario de Azure Mobile Engagement para obtener más ayuda e información práctica.

**Consulte también:**

- [Documentación de la interfaz de usuario - Cobertura - Nuevo criterio de inserción para la audiencia de destino][Link 1]
 
<a name="#ANALYTICS3">**Síntoma**:
3. Solución de problemas de bloqueos:</a>

- Bloqueos de la aplicación que aparecen en el panel, supervisión o análisis.

**Causas:**

- Para solucionar problemas de bloqueos de aplicaciones vistos en el análisis, la supervisión o el panel, asegúrese de comprobar las notas de la versión para obtener información sobre problemas conocidos con las versiones anteriores del SDK.
- Para solucionar más bloqueos de la aplicación, realice un evento desde un dispositivo de prueba con la aplicación instalada y buscar el identificador del dispositivo en la sección "Monitor - eventos" de la interfaz de usuario de Azure Mobile Engagement. A continuación, realice el evento que causa que la aplicación se bloquee y busque información adicional en la sección "Supervisión - Bloqueo" de la interfaz de usuario de Azure Mobile Engagement. 

**Consulte también:**

- [Conceptos: preguntas más frecuentes][Link 6], [Conceptos - Glosario][Link 6], [Documentación de la interfaz de usuario][Link 1], [Documentación del SDK - Notas de la versión][Link 5], [Documentación del SDK - Guía de actualización][Link 5], [Guía práctica - Registro del ID del dispositivo][Link 3]

## <a name="#APIS">API</a>

Problemas con cómo interactúan los administradores con Azure Mobile Engagement a través de las API.

**Lista de síntomas:**

1. <a href="#APIS1">Problemas de sintaxis</a>
2. <a href="#APIS2">No se puede usar la API para realizar la misma acción disponible en la interfaz de usuario de Azure Mobile Engagement</a>
3. <a href="#APIS3">Mensajes de error</a>
4. <a href="#APIS4">Errores silenciosos</a>
 
<a name="#APIS1">**Síntoma**:
1. Problemas de sintaxis:</a>

- Errores de sintaxis mediante la API (o un comportamiento inesperado).

**Causas:**

- Problemas de sintaxis:
    - Asegúrese de comprobar la sintaxis de la API específica que se utiliza para confirmar que la opción está disponible.
    - Un problema común con el uso de la API consiste en confundir la API de cobertura y la API de inserción (la mayoría de las tareas debe realizarse con la API de cobertura en lugar de la API de inserción). 
    - Otro problema común con la integración del SDK y el uso de la API es confundir la clave del SDK y la clave de la API.
    - Las secuencias de comandos que se conectan a las API necesitan enviar datos al menos cada 10 minutos o la conexión agotará el tiempo de espera (especialmente frecuente en secuencias de comandos de API de supervisión que escuchan datos). Para evitar los tiempos de espera, haga que la secuencia de comandos envíe un ping XMPP cada 10 minutos para mantener la sesión activa con el servidor.

**Consulte también:**
 
- [Conceptos - Glosario][Link 6], [Documentación de la API][Link 4], [información del protocolo XMPP]( http://xmpp.org/extensions/xep-0199.html)
 
<a name="#APIS2">**Síntoma**:
2. No se puede usar la API para realizar la misma acción disponible en la interfaz de usuario de Azure Mobile Engagement:</a>

- Una acción que funciona desde la interfaz de usuario de Azure Mobile Engagement no funciona desde la API de Azure Mobile Engagement relacionada.

**Causas:**

- Confirmar que puede realizar la misma acción desde la interfaz de usuario de Azure Mobile Engagement muestra que ha integrado correctamente esta función de Azure Mobile Engagement con el SDK.

**Consulte también:**
 
- [Documentación de la interfaz de usuario][Link 1]
 
<a name="#APIS3">**Síntoma:**
3. Mensajes de error:</a>

- Códigos de error mediante la API que se muestra en el tiempo de ejecución o en los registros.

**Causas:**

- A continuación se muestra una lista compuesta de números de códigos de estado de API comunes de referencia y de solución de problemas preliminar:

        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response's body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently "in progress", campaign must be "in progress" and the campaign's property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn't retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504         The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

**Consulte también:**

- [Documentación de API: para errores detallados en cada API específica][Link 4]
 
<a name="#APIS4">**Síntoma:**
4. Errores silenciosos:</a>

- Se produce un error en la acción de la API sin que se muestre ningún mensaje de error en el tiempo de ejecución o en los registros.

**Causas:**

- Muchos elementos se deshabilitarán en la interfaz de usuario de Azure Mobile Engagement si no se integran correctamente, pero se producirá un error silenciosamente a través de la API, por lo tanto, no olvide probar la misma funcionalidad desde la interfaz de usuario para ver si funciona.
- Azure Mobile Engagement y muchas funciones avanzadas de Azure Mobile Engagement que está intentando usar necesitan integrarse individualmente en su aplicación con el SDK en pasos distintos para poder utilizarlas.

**Consulte también:**

- [Guía de solución de problemas: SDK][Link 2], [Documentación del SDK][Link 5]
 
## <a name="#PUSH">Inserción/cobertura</a>

Problemas con cómo envía información a sus usuarios Azure Mobile Engagement.
 
**Lista de síntomas:**

1. <a href="#PUSH1">Errores de inserción</a>
2. <a href="#PUSH2">Inserción de problemas de pruebas</a>
3. <a href="#PUSH3">Problemas de personalización de inserción</a>
4. <a href="#PUSH4">Problemas de destinatarios de inserción</a>
5. <a href="#PUSH5">Programación de la inserción</a>
 
<a name="#PUSH1">**Síntoma**:
1. Errores de inserción:</a>

- Las inserciones no funcionan (en la aplicación, fuera de la aplicación, o en ambos ámbitos).

**Causas:**

- Errores de inserción:
    - Muchas veces, un error de inserción es una indicación de que Azure Mobile Engagement, la cobertura u otra función avanzada de Azure Mobile Engagement no está integrado correctamente o que se requiere una actualización en el SDK para solucionar un problema conocido con una nueva plataforma de sistema operativo o del dispositivo.
    - Pruebe simplemente una inserción en la aplicación y una inserción fuera de la aplicación para determinar si algo es un problema dentro o fuera de la aplicación.
    - Realice pruebas desde dentro de la interfaz de usuario y la API como paso de solución de problemas para ver qué información de error adicional está disponible en ambos lugares.
    - Las inserciones fuera de la aplicación no funcionarán a menos que Azure Mobile Engagement y la cobertura están integrados en el SDK.
    - Las inserciones fuera de la aplicación no funcionarán si los certificados no son válidos o está usando PROD frente a DEV correctamente (únicamente en iOS).
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

**Consulte también:**

- [Guía de solución de problemas: SDK][Link 2], [Guía de solución de problemas - Inserción][Link 2], [Documentación del SDK - iOS - Cómo preparar la aplicación para las notificaciones de inserción de Apple][Link 5]
 
<a name="#PUSH2">**Síntoma**:
2. Problemas de pruebas de inserción:</a>

- Las inserciones pueden enviarse a un dispositivo específico en función de un identificador de dispositivo.

**Causas:**

- Los dispositivos de prueba se configuran de manera diferente para cada plataforma, pero provocar un evento en la aplicación en un dispositivo de prueba y buscar el identificador del dispositivo en el portal debería funcionar para encontrar el identificador del dispositivo para todas las plataformas.
- Los dispositivo de prueba funcionan de manera diferente con IDFA frente a IDFV (únicamente en iOS).

**Consulte también:**

- [Documentación de la interfaz de usuario - Cobertura][Link 1]
 
<a name="#PUSH3">**Síntoma**:
3. Problemas de personalización de inserción:</a>

- El elemento de contenido de inserción avanzada no funciona (distintivo, anillo, vibración, imagen, etc.).
- No funcionan los vínculos de inserciones (fuera de la aplicación, en la aplicación, a un sitio web, a una ubicación de la aplicación).
- Las estadísticas de inserción muestran que no se ha enviado una inserción a tantas personas como se esperaba (demasiadas o no suficientes).
- Inserción duplicada y recibida dos veces.
- No se puede registrar el dispositivo de prueba para inserciones de Azure Mobile Engagement (con su propia aplicación de producción o desarrollo).

**Causas:**

- Para vincular a una ubicación específica de la aplicación se requieren "categorías" (solo Android).
- Los servidores de imágenes externas tienen que poder usar HTTP "GET" y "HEAD" para que funcionen inserciones generales (solo Android).
- En el código, puede deshabilitar al agente de Azure Mobile Engagement cuando se abre el teclado y hacer que el código vuelva a activar al agente de Azure Mobile Engagement una vez cerrado el teclado para que el teclado no afecte al aspecto de su notificación (únicamente en iOS).
- Algunos elementos no funcionan en las simulaciones de prueba, sino solo las campañas reales (distintivo, anillo, vibración, imagen, etc.).
- No hay datos en el servidor registrados cuando usa el botón para "probar" inserciones. Los datos solo se registran para campañas de inserción real.
- Para ayudar a aislar el problema, solucione problemas con: prueba, simular y una campaña real, ya que cada uno de ellos funciona de forma ligeramente diferente.

**Consulte también:**

- [Guía práctica - Inserción][Link 3], [Guía de solución de problemas - Inserción][Link 2], [Información del protocolo HTTP]( http://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html)
 
<a name="#PUSH4">**Síntoma**:
4. Problemas de destinatarios de inserción:</a>

- El destino integrado no funciona según lo esperado.
- La orientación de la etiqueta de información de la aplicación no funciona como se esperaba.
- La orientación de geolocalización no funcionan según lo esperado.
- Las opciones de idioma no funcionan según lo esperado.

**Causas:**

- Orientación de la inserción:
    - Asegúrese de que ha cargado etiquetas de información de aplicación a través de la interfaz de usuario o la API de Azure Mobile Engagement.
    - La limitación de la velocidad de inserción o la cuota de inserción en el nivel de la aplicación o la limitación de la audiencia en el nivel de campaña puede evitar que una persona reciba una inserción específica, incluso si cumple sus otros criterios de orientación. 
    - Establecer un "idioma" es diferente de orientar en función del país o la configuración regional, que también es diferente a orientar según la geolocalización basada en una ubicación de teléfono o de GPS.
    - El mensaje en el "idioma predeterminado" se envía a los clientes que no tienen su dispositivo establecido en uno de los idiomas alternativos que especifica.

**Consulte también:**

- [Documentación de la interfaz de usuario - Cobertura][Link 1], [Documentación de interfaz de usuario - Configuración][Link 1], [Documentación de la API - Cobertura][Link 4], [Documentación de la API - Inserción][Link 4], [Documentación de la API - Dispositivo][Link 4]
 
<a name="#PUSH5">**Síntoma**:
5. Programación de la inserción:</a>

- La programación de la inserción no funciona como se esperaba (envío demasiado tiempo o retrasado).

**Causas:**

- Las zonas horarias puede generar problemas con la programación, especialmente cuando se usa la zona horaria de los usuarios finales.
- Las funciones avanzadas de inserción pueden retrasar las inserciones.
- La orientación basada en la configuración del teléfono (en lugar de en etiquetas de información de la aplicación) puede retrasar las inserciones debido a que Azure Mobile Engagement puede tener que solicitar datos del teléfono en tiempo real antes de enviar una inserción.
- Las campañas creadas sin una fecha de finalización almacenan la inserción de forma local en el dispositivo y la muestran la próxima vez que se abre la aplicación incluso si la campaña ha finalizado manualmente.
- Iniciar más de una campaña al mismo tiempo puede provocar que se tarde más tiempo en analizar su base de usuarios (intente iniciar solo una campaña a la vez con un máximo de cuatro, y efectúe la orientación solamente a los usuarios activos para que los usuarios antiguos no se tengan que analizar).
- Si utiliza la opción "Omitir audiencia, la inserción se enviará a los usuarios a través de la API" de la sección "Campaña" de una campaña de cobertura, la campaña NO se enviará automáticamente, deberá enviarla de forma manual mediante la API de cobertura.
- Si usa una categoría personalizada en cobertura para mostrar las notificaciones de la aplicación, deberá seguir el ciclo de vida correcto de una notificación, o puede que la notificación no se borre cuando el usuario la descarte.

**Consulte también:**

- [Guía práctica - Programación][Link 3][Documentación de la interfaz de usuario - Cobertura - Nueva campaña de inserción][Link 1]
 
## <a name="#SERVICE">Servicio</a>

Problemas con la ejecución de Azure Mobile Engagement.

**Lista de síntomas:**

1. <a href="#SERVICE1">Interrupciones del servicio</a>
2. <a href="#SERVICE2">Problemas de conectividad e información incorrecta</a>
3. <a href="#SERVICE3">Solicitud de función</a>
 
<a name="#SERVICE1">**Síntoma**:
1. Interrupciones del servicio:</a>

**Causas:**

- Los problemas que parecen deberse a interrupciones del servicio de Azure Mobile Engagement pueden deberse a diversos problemas:
    - Problemas aislados que originalmente parecen sistémicos de todo Azure Mobile Engagement
    - Problemas conocidos causados por interrupciones de los servidores (no siempre se muestran en el estado del servidor):
o    Retrasos de la programación, errores de orientación, problemas de actualización de distintivos, detención de la recopilación de estadísticas, detención del funcionamiento de las API, imposibilidad de crear nuevas aplicaciones o usuarios, errores de DNS y errores de tiempo de espera en la interfaz de usuario, en la API o en las aplicaciones de un dispositivo.
1.    Interrupciones de dependencia de nube
<Estado del servicio Azure ><Estado de los servicios Web de Amazon (AWS)>
2.    Interrupciones de dependencia de los servicios de notificación de inserción (PNS)
<Google - Servicio><Apple - Servicio><Android - Google GCM><Android - Amazon ADM><Apple - APNS><Windows Phone - WNS><Windows Phone - MPNS><Windows - WNS>
3.    Interrupciones de la tienda de aplicaciones
<GooglePlay><iTunes><Tienda de Windows Phone><Tienda Windows>

    - Interrupciones de dependencia de nube
[Estado del servicio de Azure]( http://azure.microsoft.com/status/), [Estado de los servicios Web de Amazon (AWS)]( http://status.aws.amazon.com/) 
    - Interrupciones de dependencia de los servicios de notificación de inserción (PNS)
[Google - Servicio](http://www.google.com/appsstatus#hl=en&v=status), [Apple - Servicio]( http://www.apple.com/support/systemstatus/), [Android - Google GCM]( http://developer.android.com/google/gcm/index.html), [Android - Amazon ADM]( https://developer.amazon.com/appsandservices/apis/engage/device-messaging), [Apple - APNS]( https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html), [Windows Phone - WNS](http://msdn.microsoft.com/library/windows/apps/hh465407.aspx), [Windows Phone - MPNS](http://msdn.microsoft.com/library/windows/apps/ff402558(v=vs.105).aspx), [Windows - WNS](https://developer.windows.com/)
    - Interrupciones de la tienda de aplicaciones
[GooglePlay](https://play.google.com/), [iTunes](http://www.apple.com/itunes/charts/), [Tienda de Windows Phone](http://www.windowsphone.com/), [Tienda de Windows](http://windows.microsoft.com/)

*Para comprobar si el problema es sistémico puede probar la misma función desde otro:*

    - Azure Mobile Engagement integrated application
    - Test device
    - Test device OS version
    - Campaign
    - Administrator user account
    - Browser (IE, Firefox, Chrome, etc.)
    - Computer
*Para probar si el problema solo afecta a la interfaz de usuario o a la API:*

    - Test the same function from both the Azure Mobile Engagement UI and the Azure Mobile Engagement API's.

[Documentación de la API][Link 4], [Documentación de la interfaz de usuario][Link 1]
	
*Para probar si el problema está relacionado con la red de telefonía móvil:*

    - Test while connected to the Internet via WIFI and while connected via your 3G cellular phone network.
    - Confirm that your firewall is not blocking any of the Azure Mobile Engagement IP Addresses or Ports.

*Para probar si el problema está relacionado con el dispositivo:*

    - Test if your Device is able to connect to Azure Mobile Engagement with another Azure Mobile Engagement integrated app.
    - Test that you can generate Events, Jobs, and Crashes from your phone that can be seen in the Azure Mobile Engagement UI. 
    - Test if you can send push notifications from the Azure Mobile Engagement UI to your device based on its Device ID. 
[Documentación de interfaz de usuario - Configuración][Link 1]

*Para probar si el problema está relacionado con la aplicación:*

    - Install and test your application from an emulator instead of from a physical device:
        - Download and use Android SDK (includes an Android Emulator)
        - Download and use Apple Xcode (includes an iOS Simulator)
        - Download and use Windows Phone SDK (includes a Windows Phone 7, 8 and 8.1 Emulators)

Para probar si el problema está relacionado con las actualizaciones del sistema operativo de los dispositivos del usuario final, que requieren una actualización SDK para resolver:

    - Test your application on different devices with different versions of the OS.
    - Confirm that you are using the most recent version of the SDK.
[Guía de solución de problemas: SDK][Link 2]
 
<a name="#SERVICE2">**Síntoma**:
2. Problemas de conectividad e información incorrecta:</a>

- Problemas para iniciar sesión en la interfaz de usuario de Azure Mobile Engagement.
- Errores de conexión con la API de Azure Mobile Engagement.
- Problemas al cargar etiquetas de información de aplicación a través de la API del dispositivo.
- Problemas de descarga de registros o datos exportados desde Azure Mobile Engagement.
- Información incorrecta que se muestra en la interfaz de usuario de Azure Mobile Engagement.
- Información incorrecta que se muestra en los registros de Azure Mobile Engagement.

**Causas:**

- Para los problemas de conectividad con Azure Mobile Engagement:
    - Confirme que su cuenta de usuario tiene permisos suficientes para realizar la tarea.
    - Confirme que el problema no se limita a un equipo o a la red local.
    - Confirme que el servicio de Azure Mobile Engagement no tiene interrupciones registradas.
    - Confirme que los archivos de la etiqueta de información de la aplicación siguen todas estas reglas:
        - Use únicamente el juego de caracteres UTF8 (no se admite el juego de caracteres ANSI).
        - Use una coma "," como carácter separador (puede abrir una solicitud de servicio para solicitar cambiar el carácter separador de .csv de una coma "," a otro carácter como un punto y coma ";").
        - Utilice mayúsculas para los valores booleanos "TRUE" y "FALSE".
        - Use un archivo que de tamaño inferior al tamaño máximo de archivo de 35 MB.

**Consulte también:**

[Documentación de la API][Link 4][Documentación de interfaz de usuario: página principal][Link 1]
 
<a name="#SERVICE3">**Síntoma**:
3. Solicitudes de función:</a>

- La función que desea utilizar no parece existir todavía en Azure Mobile Engagement.

**Causas:**

Para sugerir una nueva función para Azure Mobile Engagement que todavía no existe:
    - Abra una solicitud de servicio de Azure Mobile Engagement con tantos detalles como sea posible sobre qué función nueva que le gustaría ver en Azure Mobile Engagement.
 
## <a name="#SDK">SDK</a>

Problemas con el modo en que Azure Mobile Engagement se integra en la aplicación.

**Lista de síntomas:**

1. <a href="#SDK1">Problemas con el SDK de Azure Mobile Engagement detectados mediante un error en otra área de la aplicación</a>
2. <a href="#SDK2">Problemas de codificación avanzados</a>
3. <a href="#SDK3">Problemas de bloqueo de aplicaciones</a>
4. <a href="#SDK4">Errores de carga de la tienda de aplicaciones</a>
 
<a name="#SDK1">**Síntoma**:
1. Problemas con el SDK de Azure Mobile Engagement detectados mediante un error en otra área de la aplicación:</a>

- Error de colección de datos de interfaz de usuario (en el análisis, supervisión, segmentación o paneles).
- Errores de inserción (las inserciones no funcionan en la aplicación, fuera de la aplicación o en ambos).
- Fallos de función avanzados (no funcionan el seguimiento, la geolocalización o las inserciones específicas de plataforma).
- Errores de la API (las API fallan a menudo de manera silenciosa sin que se muestren mensajes de error).
- Errores del servicio (ninguno de los Azure Mobile Engagement funciona para su aplicación).

**Causas:**

- La mayoría de errores que necesitan resolverse con el SDK de Azure Mobile Engagement se descubrirán mediante un fallo en su aplicación (por ejemplo, un error de recopilación de datos de interfaz de usuario, un error de inserción, un error de función avanzada, un error de API, bloqueos de aplicación o una interrupción del servicio aparente).  
- Si una función determinada de Azure Mobile Engagement nunca ha funcionado en la aplicación antes, deberá completar la integración. 
- Si una función determinada de Azure Mobile Engagement funcionaba y se detuvo, puede que necesite actualizar a la última versión con el SDK de Azure Mobile Engagement. Recuerde que hay una versión diferente del SDK de Azure Mobile Engagement para cada plataforma compatible con Azure Mobile Engagement (Android, iOS, Web, Windows y Windows Phone).

*Integración con el SDK:* 

- Azure Mobile Engagement no integrado correctamente en el SDK (análisis).
- Cobertura no integrada correctamente en SDK (inserciones dentro y fuera de la aplicación).
- Certificado caducado o PROD frente a DEV incorrecto (únicamente iOS).
- GCM o ADM incorrectamente integrado en el SDK (solo en Android - Inserciones específicas del servicio).
- Seguimiento incorrectamente integrado en el SDK (instalar el seguimiento de la tienda).
- Ubicación diferida o ubicación de GPS incorrectamente integrada en el SDK (orientación mediante ubicación geográfica).
[Documentación del SDK - Guías de integración][Link 5], [Guía de solución de problemas - Inserción][Link 2]

*Actualización del SDK:*

- Debe actualizar el SDK para resolver problemas con versiones anteriores del SDK (a menudo relacionados con las versiones más recientes del sistema operativo del dispositivo).
- Desinstale todas las versiones anteriores de la aplicación del dispositivo y vuelva a instalar la versión más reciente de la aplicación, vuelva a registrar el identificador de dispositivo desde la interfaz de usuario de Azure Mobile Engagement para confirmar que el dispositivo está utilizando la versión más reciente de la aplicación.
[Documentación del SDK - Notas de la versión](http://go.microsoft.com/fwlink/?LinkId= 525554), [Documentación del SDK - Guías de actualización](http://go.microsoft.com/fwlink/?LinkId= 525554), [Documentación de interfaz de usuario - Configuración][Link 1]

*Otros errores del SDK:*

- Los errores en las secciones de código relacionados con Azure Mobile Engagement pueden provocar que este no funcione.
- Los errores en el manifiesto de la aplicación "AndroidManifest.xml" pueden provocar que Azure Mobile Engagement no funcione (solo en Android).
- Un problema común con la integración de SDK y 
- el uso de la API es confundir la clave de SDK y la clave de la API.
[Conceptos - Glosario][Link 6]
 
<a name="#SDK2">**Síntoma**:
2. Problemas de codificación avanzados:</a>

-  Un código específico de plataforma no relacionado directamente con Azure Mobile Engagement puede causar problemas en Windows Phone, iOS y Android.

**Causas:**

- Muchos problemas de codificación avanzada con Azure Mobile Engagement están provocados por códigos específicos de plataforma escritos incorrectamente no relacionados directamente con Azure Mobile Engagement. Deberá consultar la documentación específica de la plataforma para la que está desarrollando además de la documentación de Azure Mobile Engagement (Android, iOS, Web, Windows y Windows Phone).
- No configurar correctamente las "categorías" impide la vinculación desde una notificación a otra ubicación dentro o fuera de la aplicación (solo Android). 
- No establecer "UIKit.framework" en "opcional" en el código de iOS provoca que se muestre un "Error de símbolo no encontrado" o bloqueos en los dispositivos iOS antiguos (únicamente en iOS).
- Los certificados caducados o que no usan la versión de DEV o PROD correctamente causan errores de inserción (únicamente iOS).
- Existen algunas limitaciones inherentes a una plataforma que Azure Mobile Engagement no puede controlar (por ejemplo, cómo funciona el centro del sistema para las inserciones fuera de la aplicación en iOS y Android).
- Azure Mobile Engagement publica una lista completa de los paquetes internos utilizados por Azure Mobile Engagement para iOS y Android a modo de referencia. Tenga en cuenta que algunas funciones de Azure Mobile Engagement son específicas de la plataforma (Android, iOS, Web, Windows y Windows Phone).
- Los SDK de cada plataforma se escribe en los lenguajes de programación siguientes:
    -     SDK de Android escrito en Java
    -     SDK de iOS escrito en C objetivo
    -     SDK web escrito en JavaScript
    -     SDK de Windows escrito en C# y JavaScript
    -     SDK de Windows Phone escrito en C# y JavaScript

**Consulte también:**

 - [Guía de solución de problemas - Inserción][Link 2], [Documentación del SDK - Notas de la versión][Link 5], [Documentación del SDK - Guía de actualización][Link 5], [Documentación del SDK - Android - Información general de documentación técnica de Azure Mobile Engagement][Link 5], [Documentación del SDK - IOS - Información general de documentación técnica de Azure Mobile Engagement][Link 5], [Documentación del SDK - iOS - Cómo preparar la aplicación para las notificaciones de inserción de Apple][Link 5], [Programador de Android](https://developer.android.com/), [programador de iOS](https://developer.apple.com/), [programador de Windows](https://developer.windows.com/) 
 
<a name="#SDK3">**Síntoma**:
3.    Problemas de bloqueo de aplicaciones</a>

- La aplicación se bloquea en el dispositivo de los usuarios finales.

**Causas:**

- La información del bloqueo puede verse en la interfaz de usuario del análisis o la API del análisis.
- Puede encontrar el identificador del dispositivo de su dispositivo de prueba y realizar la misma acción que provocó que la aplicación se bloquee para un usuario final para ayudar a identificar la causa de su bloqueo.
- Los problemas conocidos con el SDK de Azure Mobile Engagement que provocan que las aplicaciones se bloqueen a veces se resuelven con la actualización a la versión más reciente del SDK, así que asegúrese de comprobar las notas de la versión de la plataforma cuando investigue los bloqueos.

**Consulte también:**

- [Conceptos: preguntas más frecuentes][Link 6], [Conceptos - Glosario][Link 6], [Documentación de la API - API de análisis - Bloqueos][Link 4], [Documentación de la IU - Análisis - Bloqueos][Link 1], [Documentación de interfaz de usuario - Configuración][Link 1], [Documentación del SDK - Notas de la versión][Link 5], [Documentación del SDK - Guía de actualización][Link 5]

<a name="#SDK4">**Síntoma**:
4. Fallos de carga de la tienda de aplicaciones</a>

- Errores relacionados con la carga de la versión más reciente de su aplicación a iTunes, GooglePlay, la tienda de Windows o de Windows Phone.

**Causas:**

- Las tiendas de aplicaciones a veces bloquean aplicaciones con ciertas funciones habilitadas (la tienda iTunes impide el uso de IDFV en las aplicaciones de la tienda y la tienda GooglePlay impide el uso compartido de información de la aplicación entre las aplicaciones). 
- Asegúrese de comprobar las notas de la versión acerca de la plataforma y la versión actual del SDK si tiene dificultades para cargar una aplicación en la tienda.

**Consulte también:**

- , [Documentación del SDK - Notas de la versión][Link 5], [Documentación del SDK - Guía de actualización][Link 5] 

## <a name="#SR">Información de solución de problemas de SR</a>

Cuando abra cualquier solicitud de servicio de Azure Mobile Engagement, proporcione la siguiente información:
 
**ID: Proporcionar los identificadores aplicables relacionados con su problema:**

    - App ID
    - Campaign ID
    - Device ID
    - User ID
    - Username
    - App Info Tag
 
**Errores: Proporcione cualquier información de error aplicable relacionada con su problema:**

    - The name of the API or UI section where the issue occurs
    - The text of any error message you receive
    - The results of any tests you have performed from the troubleshooting guides
 
-    [Guías de solución de problemas](http://go.microsoft.com/fwlink/?LinkId=524382)


**Código: Proporcione la información de codificación aplicable relacionada con su problema:**

    - The SDK version and platform of your app (Android SDK 2.4.1, iOS 1.16.2, etc.)
    - The download location of your production app (or the APK/TGZ files of your development app)
    - The "AndroidManifest.xml" and/or any code snippet from your app related to Azure Mobile Engagement (for advanced troubleshooting)

<!--Link references-->
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-how-tos-first-push/
[Link 28]: ../mobile-engagement-how-tos-test-campaign/
[Link 29]: ../mobile-engagement-how-tos-personalize-push/
[Link 30]: ../mobile-engagement-how-tos-differentiate-push/
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign/
[Link 32]: ../mobile-engagement-how-tos-text-view/
[Link 33]: ../mobile-engagement-how-tos-web-view/

<!--HONumber=47-->
