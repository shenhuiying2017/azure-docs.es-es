<properties 
   pageTitle="Interfaz de usuario de Azure Mobile Engagement: cobertura" 
   description="Información general de la interfaz de usuario para la sección de cobertura de Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="mattgre" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="required" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Azure Mobile Engagement: interfaz de usuario

<div class="dev-center-tutorial-selector sublanding">
<a href="../mobile-engagement-user-interface" title="Introduction">Introducción</a>
<a href="../mobile-engagement-user-interface-navigation" title="Navigation">Navegación</a>
<a href="../mobile-engagement-user-interface-home/" title="Home">Inicio</a>
<a href="../mobile-engagement-user-interface-my-account" title="My Account">Mi cuenta</a>
<a href="../mobile-engagement-user-interface-analytics" title="Analytics">Análisis</a>
<a href="../mobile-engagement-user-interface-monitor" title="Monitor">Supervisión</a>
<a href="../mobile-engagement-user-interface-reach" title="Reach">Cobertura</a>
<a href="../mobile-engagement-user-interface-segments" title="Segments">Segmentos</a>
<a href="../mobile-engagement-user-interface-dashboard" title="Dashboard">Panel</a>
<a href="../mobile-engagement-user-interface-settings" title="Settings">Configuración</a>
</div>

# <a name="Reach">Cobertura</a>
 
La sección de cobertura de la interfaz de usuario es la herramienta de administración de campaña de inserción en la que puede crear/editar/activar/finalizar/supervisar y obtener estadísticas de las campañas de notificaciones de inserción y funciones a las que también se puede acceder a través de la API de cobertura (y algunos elementos de la API de inserción de bajo nivel). Recuerde que tanto si está utilizando las API o la interfaz de usuario, deberá integrar Azure Mobile Engagement y la cobertura en la aplicación para cada plataforma con el SDK para poder utilizar campañas de cobertura.

**Consulte también:** 

-  [Documentación de la API - API de cobertura][Link 4], [Documentación de la API - API de inserción][Link 4], [Guía de solución de problemas - Inserción/cobertura][Link 2]
-  <a href="#ReachCampaign" title="Reach Campaign">Campaña de cobertura</a>, <a href="#ReachCriterion" title="Reach Criterion">criterio de cobertura</a>, <a href="#ReachContent" title="Reach Content">contenido de cobertura</a>
 
## Cuatro tipos de notificaciones de inserción:
1.    Anuncios: le permiten enviar mensajes de publicidad a los usuarios que los redireccionan a otra ubicación dentro de la aplicación o enviarlos a una página web o tienda fuera de la aplicación. 
2.    Sondeos: le permiten reunir información de los usuarios finales formulándoles preguntas.
3.    Inserciones de datos: le permiten enviar un archivo de datos binario o base64. La información contenida en una inserción de datos se envía a la aplicación para modificar la actual experiencia del usuario en la aplicación. La aplicación debe ser capaz de procesar los datos de una inserción de datos.
4.    Mosaicos (solo en Windows Phone): le permiten utilizar el servicio de notificaciones de inserción de Microsoft (MPNS) para enviar la inserción de Windows nativa que contiene datos XML. (Compatible desde la versión 0.9.0. del SDK La carga final de los mosaicos final no puede superar los 32 kilobytes).

**Consulte también:** 

-  [Conceptos - Glosario][Link 6]

## Tres categorías de estadísticas en tiempo real mostradas para cada campaña: 
1.    Insertado: ¿cuántas inserciones se han enviado según los criterios especificados en la campaña? 
2.    Respondido: ¿cuántos usuarios han reaccionado a la notificación abriéndola desde fuera de la aplicación o cerrándola en la aplicación. 
3.    Ejecutados: cuántos usuarios han hecho clic en el vínculo de la notificación para ser redirigidos a una nueva ubicación de la aplicación, a una tienda o a un explorador web. 

> Nota: Hay estadísticas de campaña más detalladas disponibles desde las estadísticas de la API de cobertura

**Consulte también:** 

