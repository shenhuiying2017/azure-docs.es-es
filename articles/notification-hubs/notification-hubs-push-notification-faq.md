<properties
	pageTitle="Centros de notificaciones de Azure - preguntas frecuentes (FAQ)"
	description="Preguntas más frecuentes sobre el diseño y la implementación de soluciones en los Centros de notificaciones"
	services="notification-hubs"
	documentationCenter="mobile"
	authors="wesmc7777"
	manager="erikre"
    keywords="notificación push, notificaciones push, notificaciones push de iOS, notificaciones push de android, inserción de ios, inserción de android"
	editor="" />

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="06/29/2016"
	ms.author="wesmc" />

#Notificaciones push con los Centros de notificaciones de Azure: preguntas más frecuentes

##General
###1\. ¿Cuál es el modelo de precios para los Centros de notificaciones?
Los Centros de notificaciones se ofrecen en tres niveles:

* **Gratis**: obtenga hasta 1 millón de inserciones por suscripción al mes.
* **Básico**: obtenga 10 millones de inserciones por suscripción al mes como línea de base, con opciones de aumento con cuota.
* **Estándar**: obtenga 10 millones de inserciones por suscripción al mes como línea de base, con opciones de aumento con cuota, además de funcionalidades de telemetría enriquecida.

Los detalles más recientes pueden encontrarse en la página [Precios de Bases de datos centrales de notificaciones]. Los precios se establece a nivel de suscripción y se basan en el número de iniciaciones de notificaciones push, por lo que no importa cuántos espacios de nombres o centros de notificaciones haya creado en su suscripción de Azure.

El nivel **Gratuito** se ofrece para fines de desarrollo, sin ninguna garantía de contrato de nivel de servicio (SLA). Aunque este nivel puede ser un buen punto de partida para los que desean explorar las funcionalidades de las notificaciones push a través de los Centros de notificaciones de Azure, probablemente no es la mejor opción para aplicaciones de mediana y gran escala.

Los niveles **Básico** y **Estándar** están disponibles para uso de producción, con las siguientes características clave habilitadas *solamente para el nivel Estándar*:

- *Telemetría enriquecida*: los Centros de notificaciones ofrecen una serie de funcionalidades para exportar los datos de telemetría, así como la información de registro de notificación push para su análisis y visualización sin conexión.
- *Multiinquilinos*: ideal si está creando una aplicación móvil con los Centros de notificaciones para admitir varios inquilinos. Esto le permite establecer las credenciales de servicios de notificación de inserción (PNS) en el nivel de espacio de nombres del centro de notificaciones de la aplicación y, después, separar a los inquilinos ofreciéndoles centros individuales en este espacio de nombres común. Esto facilita el mantenimiento, a la vez que mantiene las claves SAS para enviar y recibir notificaciones push de los Centros de notificaciones de forma separada para cada inquilino, asegurándose con ello de que no hay superposiciones entre inquilinos.
- *Inserción programada*: le permite programar las notificaciones push que se ponen en cola y se envían posteriormente.
- *Importación masiva*: le permite importar registros en masa.

###2\. ¿Cuál es el SLA de los Centros de notificaciones?
Para los niveles **Básico** y **Estándar** de los Centros de notificaciones, garantizamos que, al menos el 99,9 % del tiempo, las aplicaciones que estén configuradas correctamente podrán enviar notificaciones push o realizar operaciones de administración de registros con respecto a un Centro de notificaciones implementado en un nivel compatible. Para más información sobre nuestro SLA, visite la página [Contratos de nivel de servicio].

> [AZURE.NOTE] No hay ninguna garantía de SLA para la autenticación mutua entre el servicio de notificación de plataforma y el dispositivo, ya que los Centros de notificaciones dependen de proveedores externos de plataforma para entregar la notificación push al dispositivo.

###3\. ¿Qué clientes utilizan los Centros de notificaciones?
Tenemos una gran cantidad de clientes que utilizan los Centros de notificaciones. A continuación destacamos algunos:

* Sochi 2014: cientos de grupos de interés, más de 3 millones de dispositivos, más de 150 millones de notificaciones enviadas en 2 semanas. [Caso práctico: Sochi]
* Skanska [(caso práctico: Skanska)]
* Seattle Times [(caso práctico: Seattle Times)]
* Mural.ly [(caso práctico: Mural.ly)]
* 7Digital [(caso práctico: 7Digital)]
* Aplicaciones de Bing: decenas de millones de dispositivos, envío de 3 millones de notificaciones al día

###4\. ¿Cómo puedo actualizar o degradar mis Centros de notificaciones para cambiar el nivel de servicio?
Vaya al [Portal de Azure clásico], haga clic en Bus de servicio, luego en el espacio de nombres y finalmente en el centro de notificaciones. En la pestaña Escalar, podrá cambiar el nivel de servicio de los Centros de notificaciones.

![](./media/notification-hubs-faq/notification-hubs-classic-portal-scale.png)

##Diseño y desarrollo
###1\. ¿Qué plataformas de servicio se admiten?
Ofrecemos SDK y [ejemplos completos] para .NET, Java, PHP, Python y Node.js, de modo que un back-end de aplicación puede configurarse para comunicarse con los Centros de notificaciones mediante cualquiera de estas plataformas. Las API de los Centros de notificaciones se basan en interfaces REST para que pueda elegir comunicarse directamente con estas, si no desea agregar una dependencia adicional. Pueden encontrar más detalles en la página [Cómo utilizar la interfaz REST de los Centros de notificación].

