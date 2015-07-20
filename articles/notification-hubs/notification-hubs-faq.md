<properties 
	pageTitle="Centros de notificaciones de Azure - preguntas frecuentes (FAQ)" 
	description="Preguntas más frecuentes sobre el diseño y la implementación de soluciones en los Centros de notificaciones" 
	services="notification-hubs" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="piyushjo" />

#Centros de notificaciones de Azure - preguntas frecuentes (FAQ)

##General
###1. ¿Cuál es el precio de los Centros de notificaciones?
Los Centros de notificaciones se ofrecen en tres niveles: *Gratis*, *Básico* y *Estándar*. Más información aquí: [Precios de los Centros de notificaciones]. Los precios se cargarán en el nivel de suscripción y se basan en el número de inserciones, por lo que no importa cuántos centros de espacios de nombres o de notificación tenga. El nivel gratuito se ofrece para fines de desarrollo, sin ninguna garantía de contrato de nivel de servicio (SLA). Los niveles básico y estándar están disponibles para uso de producción, y las siguientes características clave solo está habilitadas para el nivel estándar:

- *Difusión*: el nivel básico limita el número de etiquetas del centro de notificaciones a 3000 (se aplica a más de cinco dispositivos). Si tiene un tamaño de audiencia de más de 3000, deberá cambiarse al nivel estándar. 
- *Telemetría completa*: el nivel básico no permite exportar los datos de telemetría ni de registros. Si necesita poder exportar datos de telemetría para verlos y analizarlos sin conexión, deberá cambiarse al nivel estándar. 
- *Multiempresa*: si está creando una aplicación móvil con los Centros de notificaciones para admitir varios inquilinos, deberá considerar cambiarse al nivel Estándar. Esto le permite establecer las credenciales de servicios de notificación de inserción (PNS) en el nivel de espacio de nombres del centro de notificaciones de la aplicación y, después, separar a los inquilinos ofreciéndoles centros individuales en este espacio de nombres común. Esto facilita el mantenimiento, a la vez que mantiene las claves SAS para enviar y recibir notificaciones de los Centros de notificaciones de forma separada para cada inquilino para asegurarse de que no hay superposiciones entre inquilinos. 

###2. ¿Qué es el SLA?
Para los niveles básico y estándar de los Centros de notificaciones, garantizamos que, al menos el 99,9 % del tiempo, las aplicaciones configuradas correctamente puedan enviar notificaciones o realizar operaciones de administración de registros con respecto a un centro de notificaciones implementado en el centro de notificaciones de nivel básico o estándar. Para obtener más información sobre nuestro SLA, visite la página de SLA aquí: [SLA de Centros de notificaciones]. Tenga en cuenta que no hay ninguna garantía de SLA para la autenticación mutua entre el servicio de notificación de plataforma y el dispositivo, ya que los Centros de notificaciones dependen de proveedores externos de plataforma para entregar la notificación al dispositivo.
 
###3. ¿Qué clientes utilizan los Centros de notificaciones?
Tenemos numerosos clientes que utilizan los Centros de notificaciones. A continuación destacamos algunos:

* Sochi 2014: cientos de grupos de interés, más de 3 millones de dispositivos, más de 150 millones de notificaciones enviadas en 2 semanas. [Caso práctico: Sochi]
* Skanska [(caso práctico: Skanska)]
* Seattle Times [(caso práctico: Seattle Times)]
* Mural.ly [(caso práctico: Mural.ly)]
* 7Digital [(caso práctico: 7Digital)]
* Aplicaciones de Bing: decenas de millones de dispositivos, envío de 3 millones de notificaciones al día
 
##Diseño y desarrollo
###1. ¿Qué plataformas de servicio admiten?
Ofrecemos SDK y muestras para .NET, Java, PHP, Python y Node.js, de modo que un backend de aplicación puede configurarse para comunicarse con los Centros de notificaciones mediante cualquiera de estas plataformas. Las API de los Centros de notificaciones se basan en la interfaz REST para que pueda elegir para comunicarse directamente con estas. Más información aquí: [API de REST de Centros de notificaciones]
 
###2. ¿Qué plataformas de dispositivos admiten?
Se admite el envío de notificaciones a aplicaciones de Apple iOS, Android, universales de Windows y Windows Phone, Kindle, Android China (a través de Baidu), Xamarin (iOS y Android) y Chrome. Aquí puede encontrar tutoriales paso a paso para estas plataformas: [Tutoriales de introducción a los Centros de notificaciones].
 
