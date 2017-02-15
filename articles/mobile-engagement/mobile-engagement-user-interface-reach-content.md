---
title: Interfaz de usuario de Azure Mobile Engagement - Contenido de cobertura
description: "Aprenda a administrar el contenido exclusivo de los diferentes tipos de campañas de notificaciones de inserción en Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: dwrede
editor: 
ms.assetid: add64f06-43c9-475c-8722-51cd00bb844b
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 555342e88c912a3f43c578a40dc34933996ade4c
ms.openlocfilehash: f6208195fb998d5cb3ca00fa684f814971083cad


---
# <a name="how-to-manage-the-unique-content-of-the-different-types-of-push-notification-campaigns"></a>Cómo administrar el contenido exclusivo de los diferentes tipos de campañas de notificaciones de inserción
Puede utilizar la sección de contenido de una nueva campaña de cobertura para modificar el contenido de los anuncios, sondeos, inserción de datos y mosaicos (solo en Windows Phone). La configuración del contenido de las campañas de inserción es específica del tipo de campaña. 

### <a name="content-types"></a>Tipos de contenido:
* Anuncios
* Sondeos
* Inserciones de datos
* Mosaicos (solo en Windows Phone)

## <a name="content-of-announcements"></a>Contenido de anuncios
 ![Reach-Content1][30] 

### <a name="choose-the-type-of-your-announcement"></a>Elija el tipo de anuncio:
* Solo notificación: es una notificación estándar simple. Lo que significa que si un usuario hace clic en él, no aparecerá ninguna vista adicional, pero se producirá solo la acción asociada a él.
* Anuncio de texto: es una notificación que compromete al usuario a echar un vistazo a una vista de texto.
* Anuncio web: es una notificación que compromete al usuario a echar un vistazo a una vista de texto.

### <a name="see-also"></a>Consulte también
* [Cobertura - Guía práctica - Anuncios][Vínculo 3] 

### <a name="about-web-view-announcements"></a>Acerca de los anuncios de visualización web:
Las apariciones del patrón "{deviceid}" en el código HTML o JavaScript que proporcione aquí se reemplazarán automáticamente por el identificador del dispositivo que muestra el anuncio. Se trata de una manera fácil de recuperar identificadores de dispositivo de Azure Mobile Engagement en un servicio web externo hospedado en su oficina.
Si desea crear una vista web de pantalla completa (sin los botones predeterminados de Acción y Salir proporcionados) puede utilizar las siguientes funciones desde código de JavaScript del anuncio de la vista web: 

* realizar la acción del anuncio: ReachContent.actionContent()
* salir del anuncio: ReachContent.exitContent()

### <a name="choose-your-action"></a>Elija la acción:
### <a name="about-action-urls"></a>Acerca de las direcciones URL de la acción:
Cualquier dirección URL que puede ser interpretada por el sistema operativo de destino de un dispositivo puede utilizarse como una dirección URL de la acción.
Cualquier dirección URL específica que pueda admitir su aplicación (por ejemplo, para hacer saltar a los usuarios a una pantalla concreta) también puede utilizarse como una dirección URL de acción.
Cada repetición del patrón {deviceid} se reemplaza automáticamente por el identificador del dispositivo que realiza la acción. Esto puede utilizarse para recuperar fácilmente los identificadores de dispositivo Azure Mobile Engagement a través de un servicio web externo hospedado en su área de operaciones.

* **Acciones de Android + iOS**
  * Abrir una página web
  * http://\[dominio-sitio-web\] 
  * Ejemplo: http://www.azure.com
  * Enviar un correo electrónico
  * mailto:\[destinatario-correo-electrónico\]?subject=\[asunto\]&body=\[mensaje\] 
  * Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
  * Enviar un SMS
  * sms:\[número-teléfono\] 
  * Ejemplo:sms:2125551212
  * Marcar un número de teléfono
  * tel:\[número-teléfono\] 
  * Ejemplo:tel:2125551212
* **Acciones solo para Android**
  * Descargar una aplicación de Play Store
  * market://details?id=\[paquete de la aplicación\] 
  * Ejemplo:market://details?id=com.microsoft.office.word
  * Iniciar una búsqueda localizada geográficamente
  * geo:0,0?q=\[consulta de búsqueda\] 
  * Ejemplo:geo:0,0?q=starbucks,paris
* **Acciones solo para iOS**
  * Descargar una aplicación de la App Store
  * http://itunes.apple.com/[país]/app/[nombre de la aplicación]/id[id. de la aplicación]?mt=8 
  * Ejemplo: http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
  * Acciones de Windows
  * Abrir una página web
  * http://\[dominio-sitio-web\] 
  * Ejemplo: http://www.azure.com
  * Enviar un correo electrónico
  * mailto:\[destinatario-correo-electrónico\]?subject=\[asunto\]&body=\[mensaje\] 
  * Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
  * Enviar un SMS (requiere la aplicación Skype)
  * sms:\[número-teléfono\] 
  * Ejemplo:sms:2125551212
  * Marcar un número de teléfono (requiere la aplicación Skype)
  * tel:\[número-teléfono\] 
  * Ejemplo:tel:2125551212
  * Descargar una aplicación de Play Store
  * ms-windows-store:PDP?PFN=\[id. del paquete de la aplicación\] 
  * Ejemplo:ms-windows-store:PDP?PFN=4d91298a-07cb-40fb-aecc-4cb5615d53c1
  * Iniciar una búsqueda en Mapas de Bing
  * bingmaps:?q=\[consulta de búsqueda\] 
  * Ejemplo:bingmaps:?q=starbucks,paris
  * Utilice un esquema personalizado
  * \[esquema personalizado\]://\[parámetros del esquema personalizado\] 
  * Ejemplo:myCustomProtocol://myCustomParams
  * Utilizar datos de paquete (se necesita una aplicación de la tienda para leer la extensión)
  * \[carpeta\]\[datos\].\[extensión\] 
  * Ejemplo:myfolderdata.txt