-  [Conceptos - Glosario][Link 6], [Documentación de la API - API de cobertura - Estadísticas][Link 4]


## Detalles de la campaña:
Puede editar, clonar, eliminar o activar las campañas que no se han activado todavía pasando el ratón sobre sus nombres o puede hacer clic para abrirlas. Se pueden clonar las campañas que ya se han activado pasando el ratón sobre sus nombres o puede hacer clic para abrirlas. Sin embargo, no puede cambiar una campaña una vez activada.
 
![Reach1][18]

## Comentarios sobre la cobertura:
Puede cambiar de la vista de detalles a la vista de estadísticas de una campaña abierta que ya se ha activado y cambiar de la vista simple a la vista avanzada de las estadísticas para ver más información detallada (según los permisos). También puede utilizar la información de comentarios de la cobertura de una campaña anterior como criterios de orientación de una nueva campaña. Las estadísticas de comentarios de cobertura también se pueden recopilar mediante "Estadísticas" de la API de cobertura. También puede personalizar el público de las campañas de inserción en función de las campañas anteriores.


**Consulte también:** 

-  <a href="#ReachCampaign">Documentación de UI - Cobertura - Nueva campaña de inserción</a>, [Documentación de la API - API de cobertura - Estadísticas][Link 4]
![Reach2][19]

## <a name="ReachCampaign">Alcance - Nueva campaña de inserción:</a>
 
Puede utilizar la sección de cobertura de la interfaz de usuario para crear una nueva campaña de inserción con una fórmula compleja proporcionando toda la información que necesita para enviar una notificación de inserción. Las opciones de una campaña de inserción varían ligeramente en función de los cuatro tipos de campaña: Anuncios, sondeos, inserciones de datos y mosaicos (solo en Windows Phone).

**La opción se aplica a:**

- Idiomas:    Todo (anuncios, sondeos, inserciones de datos, mosaicos)
- Campaña:    Todo (anuncios, sondeos, inserciones de datos, mosaicos)
- Notificación:     Anuncios, sondeos
- Contenido:    Único para cada tipo de campaña
- Público:     Todo (anuncios, sondeos, inserciones de datos, mosaicos)
- Período de tiempo:     Anuncios, sondeos, mosaicos
- Prueba:    Todo (anuncios, sondeos, inserciones de datos, mosaicos)
 
![Reach-Campaign1][20]

## Idiomas:
Puede utilizar el menú desplegable de idiomas para enviar una versión diferente de la inserción a los dispositivos que están configurados para utilizar distintos idiomas. De forma predeterminada, todos los dispositivos recibirán la misma inserción independientemente del lenguaje que están configurados para utilizar. Los usuarios con su dispositivo configurado en un idioma diferente recibirán la versión del idioma predeterminado de la inserción. Muchas de las opciones de la campaña de inserción le permiten especificar contenido alternativo para cada uno de los idiomas adicionales que seleccione. 
 
![Reach-Campaign2][21]

**Las diferencias de los idiomas se aplican a:**

- Idiomas:    Se pueden seleccionar idiomas únicos además del idioma predeterminado
- Campaña:    Igual para todos los idiomas
- Notificación:    Único para cada idioma además del idioma predeterminado
- Contenido:    Único para cada idioma además del idioma predeterminado
- Público:     Se puede filtrar por un criterio de idioma independiente
- Período de tiempo:     Igual para todos los idiomas
- Prueba:    Se puede enviar a cada idioma a la vez
 
**Idiomas admitidos**

- Árabe (ar) 
- Búlgaro (bg) 
- Catalán (ca) 
- Chino (zh) 
- Croata (hr) 
- Checo (cs) 
- Danés (da) 
- Neerlandés (Países Bajos) 
- Español (es) 
- Finés (fi) 
- Francés (fr) 
- Alemán (de) 
- Griego (el) 
- Hebreo (he) 
- Hindi (hi) 
- Húngaro (hu) 
- Indonesio (id) 
- Italiano (it) 
- Japonés (ja) 
- Coreano (ko) 
- Letón (lv) 
- Lituano (lt) 
- Malayo (macroidioma) (ms) 
- Noruego Bokmål (nb) 
- Polaco (pl) 
- Portugués (pt) 
- Rumano (ro) 
- Ruso (ru) 
- Serbio (sr) 
- Eslovaco (sk) 
- Esloveno (sl) 
- Español (es) 
- Sueco (sv) 
- Tagalo (tl) 
- Tailandés (th) 
- Turco (tr) 
- Ucraniano (uk) 
- Vietnamita (vi) 
 