###3. ¿Admiten las notificaciones de correo electrónico/web/SMS?
Los Centros de notificaciones están diseñados principalmente para enviar notificaciones a aplicaciones móviles mediante las plataformas mencionadas anteriormente. No proporcionamos la capacidad de enviar correo electrónico o SMS; sin embargo, se pueden integrar plataformas de terceros que proporcionan estas capacidades junto con los Centros de notificaciones para enviar notificaciones de inserción nativa mediante los Servicios móviles de Azure. Por ejemplo, este tutorial trata sobre cómo enviar notificaciones SMS mediante Servicios móviles de Azure [Envío de SMS con Servicios móviles]. Tampoco proporcionamos una notificación push en explorador lista para usar. Los clientes pueden elegir implementarla mediante SignalR. También proporcionamos un tutorial para enviar notificaciones de inserción a aplicaciones de Chrome que funcionan en el explorador de Google Chrome. Vea este [tutorial de aplicaciones de Chrome].
 
###4. ¿Cuál es la relación entre los Servicios móviles de Azure y los Centros de notificaciones de Azure en qué casos debo utilizar cada uno? 
Si tiene un back-end de aplicación móvil existente y desea agregar la capacidad de enviar notificaciones de inserción, debe utilizar los Centros de notificaciones de Azure. Si desea configurar su back-end de aplicación móvil desde cero, debe considerar utilizar los Servicios móviles de Azure. Un servicio móvil de Azure proporciona automáticamente un centro de notificaciones para que pueda enviar notificaciones de inserción fácilmente desde el back-end de aplicación móvil. Los precios de los Servicios móviles de Azure incluyen los cargos de base por un centro de notificaciones, y solo paga cuando supera las inserciones incluidas. Más información aquí: [Precios de Servicios móviles]
 
###5. ¿Cuántos dispositivos admiten?
En los niveles estándar y básico, no se exige ningún límite en cuanto al número de dispositivos activos que pueden recibir notificaciones. Más información aquí: [Precios de los Centros de notificaciones].
 
###6. ¿Cuántas notificaciones de inserción puedo enviar? 
Los clientes usan los Centros de notificaciones de Azure para enviar millones de notificaciones de inserción al día. No tiene que hacer nada más para escalar los Centros de notificaciones. Escalamos vertical y automáticamente en función del número de notificaciones que fluyen a través del sistema. Tenga en cuenta que los precios cambian según las notificaciones de inserción servidas.
 
###7. ¿Cuánto tiempo pasa hasta que las notificaciones llegan a mi dispositivo?
Los Centros de notificaciones de Azure procesan al menos 1 millón de envíos en 1 minuto en un escenario de uso normal, donde la carga entrante es bastante coherente y no tiene picos. Esta velocidad puede variar según el número de etiquetas, la naturaleza de los envíos entrantes, etc. En esta duración podemos calcular los destinos por plataforma y enrutar mensajes a los servicios de notificaciones push correspondientes basándose en las expresiones de etiquetas o en las etiquetas registradas. De aquí en adelante, es responsabilidad de los servicios de las notificaciones de inserción (PNS) enviar la notificación al dispositivo. Los PNS no garantizan los SLA para entregar notificaciones; sin embargo, normalmente la gran mayoría de los mensajes se entregan a los dispositivos en cuestión de minutos (< 10 minutos) desde el momento en que se envían a nuestra plataforma. Puede haber algunos valores atípicos que pueden tardar más tiempo. Los Centros de notificaciones de Azure también tienen una directiva en su lugar para quitar las notificaciones que no pueden entregarse a los PNS en 30 minutos. Este retraso puede ocurrir por una serie de motivos, más comúnmente porque el PNS está limitando su aplicación.
 
###8. ¿Hay alguna garantía de latencia?
Debido a la naturaleza de las notificaciones de inserción que se entregan por un servicio externo de notificaciones de inserción específico de una plataforma, no hay ninguna garantía de latencia. Normalmente, la mayoría de las notificaciones se entregan en unos minutos.
 
