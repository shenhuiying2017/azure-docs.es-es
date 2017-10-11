---
title: "Azure Mobile Engagement: características principales"
description: "Describe las características clave de Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: d98bafb6-4fd0-4cc3-8c2f-962af70c416c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 8270a223ca31c623c1b760a64c0e4d64fb85e080
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="azure-mobile-engagement---key-features"></a>Azure Mobile Engagement: características principales
En este artículo se proporciona una introducción de alto nivel sobre las características principales de la plataforma Mobile Engagement. 

## <a name="general"></a>**General**
* **Encuentre SDK para las principales plataformas.** Encontrará SDK disponibles para las principales plataformas: iOS, Android, Windows universal, Windows Phone Silverlight, Kindle o Cordova. 
  Proporcionamos SDK fáciles de integrar y documentación útil para que comience a trabajar en cualquier plataforma de su elección. 
* **Portal de SaaS independiente.** Le facilita el acceso al equipo de marketing, sin que tenga que pasar por el Portal de administración de Azure. 
* **Disponibilidad de API de REST abiertas.** Para permitir la integración y la automatización con sistemas de CRM, CMS y TI mediante API de plataforma abierta, proporcionamos API de REST abiertas y SDK de .NET para que pueda usar estas API, que permiten una fácil integración y automatización con Mobile Engagement. Consulte [este artículo](mobile-engagement-api-authentication.md) para obtener más información. 
* **Conector de Power BI disponible.** También puede extraer los gráficos de análisis clave a un panel de Power BI. Consulte esta [guía](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-mobile/).
* **Garantía de seguridad y privacidad** Dado que Azure Mobile Engagement forma parte de la familia de Azure, sigue todos los procedimientos recomendados estándar de seguridad y privacidad que se esperan de un servicio en la nube.

## <a name="actionable-analytics"></a>**Análisis accionable**
* **Supervisión de datos en tiempo real** Puede realizar un seguimiento del análisis en tiempo real con el módulo Supervisar, que muestra en tiempo real detalles como sesiones, eventos, errores y bloqueos. Eche un vistazo a este [artículo](mobile-engagement-concepts.md) para entender los conceptos básicos. 
  
    ![][1]
  
    ![][2]        
* **Consulte datos agregados** También obtendrá una vista más completa de los datos de análisis agregados con nuestro módulo Análisis, que permite filtrar fácilmente los datos en función de la versión de la aplicación y los períodos de tiempo.
  
    ![][3]        
* **Obtenga información sobre los usuarios y el patrón de retención.**
  
    ![][4]        
* **Obtenga información sobre la procedencia de los usuarios y el tiempo que pasan en la pantalla.**
  
    ![][5]        
  
    ![][6]        
* **Averigüe qué pantallas visitan los usuarios de la aplicación y cómo puede optimizar la ruta de acceso de usuario.** Esto les ayuda a detectar las pantallas y las características que usted desea que detecten.
  
    ![][7]        
  
    ![][8]        
* **Obtenga información sobre los eventos más frecuentes de su aplicación y entienda el proceso empresarial en función de dichos eventos.** 
  
    ![][9]    
* **Realice un seguimiento de los errores comunes y los bloqueos y obtenga información para el equipo de desarrollo.**
  
    ![][10]        
  
    ![][11]    
* **Comprenda a qué dispositivos y redes acceden los usuarios desde su aplicación, con el fin de optimizarla.** 
  
    ![][12]    

## <a name="targeted--personalized-push-notifications"></a>**Notificaciones push específicas y personalizadas**
* **Cree un segmento basado en los datos recopilados.** Para ello, puede usar cualquiera de los datos de evento, sesión, actividad, trabajo, bloqueo, error o etiqueta.
  
    ![][13]
  
    ![][14]        
* **Realice un seguimiento del historial de los segmentos creados día a día.**
  
    ![][15]    
* **Envíe notificaciones específicas.** Puede enviarlas usando los criterios más habituales, como los usuarios más antiguos o más recientes, o bien enviarlas a un segmento personalizado que haya creado.
  
    ![][16]    