## Campaña:
Puede utilizar la sección Campaña para establecer el nombre y la categoría de la campaña, así como si planea pasar por alto la sección de audiencia de una campaña de inserción y enviar esta campaña a través de la API de cobertura (y algunos elementos de la API de inserción de nivel bajo) en su lugar. Las categorías se pueden utilizar con una plantilla de notificación personalizada para controlar las notificaciones de aplicaciones basadas en configuraciones predefinidas. Puede obtener una lista de las "Categorías" existentes mediante la API de cobertura.

> Advertencia: Si utiliza la opción "Omitir audiencia, la inserción se enviará a los usuarios a través de la API" de la sección "Campaña" de una campaña de cobertura, la campaña NO se enviará automáticamente, deberá enviarla de forma manual mediante la API de cobertura.
 
![Reach-Campaign3][22]
 
**La opción se aplica a:**

- Nombre:    Todo
- Category:    Anuncios, sondeos
- Omitir la audiencia, la inserción se enviará a los usuarios a través de la API:    Todo
 
## Notificación:
Puede utilizar la sección de notificación para establecer la configuración básica de la inserción, incluido: El título de la inserción, el mensaje, una imagen de la aplicación o si se pueden pasar por alto. Muchas opciones de notificación son específicas de la plataforma del dispositivo. Puede seleccionar si desea enviar la inserción "en la aplicación", "fuera de la aplicación" o a ambas ubicaciones. (Recuerde que los usuarios pueden participar o no participar en las inserciones fuera de la aplicación a nivel del sistema operativo en sus dispositivos, y Azure Mobile Engagement no podrá invalidar esta configuración. Recuerde también que la API de cobertura maneja inserciones "dentro de la aplicación" y "fuera de la aplicación". La API de inserción puede utilizarse para controlar también inserciones "fuera de la aplicación"). Las inserciones pueden personalizarse con imágenes o contenido HTML, incluidos vínculos profundos para vincular fuera de la aplicación o en otra ubicación de la aplicación (se requieren categorías preventivas del SDK de Android 2.1.0 o posteriores). Puede cambiar el icono o el distintivo de iOS y enviar contenido de texto o web (una ventana emergente con contenido html, un vínculo de URL a otra ubicación dentro o fuera de la aplicación). También puede hacer que dispositivos Android suenen o vibren con la inserción. (Recuerde que necesitará los permisos correctos del SDK en su archivo de manifiesto de Android para hacer sonar o vibrar un dispositivo). Actualmente no hay ningún estándar del sector para tamaños de Android de "Imagen grande", puesto que los tamaños de pantalla son diferentes en cada dispositivo, pero las imágenes de 400 x 100 funcionan en casi cualquier tamaño de pantalla.

## Tipos de entrega:
-    Solo fuera de la aplicación: la notificación se entregará cuando el usuario no utilice la aplicación.
- La notificación solo fuera de la aplicación requiere un certificado de Apple o Google (certificado APN o GCM).
- Solo dentro de la aplicación: La notificación aparece solo cuando se ejecuta la aplicación.
- La notificación utiliza el sistema de entrega Capptain para ponerse en contacto con el usuario. Puede personalizar completamente el diseño y presentación visual de la inserción.
- En cualquier momento: Esta opción garantiza enviar una notificación tanto si la aplicación se está ejecutando como si no.

 
![Reach-Campaign4][23]

**La opción se aplica a:**

- Notificación:     Anuncios, sondeos
 