###9. ¿Cuáles son las consideraciones que debemos tener en cuenta para elegir el diseño de una solución con espacios de nombres y centros de notificaciones? 
*Aplicación móvil y entorno*: debe haber un centro de notificaciones por entorno y aplicación móvil. En un escenario de varios inquilinos, cada inquilino debe tener un centro independiente. Nunca debe compartir el mismo centro de notificaciones entre entornos de producción y de prueba, ya que esto puede causar problemas posteriores al enviar notificaciones. Por ejemplo, Apple ofrece extremos de espacio aislado y de inserción de producción, cada uno con unas credenciales distintas. Si el concentrador se ha configurado originalmente con un certificado de espacio aislado de Apple y, a continuación, se vuelve a configurar para usar el certificado de producción de Apple, los tokens antiguos del dispositivo dejan de ser válidos con el nuevo certificado y la inserción no se realiza correctamente. Es mejor separar los entornos de producción y de prueba y utilizar centros diferentes para entornos diferentes.

*Credenciales de PNS*: cuando una aplicación móvil se registra en el portal para desarrolladores de una plataforma (por ejemplo, Apple, Google, etc.), obtiene un identificador de aplicación y tokens de seguridad que el back-end de la aplicación necesita proporcionar a los servicios de notificaciones push de la plataforma para poder enviar notificaciones push a los dispositivos. Estos tokens de seguridad que pueden ser en forma de certificados (por ejemplo, para Apple iOS o Windows Phone) o claves de seguridad (Google Android, Windows), entre otros, y deben configurarse en los centros de notificaciones. Esto se hace normalmente en el nivel de concentrador de notificación, pero también puede realizarse en el nivel de espacio de nombres en un escenario de varios inquilinos.

*Espacios de nombres:* los espacios de nombres también se pueden usar para agrupar las implementaciones. También puede utilizarse para representar todos los centros de notificaciones para todos los inquilinos de la misma aplicación en el escenario de varios inquilinos.

*Distribución geográfica*: la distribución geográfica no siempre es crítica en el caso de las notificaciones push. Se realiza para destacar que los distintos servicios de notificación de inserción (APNS, GCM, etc.) que entregan en última instancia las notificaciones de inserción a los dispositivos no se han distribuido de manera uniforme. Sin embargo, si tiene una aplicación que se usa en todo el mundo, puede crear varios centros en distintos espacios de nombres para sacar provecho de la disponibilidad del servicio de los centros de notificaciones en diferentes regiones de Azure en todo el mundo. Tenga en cuenta que esto aumentará el coste de administración, especialmente en relación con los registros, por lo que no es muy recomendable y solo debe realizarse si es realmente necesario.
 
###10. ¿Debemos llevar a cabo los registros desde el backend de la aplicación o desde los dispositivos directamente?
Los registros desde el backend de la aplicación son útiles cuando tiene que realizar una autenticación de cliente antes de crear el registro o cuando se tienen etiquetas que deben crearse o modificarse por el back-end de aplicación basándose en alguna lógica de aplicación. Más información disponible aquí: [Instrucciones de registro de back-end] e [Instrucciones de registro de back-end - Parte 2]
 
###11. ¿Qué es el modelo de seguridad?
Los Centros de notificaciones de Azure usan un modelo de seguridad basado en la firma de acceso compartido (SAS). Puede utilizar los tokens SAS en el nivel de espacio de nombres raíz o en el nivel específico de los centros de notificación. Estos tokens de SAS pueden establecerse con distintas reglas de autorización; por ejemplo, permisos de envío de mensajes, permisos de notificaciones de escucha, etc. Más información aquí: [Modelo de seguridad de los Centros de notificaciones]
 
###12. ¿Cómo se controlan las cargas confidenciales en las notificaciones? 
Todas las notificaciones se entregan a los dispositivos mediante las plataformas de servicios de notificación de inserción (PNS). Cuando un remitente envía una notificación a los centros de notificaciones de Azure, procesamos y pasamos la notificación a las PNS correspondientes. Todas las conexiones desde el remitente a los Centros de notificaciones de Azure y a la PNS usan HTTPS. Los Centros de notificaciones de Azure no registran la carga del mensaje de ninguna manera. No obstante, para enviar cargas confidenciales, recomendamos un patrón de inserción seguro en el que el remitente envía una notificación ’ping’ con un identificador de mensaje al dispositivo sin la carga confidencial y, cuando la aplicación en el dispositivo recibe esta carga, llama a una API de back-end de aplicación segura directamente para capturar los detalles del mensaje. Aquí tiene el tutorial para implementar el patrón: [Tutorial de inserción segura de los Centros de notificaciones].
 
##Operaciones
###1. ¿Qué es el historial de recuperación ante desastres (DR)?
Ofrecemos una recuperación ante desastres de metadatos nuestro final (nombre del centro de notificaciones, cadena de conexión, etc.). En el caso de una recuperación ante desastres, los datos de los registros se perderán, por lo que tendrá que proponer una solución para volver a rellenarlos en su centro de nuevo.