###2\. ¿Qué plataformas de cliente se admiten?
Se admite el envío de notificaciones push a plataformas de [Apple iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [Universal de Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android China (a través de Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push notification-apns-get-started.md) y [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), [aplicaciones de Chrome](notification-hubs-chrome-push-notifications-get-started.md) y [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Para obtener una lista completa de los tutoriales de introducción que abordan el envío de notificaciones push en estas plataformas, consulte nuestra página [Introducción a Centros de notificaciones para aplicaciones iOS].

###3\. ¿Admiten las notificaciones de correo electrónico/web/SMS?
Los Centros de notificaciones están diseñados principalmente para enviar notificaciones a aplicaciones móviles mediante las plataformas mencionadas anteriormente. De momento no proporcionamos la funcionalidad de enviar correo electrónico o alertas de SMS; sin embargo, se pueden integrar plataformas de terceros que proporcionan estas funcionalidades junto con los Centros de notificaciones para enviar notificaciones push nativas mediante las [Aplicaciones móviles de Azure].

Los Centros de notificaciones tampoco proporcionan un servicio de entrega de notificación push en el explorador listo para usarse. Los clientes pueden elegir implementarlo usando SignalR sobre las plataformas de servidor admitidas. Si desea enviar notificaciones a aplicaciones de explorador en el espacio aislado de Chrome, consulte [Introducción a Centros de notificaciones para aplicaciones Chrome].

###4\. ¿Cuál es la relación entre las Aplicaciones móviles de Azure y los Centros de notificaciones de Azure y en qué casos debo utilizar cada uno?
Si tiene un back-end de aplicación móvil existente y solo desea agregar la funcionalidad de enviar notificaciones push, debe utilizar los Centros de notificaciones de Azure. Si desea configurar su back-end de aplicación móvil desde cero, entonces debe considerar utilizar las Aplicaciones móviles de Azure. Una Aplicación móvil de Azure proporciona automáticamente un Centro de notificaciones para que pueda enviar notificaciones push fácilmente desde el back-end de aplicación móvil. Los precios de las Aplicaciones móviles de Azure incluyen los cargos de base para un Centro de notificaciones, y solo tendrá que pagar si supera las inserciones incluidas. Para más detalles sobre los costos puede consultar la página [Precios de servicio de aplicaciones].

###5\. ¿Cuántos dispositivos puedo admitir si envío notificaciones push a través de los Centros de notificaciones?
Consulte la página [Precios de Bases de datos centrales de notificaciones] para más detalles sobre el número de dispositivos compatibles.

Para determinados escenarios, si necesita admitir más de 10.000.000 dispositivos registrados, [póngase en contacto con nosotros](https://azure.microsoft.com/overview/contact-us/) directamente y le ayudaremos a escalar su solución.

###6\. ¿Cuántas notificaciones de inserción puedo enviar?
Dependiendo del nivel seleccionado, Azure realizará un escalado vertical y automático en función del número de notificaciones que fluyan a través del sistema.

>[AZURE.NOTE] El costo de uso general puede crecer en función del número de notificaciones push servidas. Asegúrese de que es consciente de los límites de nivel existentes que se describen en la página [Precios de Bases de datos centrales de notificaciones].

Nuestros clientes ya usan los Centros de notificaciones para enviar millones de notificaciones push al día. No es necesario hacer nada especial para escalar la cobertura de notificaciones push, siempre y cuando esté usando los Centros de notificaciones de Azure.

###7\. ¿Cuánto tiempo pasa desde que las notificaciones push enviadas llegan a mi dispositivo?
Los Centros de notificaciones de Azure procesan por lo menos **el envío de 1 millón de notificaciones push en 1 minuto** en un escenario de uso normal, donde la carga entrante es bastante regular y no tiene picos. Esta velocidad puede variar según el número de etiquetas, la naturaleza de los envíos que llegan y otros factores externos.

Durante el tiempo estimado de entrega, el servicio calcula los destinos por plataforma y enruta mensajes a los servicios de entrega de notificaciones push correspondientes, basándose en las expresiones de etiquetas o en las etiquetas registradas. De aquí en adelante, es responsabilidad de los servicios de las notificaciones de inserción (PNS) enviar la notificación al dispositivo.

Un PNS no garantiza ningún SLA para la entrega de notificaciones; de todas formas, normalmente la gran mayoría de las notificaciones push se entregan a los dispositivos en cuestión de minutos (normalmente en un tiempo máximo de 10 minutos) desde el momento en que se envían a nuestra plataforma. Puede haber algunos valores atípicos que pueden tardar más tiempo.

>[AZURE.NOTE] Los Centros de notificaciones de Azure tienen una directiva para retirar las notificaciones push que no se puedan entregar a los PNS dentro de un plazo de 30 minutos. Este retraso puede ocurrir por una serie de motivos, más comúnmente porque el PNS está limitando su aplicación.

###8\. ¿Hay alguna garantía de latencia?
Debido a la naturaleza de las notificaciones push (la entrega la realiza un servicio externo de notificaciones push específico para la plataforma), no hay ninguna garantía de latencia. Normalmente, la mayoría de las notificaciones push se entregan en un par de minutos.

###9\. ¿Cuáles son las consideraciones que tengo que tener en cuenta a la hora de diseñar una solución con espacios de nombres y Centros de notificaciones?

####Entorno/aplicación móvil

* Debe haber un Centro de notificaciones por entorno y aplicación móvil.
* En un escenario multiinquilino, cada inquilino debe tener un centro independiente.
* Nunca debe compartir el mismo centro de notificaciones entre entornos de producción y de prueba, ya que esto puede causar problemas posteriores al enviar notificaciones. Por ejemplo, Apple ofrece extremos de espacio aislado y de inserción de producción, cada uno con unas credenciales distintas.
* De forma predeterminada, puede enviar notificaciones de prueba a los dispositivos registrados a través del Portal de Azure o el componente de Azure integrado en Visual Studio. El umbral se establece en 10 dispositivos que se seleccionan de forma aleatoria desde el grupo de registro.

>[AZURE.NOTE] Si el grupo se ha configurado originalmente con un certificado de espacio aislado de Apple y, a continuación, se vuelve a configurar para usar un certificado de producción de Apple, los antiguos tokens del dispositivo dejan de ser válidos con el nuevo certificado y la inserción no se realiza correctamente. Es mejor separar los entornos de producción y de prueba y utilizar centros diferentes para entornos diferentes.

####Credenciales PNS:

Cuando una aplicación móvil se registra con un portal para desarrolladores de una plataforma (por ejemplo, Apple, Google, etc.), obtendrá un identificador de aplicación y tokens de seguridad que el back-end de la aplicación necesita proporcionar a los servicios de notificación de inserción de la plataforma para poder enviar notificaciones de inserción a los dispositivos. Estos tokens de seguridad, que pueden ser en forma de certificados (por ejemplo, para Apple iOS o Windows Phone) o de claves de seguridad (Google Android o Windows, entre otros), deben configurarse en los Centros de notificaciones. Esto se hace normalmente en el nivel de concentrador de notificación, pero también puede realizarse en el nivel de espacio de nombres en un escenario de varios inquilinos.

####Espacios de nombres

Los espacios de nombres pueden usarse para la agrupación de implementación. También puede utilizarse para representar todos los Centros de notificaciones para todos los inquilinos de la misma aplicación en el escenario multiinquilino.

####Distribución geográfica

La distribución geográfica no siempre es fundamental en el caso de las notificaciones push. Es necesario recordar que varios servicios de notificación push (como APNS, GCM, etc.), que son los que en última instancia entregan las notificaciones push a los dispositivos, no están distribuidos de manera uniforme.

Si tiene una aplicación que se usa a nivel mundial, puede crear varios centros en distintos espacios de nombres para sacar provecho de la disponibilidad del servicio de los Centros de notificaciones en diferentes regiones de Azure en todo el mundo.

>[AZURE.NOTE] Esto aumentará el costo de administración, especialmente en relación con los registros, por lo que no es muy recomendable y solo debe realizarse si es realmente necesario.

###10\. ¿Debo hacer registros desde el back-end de aplicación o directamente a través de los dispositivos cliente?
Los registros desde el back-end de la aplicación son útiles cuando se tiene que realizar una autenticación de cliente antes de crear el registro, o cuando se tienen etiquetas que el back-end de aplicación tiene que crear o modificar basándose en alguna lógica de aplicación. Para más información, consulte las páginas [Registro desde el backend de la aplicación] y [Administración de registro].

###11\. ¿Cuál es el modelo de seguridad de entrega de las notificaciones push?
Los Centros de notificaciones de Azure usan un modelo de seguridad basado en la [firma de acceso compartido (SAS)](../storage/storage-dotnet-shared-access-signature-part-1.md). Puede utilizar los tokens SAS en el nivel de espacio de nombres raíz o en el nivel detallado de los Centros de notificaciones. Estos tokens de SAS pueden establecerse con distintas reglas de autorización; por ejemplo, permisos de envío de mensajes, permisos de notificaciones de escucha, etc. Encontrará más detalles en el documento [Seguridad].

###12\. ¿Cómo debo controlar las cargas confidenciales en las notificaciones push?
Todas las notificaciones se entregan a los dispositivos mediante los servicios de notificación push (PNS) de las plataformas. Cuando un remitente envía una notificación a los centros de notificaciones de Azure, procesamos y pasamos la notificación a las PNS correspondientes.

Todas las conexiones desde el remitente a los Centros de notificaciones de Azure y a la PNS usan HTTPS.

>[AZURE.NOTE] Los Centros de notificaciones de Azure no registran la carga del mensaje de ninguna manera.

No obstante, para enviar cargas confidenciales, recomendamos un patrón de inserción seguro en el que el remitente envía una notificación "ping" con un identificador de mensaje al dispositivo sin la carga confidencial y, cuando la aplicación en el dispositivo recibe esta carga, llama a una API de aplicación segura directamente para capturar los detalles del mensaje. Una guía sobre cómo implementar el patrón descrito anteriormente está disponible en la página [Inserción segura de los Centros de notificaciones de Azure].

##Operaciones
###1\. ¿Qué es el historial de recuperación ante desastres (DR)?
En nuestro extremo se incluye cobertura de recuperación ante desastres de metadatos (nombre del Centro de notificaciones, cadena de conexión, y otra información crítica). Cuando se desencadena un escenario de recuperación ante desastres, los datos de los registros son el **único segmento** de la infraestructura de los Centros de notificaciones que se perderá. Tiene que implementar una solución para volver a rellenar estos datos en el nuevo centro posterior recuperación.

- *Paso 1*: cree un Centro de notificaciones secundario en otro centro de datos. Puede crearlo sobre la marcha en el momento del evento de recuperación ante desastres, o bien puede crear uno desde cero. No hay mucha diferencia entre una y otra opción porque el aprovisionamiento del Centro de notificaciones es un proceso relativamente rápido de unos pocos segundos. Si tiene uno desde el principio, le protegerá contra la posibilidad de que el evento de recuperación ante desastres afecte a sus funciones de administración, por lo que esta opción es muy recomendable.

- *Paso 2*: hidrate el Centro de notificaciones secundario con los registros del Centro de notificaciones principal. No se recomienda intentar mantener los registros en ambos centros, ni intentar mantenerlos sincronizados a medida que llegan los registros. Esto normalmente no funciona bien debido a la tendencia inherente de los registros a caducar en el extremo del PNS. Los centros de notificaciones se limpian cuando recibimos comentarios del PNS de que los registros han caducado o no son válidos.

Se recomienda usar un back-end de aplicación que:

- Mantiene un conjunto de registros determinado en su extremo para que pueda realizar una inserción masiva en el Centro de notificaciones secundario en caso de recuperación ante desastres

**O bien**

- obtenga un volcado normal de registros desde el centro principal como una copia de seguridad y, a continuación, realice una inserción masiva en el centro de notificaciones secundario.

>[AZURE.NOTE] La funcionalidad de exportación/importación de registros disponible en el nivel Estándar se describe en el documento [Procedimiento: cómo exportar y modificar registros en bloque].

Si no tiene un back-end, cuando se inicia la aplicación en cualquiera de los dispositivos de destino, estos realizarán un nuevo registro en el Centro de notificaciones secundario y, finalmente, el Centro de notificaciones secundario tendrá todos los dispositivos activos registrados.

La desventaja es que habrá un período de tiempo durante el que los dispositivos en los que no se han abierto las aplicaciones, no recibirán notificaciones.

###2\. ¿Hay alguna funcionalidad de registro de auditoría?
Todas las operaciones de administración de los centros de notificaciones van a los registros de operaciones, que se exponen en el [Portal de Azure clásico].

##Supervisión y solución de problemas
###1\. ¿Qué funcionalidades de solución de problemas hay disponibles?
Los Centros de notificaciones de Azure proporcionan varias características para la solución de problemas comunes, especialmente en el escenario muy común que aparece en relación a las notificaciones quitadas. Vea los detalles en este artículo [Centros de notificaciones de Azure: pautas de diagnóstico].

###2\. ¿Qué características de telemetría hay disponibles?
Los Centros de notificaciones Azure permiten ver los datos de telemetría en el [Portal de Azure clásico]. Encontrar detalles de las métricas disponibles en la página [Centros de notificaciones: Metrics].

>[AZURE.NOTE] El éxito de las notificaciones solo significan que las notificaciones push se han entregado al servicio de notificaciones push externo (por ejemplo, APNS de Apple, GCM para Google, etc.). Es el PNS el que entrega la notificación al dispositivos de destino. Normalmente, el PNS no muestra las métricas de entrega a terceros.

También proporciona la funcionalidad de exportar los datos de telemetría mediante programación (en el nivel **Estándar**). Consulte [el ejemplo de métricas de Centros de notificaciones] para más información.

[Portal de Azure clásico]: https://manage.windowsazure.com
[Precios de Bases de datos centrales de notificaciones]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Contratos de nivel de servicio]: http://azure.microsoft.com/support/legal/sla/
[Caso práctico: Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[(caso práctico: Skanska)]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[(caso práctico: Seattle Times)]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[(caso práctico: Mural.ly)]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[(caso práctico: 7Digital)]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[Cómo utilizar la interfaz REST de los Centros de notificación]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Introducción a Centros de notificaciones para aplicaciones iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Introducción a Centros de notificaciones para aplicaciones Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Registro desde el backend de la aplicación]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Administración de registro]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Seguridad]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Inserción segura de los Centros de notificaciones de Azure]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Centros de notificaciones de Azure: pautas de diagnóstico]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Centros de notificaciones: Metrics]: https://msdn.microsoft.com/library/dn458822.aspx
[el ejemplo de métricas de Centros de notificaciones]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[Procedimiento: cómo exportar y modificar registros en bloque]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[ejemplos completos]: https://github.com/Azure/azure-notificationhubs-samples
[Aplicaciones móviles de Azure]: https://azure.microsoft.com/es-ES/services/app-service/mobile/
[Precios de servicio de aplicaciones]: https://azure.microsoft.com/es-ES/pricing/details/app-service/

<!---HONumber=AcomDC_0706_2016-->