## Contenido:
Puede utilizar la sección de contenido para modificar el contenido de los anuncios, sondeos, inserciones de datos y mosaicos (solo en Windows Phone). La configuración del contenido de las campañas de inserción es específica del tipo de campaña. 

**Consulte también:**

- <a href="#ReachContent">Documentación de la interfaz de usuario - Cobertura - Insertar contenido</a>
 
![Reach-Campaign5][24]

## Público:
Puede utilizar la sección de audiencia para definir una lista estándar de elementos para limitar su campaña o limitar la campaña en función de criterios personalizados. El conjunto estándar de opciones para limitar la audiencia le permite insertar a los usuarios nuevos o antiguos o solo a los usuarios de inserción nativa. También puede establecer una cuota para limitar el número de usuarios que reciben la inserción. Puede editar manualmente la expresión sobre cómo se filtra su campaña para incluir uno o más criterios para los usuarios de destino. Manualmente puede escribir una expresión de audiencia. Este tipo de expresión debe definir explícitamente la relación entre los criterios. Un criterio se describe mediante un identificador que debe comenzar con una letra mayúscula y no puede contener espacios. La relación entre los criterios se puede describir mediante los operadores 'and' ' o', 'not' y "(", ")". Ejemplo: "Criterio1 o (Criterio1 y no Criterio2)".

> Nota: Con una amplia audiencia incluida en las campañas, el análisis de orientación del servidor puede ralentizarse, especialmente si intenta iniciar varias campañas al mismo tiempo.

- Si es posible, inicie una sola campaña de cada vez.
- Como máximo, inicie solamente cuatro campañas de cada vez.
- Realice la inserción únicamente a sus usuarios activos (casilla de verificación "Atraer solo a los usuarios con los que se pueda contactar mediante inserción nativa" y "Atraer solo a usuarios activos") para que solo se tengan que analizar los usuarios que aún tienen la aplicación instalada y la utilicen.
Una vez definida la audiencia, puede utilizar el botón de simulación para averiguar cuántos usuarios recibirán esta inserción. Esto calculará el número de usuarios conocidos a los que se dirige potencialmente este público (esta es una estimación basada en una muestra aleatoria de usuarios). Tenga en cuenta que los usuarios que han desinstalado la aplicación también forman parte de esta audiencia, pero no se puede contactar con ellos.

**Consulte también:**

- <a href="#ReachCriterion">Documentación de la interfaz de usuario - Cobertura - Nuevo criterio de inserción</a>

![Reach-Campaign6][25]

## Editar expresión
 
![Reach-Campaign7][26]
 
**Limitar la opción de audiencia se aplica a:**

- Haga participar a tan solo un subconjunto de usuarios:    Todo (anuncios, sondeos, inserciones de datos, mosaicos)
- Haga participar a solo los usuarios antiguos:    Todo (anuncios, sondeos, inserciones de datos, mosaicos)
- Haga participar a solo a los usuarios nuevos:    Todo (anuncios, sondeos, inserciones de datos, mosaicos)
- Haga participar solo a los usuarios inactivos:    Anuncios, sondeos, mosaicos
- Haga participar solo a los usuarios activos:    Todo (anuncios, sondeos, inserciones de datos, mosaicos)
- Atraiga a los usuarios con los que se pueda contactar mediante la inserción nativa:     Anuncios, sondeos
 
## Período de tiempo:
Puede utilizar la sección de intervalo de tiempo para establecer cuándo se enviará la inserción o puede dejar el intervalo de tiempo en blanco para iniciar la campaña inmediatamente. Recuerde que utilizando la zona horaria de los usuarios finales puede comenzar la campaña un día antes de lo esperado para los usuarios finales de Asia y enviar lotes pequeños de inserciones a la vez hasta que todas las zonas horarias del mundo coincidan con el intervalo de tiempo establecido para la campaña. Utilizar la zona horaria de los usuarios finales también puede provocar retrasos en las campañas porque tiene que solicitar el tiempo desde el teléfono antes de iniciar la inserción.