* **Envíe notificaciones push fuera de aplicación o del sistema y basadas en HTML enriquecido, según corresponda en su caso**.
  
    ![][17]    
  
    ![][18]    
* **Oriente las notificaciones en aplicación para que se muestren en una pantalla o una actividad concretas de la aplicación.**
  
    ![][19]    
* **Especifique la acción que debe producirse cuando el usuario hace clic en una notificación.** Puede ser algo tan sencillo como abrir una página web o navegar dentro de la aplicación a una pantalla específica al hacer clic en la notificación. 
  
    ![][20]
* **Envíe notificaciones localizadas.** De esta forma, podrá dirigirse a los usuarios de la aplicación en el idioma en el que se sientan más a gusto. 
  
    ![][21]    
* **Especifique una hora de inicio y de finalización para las campañas.** 
  
    ![][22]    
* **Pruebe las notificaciones fácilmente.** Para ello, registre un dispositivo de prueba y envíe la notificación de prueba solo a este dispositivo.
  
    ![][23]    
* **Configure fácilmente una notificación en aplicación para que aparezca como una encuesta o un sondeo rápidos.**  
  
    ![][24]
* **Obtenga estadísticas sobre las campañas de inserción para las notificaciones.** De esta forma, se hará una idea del grado de éxito de sus notificaciones.
  
    ![][25]    
* **Personalice fácilmente sus notificaciones e imprímales carácter mediante etiquetas o información de aplicación y emojis.** 
  
    ![][26]    
  
    ![][27]    
* **Establezca límites para las notificaciones push para no enviar notificaciones no deseadas a los usuarios.** Así podrá evitar enviar demasiadas notificaciones push a los usuarios de la aplicación, ya que podrían considerarlas no deseadas. Para ello resulta útil la funcionalidad para limitar las notificaciones push, ya que permite configurar los límites de inserción para cada segmento. 
  
    ![][28]            

<!-- Images -->
[1]: ./media/mobile-engagement-key-features/monitor1.png
[2]: ./media/mobile-engagement-key-features/monitor2.png
[3]: ./media/mobile-engagement-key-features/analytics-filter.png
[4]: ./media/mobile-engagement-key-features/retention.png
[5]: ./media/mobile-engagement-key-features/analytics-geomap.png
[6]: ./media/mobile-engagement-key-features/analytics-session-length.png
[7]: ./media/mobile-engagement-key-features/analytics-activities.png
[8]: ./media/mobile-engagement-key-features/analytics-userpath.png
[9]: ./media/mobile-engagement-key-features/analytics-events.png
[10]: ./media/mobile-engagement-key-features/analyics-errors.png
[11]: ./media/mobile-engagement-key-features/analyics-errors-details.png
[12]: ./media/mobile-engagement-key-features/technicals.png
[13]: ./media/mobile-engagement-key-features/segment.png
[14]: ./media/mobile-engagement-key-features/segment-creation.png
[15]: ./media/mobile-engagement-key-features/segment-history.png
[16]: ./media/mobile-engagement-key-features/segment-push.png
[17]: ./media/mobile-engagement-key-features/out-of-app.png
[18]: ./media/mobile-engagement-key-features/in-app-push.png
[19]: ./media/mobile-engagement-key-features/push-in-activity.png
[20]: ./media/mobile-engagement-key-features/push-action.png
[21]: ./media/mobile-engagement-key-features/push-languages.png
[22]: ./media/mobile-engagement-key-features/push-timeframe.png
[23]: ./media/mobile-engagement-key-features/push-test.png
[24]: ./media/mobile-engagement-key-features/push-poll.png
[25]: ./media/mobile-engagement-key-features/push-stats.png
[26]: ./media/mobile-engagement-key-features/push_personalized.png
[27]: ./media/mobile-engagement-key-features/push_emoji.png
[28]: ./media/mobile-engagement-key-features/push_limits.png