- *Paso 1*: cree un centro de notificaciones secundario en un controlador de dominio diferente. Puede crearlo sobre la marcha en el momento del evento de recuperación ante desastres, o bien puede crear uno desde cero. No es muy relevante, ya que el aprovisionamiento del Centro de notificaciones es un proceso rápido de unos pocos segundos. Al tener uno desde el principio, también se protege contra el evento de recuperación ante desastres, que afecta a nuestras capacidades de administración, por lo que esto es recomendable. 

- *Paso 2*: hidrate el centro de notificaciones secundario con los registros del centro de notificaciones principal. No se recomienda intentar mantener los registros en ambos centros de ambos ni intentar mantenerlos sincronizados conforme llegan los registros. Esto no funciona bien debido a la naturaleza inherente de los registros, que caducan según el PNS. Los centros de notificaciones se limpian cuando recibimos comentarios del PNS de que los registros han caducado o no son válidos.

Se recomienda usar un back-end de aplicación que:

- mantenga ese conjunto de registros en su lugar para que pueda realizar una inserción masiva en el centro de notificaciones secundario en caso de recuperación ante desastres; O BIEN

- obtenga un volcado normal de registros desde el centro principal como una copia de seguridad y, a continuación, realice una inserción masiva en el centro de notificaciones secundario.

(La funcionalidad de exportación/importación de registros disponible en el nivel Estándar se describe aquí: [Exportación e importación de registros]).
 
Si no dispone de un backend, cuando la aplicación se inicie en los dispositivos, estos realizarán un nuevo registro en el centro de notificaciones secundario y, finalmente, el centro de notificaciones secundario tendrá todos los dispositivos activos registrados, pero habrá un periodo durante el cual los dispositivos donde las aplicaciones no se hayan abierto no recibirán notificaciones.
 
###2. ¿Hay alguna funcionalidad de registro de auditoría?
Todas las operaciones de administración de los centros de notificación van a los registros de operaciones, que se exponen en el Portal de administración de Azure.
 
##Supervisión y solución de problemas
###1. ¿Qué funcionalidades de solución de problemas hay disponibles?
Los Centros de notificaciones de Azure proporcionan varias características para la solución de problemas comunes, especialmente en el escenario más común sobre las notificaciones quitadas. Vea los detalles de este artículo sobre solución de problemas: [Solución de problemas de los Centros de notificaciones].
 
###2. ¿Qué características de telemetría hay disponibles?
Los Centros de notificaciones Azure permiten ver los datos de telemetría en el Portal de administración de Azure. Puede encontrar detalles de las métricas disponibles aquí: [Métricas de los Centros de notificaciones]. Tenga en cuenta que con "notificaciones correctas" solo se indica que estas notificaciones se han entregado al servicio de notificación de inserción externo (APNS de Apple, GCM para Google, etc.) y, a continuación, al PNS para entregarse a los dispositivos. El PNS no nos muestra estas métricas. También proporciona la funcionalidad de exportar la telemetría mediante programación (en el nivel estándar). Vea este ejemplo para obtener más información: [Ejemplo de métrica de Centro de notificaciones].

[Precios de los Centros de notificaciones]: http://azure.microsoft.com/pricing/details/notification-hubs/
[SLA de Centros de notificaciones]: http://azure.microsoft.com/support/legal/sla/
[Caso práctico: Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[(caso práctico: Skanska)]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[(caso práctico: Seattle Times)]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[(caso práctico: Mural.ly)]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[(caso práctico: 7Digital)]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[API de REST de Centros de notificaciones]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Tutoriales de introducción a los Centros de notificaciones]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Envío de SMS con Servicios móviles]: http://azure.microsoft.com/documentation/articles/partner-twilio-mobile-services-how-to-use-voice-sms/
[tutorial de aplicaciones de Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Precios de Servicios móviles]: http://azure.microsoft.com/pricing/details/mobile-services/
[Instrucciones de registro de back-end]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Instrucciones de registro de back-end - Parte 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Modelo de seguridad de los Centros de notificaciones]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Tutorial de inserción segura de los Centros de notificaciones]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Solución de problemas de los Centros de notificaciones]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Métricas de los Centros de notificaciones]: https://msdn.microsoft.com/library/dn458822.aspx
[Ejemplo de métrica de Centro de notificaciones]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[Exportación e importación de registros]: https://msdn.microsoft.com/library/dn790624.aspx
 


 

<!---HONumber=July15_HO2-->