> Nota: Sin una fecha de finalización puede almacenar en caché de campañas inserciones localmente y todavía mostrarlos después de completar manualmente las campañas. Para evitar este comportamiento, especifique una hora de finalización para campañas.

**Consulte también:**

- [Guía práctica - Programación][Link 3] 
 
![Reach-Campaign8][27]

**La configuración se aplica a:**

- Período de tiempo:     Anuncios, sondeos, mosaicos
 
## Prueba:
Puede utilizar la sección de prueba para enviar esta inserción a su propio dispositivo de prueba antes de guardar la campaña. Si ha configurado algún idioma personalizado para esta campaña, puede probar la inserción en cada idioma. Puede configurar un dispositivo de prueba desde "Mi cuenta".
> Nota: No se registra ningún dato del servidor cuando utiliza el botón para "probar" inserciones, los datos solo se registran para campañas de inserción real.

**Consulte también:**

- [Documentación de la interfaz de usuario - Mi cuenta][Link 14]
 
![Reach-Campaign9][28]

## <a name="ReachCriterion">Alcance - Nuevo criterio de inserción (para la audiencia de destino)</a>

Orientar su audiencia según criterios específicos mediante el botón "Nuevos criterios" es uno de los conceptos más eficaces de Azure Mobile Engagement, que le ayuda a enviar notificaciones de inserción relevantes a las que responderán los clientes en lugar de enviar spam a todo el mundo. Puede limitar la audiencia basándose en criterios estándar y simular inserciones para determinar cuántas personas recibirán la notificación.

**Consulte también:**

- <a href="#ReachCampaign">Documentación de la interfaz de usuario - Cobertura - Nueva campaña de inserción</a>

## Dentro de los criterios de audiencia se pueden incluir:

- **Notas técnicas: ** Puede orientar en función de la misma información técnica que puede ver en las secciones de análisis y supervisión. **Consulte también:** [Documentación de la interfaz de usuario - Análisis][Link 15], [Documentación de la interfaz de usuario - Supervisión][Link 16]
- **Ubicación:** las aplicaciones que usan "informes de ubicación en tiempo Real" con Geofencing pueden usar la geolocalización como criterio para orientarse a una audiencia desde la ubicación de GPS. La llamada "Informes de ubicación de área diferida" también se puede utilizar para orientarse a una audiencia desde la ubicación del teléfono móvil ("Informes de ubicación en tiempo real" e " Informes de ubicación de área diferida" deben activarse desde el SDK). **Consulte también:**[Documentación del SDK - iOS - Integración][Link 5], [Documentación del SDK - Android - Integración][Link 5]
- **Comentarios de cobertura:** puede tener como destino su audiencia según sus comentarios de notificaciones de cobertura anteriores a través de comentarios del cobertura de anuncios, sondeos e inserciones de datos. Esto le permite orientarse mejor a la audiencia después de dos o tres campañas de cobertura que la primera vez. También puede utilizarse para filtrar los usuarios que ya han recibido una notificación con contenido similar, estableciendo una campaña para NO enviarse a los usuarios que ya han recibido una determinada campaña anterior. Incluso puede excluir a los usuarios incluidos en una campaña específica que está todavía activa de recibir inserciones nuevas. **Consulte también:**<a href="#ReachContent">Documentación de la interfaz de usuario - Cobertura - Insertar contenido</a>
- **Seguimiento de la instalación:** puede realizar un seguimiento de información basada en donde instalaron los usuarios su aplicación. **Consulte también:**[Documentación de interfaz de usuario - Configuración][Link 20]
- **Perfil de usuario:** puede orientarse según la información de usuario estándar y puede orientarse basándose en la información de la aplicación personalizada que ha creado. Aquí se incluye a los usuarios que han iniciado sesión y a los que han respondido preguntas específicas que les ha solicitado definir en la propia aplicación en lugar de tan solo cómo han respondido a campañas anteriores. Todas la información de la aplicación definida para la aplicación aparecen en esta lista.
- Segmentos: También puede orientarse en función de segmentos que ha creado según el comportamiento específico del usuario que contiene varios criterios. Todos los segmentos definidos para la aplicación aparecen en esta lista. **Consulte también:** [Documentación de la interfaz de usuario - Segmentos][Link 18]
- **Información de la aplicación:** es posible crear etiquetas de información de aplicación personalizadas desde "Configuración" para realizar un seguimiento del comportamiento de los usuarios. **Consulte también:**[Documentación de interfaz de usuario - Configuración][Link 20]
## Ejemplo: 
Si desea insertar un anuncio solo en el subconjunto de los usuarios que han realizado una acción de compra en la aplicación.