### <a name="build-a-tracking-url"></a>Crear una dirección URL de seguimiento:
* Consulte la sección "Configuración" de <UI Documentation> para obtener instrucciones sobre la creación de una dirección URL de seguimiento que permita a los usuarios descargar una de las otras aplicaciones.

### <a name="define-the-texts-of-your-announcement"></a>Definir los textos del anuncio
Rellene el título, el contenido y los textos del anuncio. Puede dirigirse a una audiencia de una campaña futura basándose en los comentarios de la cobertura sobre cómo respondieron los usuarios a esta campaña. La orientación a la audiencia puede basarse en los comentarios de si solo se ha insertado esta campaña, respondido, ejecutada o terminado.

### <a name="see-also"></a>Consulte también
* [Documentación de la interfaz de usuario - Cobertura - Nuevo criterio de inserción][Vínculo 28]

## <a name="content-of-polls"></a>Contenido de sondeos
![Reach-Content2][31] 

 Rellene el título, la descripción y los textos de los botones del anuncio. A continuación, agregue preguntas y opciones para las respuestas a sus preguntas.
Puede dirigirse a una audiencia de una campaña futura basándose en los comentarios de la cobertura sobre cómo respondieron los usuarios a esta campaña. La orientación a la audiencia puede basarse en si solo se ha insertado, respondido, ejecutado o terminado esta campaña. La orientación de la audiencia también puede basarse en los comentarios de respuesta de sondeos, donde la pregunta y respuesta se utilizan como criterios.

### <a name="see-also"></a>Consulte también
* [Documentación de la interfaz de usuario - Cobertura - Nuevo criterio de inserción][Vínculo 28]

## <a name="content-of-data-pushes"></a>Contenido de las inserciones de datos
![Reach-Content3][32] 

### <a name="choose-the-type-of-your-data"></a>Elija el tipo de datos:
* Texto
* Datos binarios
* Datos de Base64

### <a name="define-the-content-of-your-data"></a>Defina el contenido de los datos
* Si ha seleccionado insertar datos de texto, copie y pegue el texto en el cuadro "content".
* Si ha seleccionado insertar datos binarios o base64, use el botón "cargar el archivo" para cargar el archivo.
* Puede dirigirse a una audiencia de una campaña futura basándose en los comentarios de la cobertura sobre cómo respondieron los usuarios a esta campaña. La orientación a la audiencia puede basarse en si solo se ha insertado, respondido, ejecutado o terminado esta campaña.

### <a name="see-also"></a>Consulte también
* [Documentación de la interfaz de usuario - Cobertura - Nuevo criterio de inserción][Vínculo 28]

## <a name="content-of-tiles-windows-phone-only"></a>Contenido de los mosaicos (solo en Windows Phone)
![Reach-Content4][33]

### <a name="define-the-content-of-your-tile"></a>Defina el contenido de su mosaico
La carga de mosaicos es el texto que se mostrará en el mosaico de la aplicación en los dispositivos Windows Phone.
La inserción de un mosaico es la versión del servicio de notificaciones de inserción de Microsoft (MPNS) de una inserción nativa para Windows Phone. El tipo de inserción de mosaico es el único tipo de inserción que no tiene una respuesta y, por tanto, la audiencia de las campañas futuras no se puede integrar en los resultados de una campaña de inserción de mosaico. 

### <a name="see-also"></a>Consulte también
* [Documentación de la API - API de cobertura - Inserción nativa][Vínculo 4]

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
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
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
[Vínculo 1]: mobile-engagement-user-interface.md
[Vínculo 2]: mobile-engagement-troubleshooting-guide.md
[Vínculo 3]: mobile-engagement-how-tos.md
[Vínculo 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Vínculo 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Vínculo 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Vínculo 7]: https://account.windowsazure.com/PreviewFeatures
[Vínculo 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Vínculo 9]: http://azure.microsoft.com/services/mobile-engagement/
[Vínculo 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Vínculo 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Vínculo 12]: mobile-engagement-user-interface-navigation.md
[Vínculo 13]: mobile-engagement-user-interface-home.md
[Vínculo 14]: mobile-engagement-user-interface-my-account.md
[Vínculo 15]: mobile-engagement-user-interface-analytics.md
[Vínculo 16]: mobile-engagement-user-interface-monitor.md
[Vínculo 17]: mobile-engagement-user-interface-reach.md
[Vínculo 18]: mobile-engagement-user-interface-segments.md
[Vínculo 19]: mobile-engagement-user-interface-dashboard.md
[Vínculo 20]: mobile-engagement-user-interface-settings.md
[Vínculo 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Vínculo 22]: mobile-engagement-troubleshooting-guide-apis.md
[Vínculo 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Vínculo 24]: mobile-engagement-troubleshooting-guide-service.md
[Vínculo 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Vínculo 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Vínculo 27]: mobile-engagement-user-interface-reach-campaign.md
[Vínculo 28]: mobile-engagement-user-interface-reach-criterion.md
[Vínculo 29]: mobile-engagement-user-interface-reach-content.md




<!--HONumber=Nov16_HO3-->