1. Vaya a la página de configuración de la aplicación, seleccione el menú "Información de la aplicación" y seleccione "Nueva información de la aplicación"
2. Registre una nueva información de aplicación de booleano denominada "inAppPurchase"
3. Haga que la aplicación establezca esta información de aplicación en "true" cuando el usuario realice correctamente una compra en la aplicación (mediante el uso de la función sendAppInfo ("inAppPurchase",...))
4. Si no desea hacerlo desde la aplicación, puede hacerlo desde el backend mediante el uso de la API del dispositivo)
5. A continuación, solo necesita crear el anuncio con un criterio que limite la audiencia a los usuarios que tienen "inAppPurchase" establecido en "true")
 
> Nota: La orientación basada en criterios que no sean etiquetas de información de aplicación requiere que Azure Mobile Engagement recopile información de los dispositivos de los usuarios antes de enviar la inserción para poder provocar un retraso. Las opciones de configuración de inserción complejas (como la actualización de insignias) también pueden retrasar las inserciones. El uso de una campaña de "Monoestable" de la API de inserción es el método de inserción de más rápido absoluto en Azure Mobile Engagement. Utilizar solo etiquetas de información de aplicación como criterios de inserción para una campaña de cobertura (ya sea desde la API de cobertura o la interfaz de usuario) es el siguiente método más rápido, dado que las etiquetas de información de la aplicación se almacenan en el servidor. Usar otros criterios de orientación para una campaña de inserción es el método de inserción más flexible pero más lento, ya que Azure Mobile Engagement tiene que consultar los dispositivos para enviar la campaña.
 
![Reach-Criterion1][29] 

**Las opciones de criterios se aplican a:**

- **Notas técnicas:**     
- Nombre del firmware:    Nombre del firmware:
- Versión del firmware:    Versión del firmware
- Modelo de dispositivo:    Modelo de dispositivo
- Fabricante del dispositivo:    Fabricante del dispositivo
- Versión de la aplicación:    Versión de la aplicación
- Nombre del operador:    Nombre del operador, sin definir
- País del operador:    País del operador, sin definir
- Tipo de red:    Tipo de red
- Configuración regional:    Configuración regional
- Tamaño de pantalla:    Tamaño de pantalla
- **Ubicación**      
- Última área conocida:    País, región, localidad
- Geofencing en tiempo real:    Lista de puntos de interés (nombre, acciones), punto de interés circular (nombre, latitud, longitud, radio en metros)
- **Comentarios sobre la cobertura:**     
- Comentarios de anuncio:    Anuncio, comentarios
- Comentarios de sondeo:    Sondeo, comentarios
- Comentarios de respuesta de sondeo:    Comentarios de respuesta de sondeo, pregunta, opción
- Comentarios de inserción de datos:    Inserción de datos, comentarios
- **Seguimiento de la instalación**     
- Tienda:    Tienda, sin definir
- Origen:    Origen, sin definir
- **Perfil de usuario**     
- Sexo:    hombre o mujer, sin definir
- Fecha de nacimiento:    operador, fecha, sin definir
- Participar:    verdadero o falso, sin definir
- **Información de la aplicación:**      
- Cadena:    Cadena, sin definir
- Fecha:    operador, fecha, sin definir
- Entero:    operador, número, sin definir
- Valor booleano:    verdadero o falso, sin definir
- **Segmento**    
- Nombre de segmentos (de la lista desplegable), exclusión (usuarios de destino que no forman parte de este segmento).

## <a name="ReachContent">Cobertura - Insertar contenido (para cada tipo de campaña)</a>
 
Puede utilizar la sección de contenido de una nueva campaña de cobertura para modificar el contenido de los anuncios, sondeos, inserción de datos y mosaicos (solo en Windows Phone). La configuración del contenido de las campañas de inserción es específica del tipo de campaña. 
 
## Tipos de contenido
- Anuncios
- Sondeos
- Inserciones de datos
- Mosaicos (solo en Windows Phone)
 
## Contenido de anuncios
 
 ![Reach-Content1][30] 

## Elija el tipo de anuncio:
-    Solo notificación: Es una notificación estándar simple. Lo que significa que si un usuario hace clic en él, no aparecerá ninguna vista adicional, pero se producirá solo la acción asociada a él.
-    Anuncio de texto: Es una notificación que compromete al usuario a echar un vistazo a una vista de texto.
-    Anuncio web: Es una notificación que compromete al usuario a echar un vistazo a una vista web.

**Consulte también:**

- [Guía práctica - Anuncios][Link 3] 

**Acerca de los anuncios de visualización web:**

Las apariciones del patrón "{deviceid}" en el código HTML o JavaScript que proporcione aquí se reemplazarán automáticamente por el identificador del dispositivo que muestra el anuncio. Se trata de una manera fácil de recuperar identificadores de dispositivo de Azure Mobile Engagement en un servicio web externo hospedado en su oficina.
Si desea crear una vista web de pantalla completa (sin los botones predeterminados de Acción y Salir proporcionados) puede utilizar las siguientes funciones desde código de JavaScript del anuncio de la vista web: 

-    realice la acción del anuncio: ReachContent.actionContent()
-    salir del anuncio: ReachContent.exitContent()
 
## Elija la acción:

**Acerca de las direcciones URL de la acción:**

Cualquier dirección URL que puede ser interpretada por el sistema operativo de destino de un dispositivo puede utilizarse como una dirección URL de la acción.
Cualquier dirección URL específica que pueda admitir su aplicación (por ejemplo, para hacer saltar a los usuarios a una pantalla concreta) también puede utilizarse como una dirección URL de acción.
Cada repetición del patrón {deviceid} se reemplaza automáticamente por el identificador del dispositivo que realiza la acción. Esto puede utilizarse para recuperar fácilmente los identificadores de dispositivo Azure Mobile Engagement a través de un servicio web externo hospedado en su área de operaciones.

- **Acciones de Android + iOS**
    - Abrir una página web
    - http://[dominio del sitio web] 
    - Ejemplo:http://www.azure.com
    - Enviar un correo electrónico
    - mailto:[destinatario del correo electrónico]?subject=[asunto]&body=[mensaje] 
    - Ejemplo:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Enviar un SMS
    - sms:[número de teléfono] 
    - Ejemplo:sms:2125551212
    - Marcar un número de teléfono
    - Tel:[número de teléfono] 
    - Ejemplo:tel:2125551212
- **Acciones solo para Android**
    - Descargar una aplicación de Play Store
    - market://details?id=[paquete de aplicación] 
    - Ejemplo:market://details?id=com.microsoft.office.word
    - Iniciar una búsqueda localizada geográficamente
    - geo:0,0?q=[consulta de búsqueda] 
    - Ejemplo:geo:0,0?q=starbucks,paris
- **Acciones solo para iOS**
    - Descargar una aplicación de la App Store
    - http://itunes.apple.com/[country]/app/[app name]/id[app id]?mt=8 
    - Ejemplo:http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
    - Acciones de Windows
    - Abrir una página web
    - http://[dominio del sitio web] 
    - Ejemplo:http://www.azure.com
    - Enviar un correo electrónico
    - mailto:[destinatario del correo electrónico]?subject=[asunto]&body=[mensaje] 
    - Ejemplo:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Enviar un SMS (requiere la aplicación Skype)
    - sms:[número de teléfono] 
    - Ejemplo:sms:2125551212
    - Marcar un número de teléfono (requiere la aplicación Skype)
    - tel:[número de teléfono] 
    - Ejemplo:tel:2125551212
    - Descargar una aplicación de Play Store
    - ms-windows-store:PDP?PFN=[ID del paquete de aplicación] 
    - Ejemplo:ms-windows-store:PDP?PFN=4d91298a-07cb-40fb-aecc-4cb5615d53c1
    - Iniciar una búsqueda en Mapas de Bing
    - bingmaps:?q=[consulta de búsqueda] 
    - Ejemplo:bingmaps:?q=starbucks,paris
    - Utilice un esquema personalizado
    - [esquema personalizado]://[parámetros de esquema personalizado]
    - Ejemplo:myCustomProtocol://myCustomParams
    - Utilizar datos de paquete (se necesita una aplicación de la tienda para leer la extensión)
    - [carpeta][datos].[extensión] 
    - Ejemplo:myfolderdata.txt
 
## Crear una dirección URL de seguimiento:
-    Consulte la sección "Configuración" de <Documentación de la interfaz de usuario> para obtener instrucciones sobre la creación de una dirección URL de seguimiento que permita a los usuarios descargar una de las otras aplicaciones.
 
## Definir los textos del anuncio
Rellene el título, el contenido y los textos del anuncio. 
Puede dirigirse a una audiencia de una campaña futura basándose en los comentarios de la cobertura sobre cómo respondieron los usuarios a esta campaña. La orientación a la audiencia puede basarse en los comentarios de si solo se ha insertado esta campaña, respondido, ejecutada o terminado.

**Consulte también:**
- <a href="#ReachCriterion">Documentación de la interfaz de usuario - Cobertura - Nuevo criterio de inserción</a>

## Contenido de sondeos
 
![Reach-Content2][31] 
Rellene el título, la descripción y los textos de los botones del anuncio. 
A continuación, agregue preguntas y opciones para las respuestas a sus preguntas.
Puede dirigirse a una audiencia de una campaña futura basándose en los comentarios de la cobertura sobre cómo respondieron los usuarios a esta campaña. La orientación a la audiencia puede basarse en si solo se ha insertado, respondido, ejecutado o terminado esta campaña. La orientación de la audiencia también puede basarse en los comentarios de respuesta de sondeos, donde la pregunta y respuesta se utilizan como criterios.

**Consulte también:**

- <a href="#ReachCriterion">Documentación de la interfaz de usuario - Cobertura - Nuevo criterio de inserción</a>
 
## Contenido de las inserciones de datos
 
![Reach-Content3][32] 

## Elija el tipo de datos
- Texto
- Datos binarios
- Datos de Base64

## Defina el contenido de los datos
- Si ha seleccionado insertar datos de texto, copie y pegue el texto en el cuadro "content".
- Si ha seleccionado insertar datos binarios o base64, use el botón "cargar el archivo" para cargar el archivo.
- Puede dirigirse a una audiencia de una campaña futura basándose en los comentarios de la cobertura sobre cómo respondieron los usuarios a esta campaña. La orientación a la audiencia puede basarse en si solo se ha insertado, respondido, ejecutado o terminado esta campaña.

**Consulte también:**

- <a href="#ReachCriterion">Documentación de la interfaz de usuario - Cobertura - Nuevo criterio de inserción</a>

## Contenido de los mosaicos (solo en Windows Phone)

![Reach-Content4][33]

## Defina el contenido de su mosaico
La carga de mosaicos es el texto que se mostrará en el mosaico de la aplicación en los dispositivos Windows Phone.
La inserción de un mosaico es la versión del servicio de notificaciones de inserción de Microsoft (MPNS) de una inserción nativa para Windows Phone. El tipo de inserción de mosaico es el único tipo de inserción que no tiene una respuesta y, por tanto, la audiencia de las campañas futuras no se puede integrar en los resultados de una campaña de inserción de mosaico. 

**Consulte también:**

- [Documentación de la API - API de cobertura - Inserción nativa][Link 4]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

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
