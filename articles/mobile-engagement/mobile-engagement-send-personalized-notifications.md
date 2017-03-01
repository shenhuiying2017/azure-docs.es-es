---
title: "Envío de una notificación personalizada con Azure Mobile Engagement"
description: "Envío de notificaciones personalizadas mediante la inclusión de información del perfil de usuario en las notificaciones, como sus nombres"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 51007089-156a-4bac-bb1b-a590633bf2a2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: all
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 06e16033435ed0a37d5688055743875827d3aec2
ms.openlocfilehash: f27ed36eddddfab1de424574b0cf8e9ef04a162d
ms.lasthandoff: 03/01/2017


---
# <a name="personalize-notifications-by-including-user-name"></a>Personalización de notificaciones con el nombre de usuario
Para crear notificaciones más atractivas para los usuarios de aplicación, existe la posibilidad de personalizarlas. Un enfoque eficaz incluye la utilización selectiva de los nombres de los usuarios de la aplicación al dirigir las notificaciones con el fin de hacerlas más personales. Precaución: debe tener cuidado al agregar los nombres de usuario a las notificaciones, ya que si utiliza esta estrategia demasiado, podría llegar a parecerle horrible a algunos usuarios de la aplicación. También debe asegurarse de que está permitiendo al usuario optar por proporcionar estos detalles personales con un consentimiento total en la aplicación móvil antes de comenzar a usarlo. 

Técnicamente, con Azure Mobile Engagement, puede personalizar las notificaciones mediante los pasos siguientes, en los que usará el escenario de incluir el nombre del usuario en las notificaciones. Usará el concepto de app-info o tags, cuyos valores podrían pasar los SDK integrados en la aplicación móvil o mediante las API. A continuación, estas app-info o tags podrían usarse:

1. Para dirigir notificaciones a usuarios específicos de acuerdo con los valores de app-info o 
2. Como marcadores de posición en las notificaciones que se sustituirán por valores específicos del dispositivo/usuario al enviarlas notificaciones al dispositivo. 

> [!IMPORTANT]
> Tenga en cuenta que la velocidad de envío de las notificaciones se reducirá debido a este proceso adicional de reemplazar los valores de app-info en cada notificación. 
> 
> 

## <a name="register-app-info-in-the-mobile-engagement-portal"></a>Registro de app-info en el portal de Mobile Engagement
1) Para empezar, registre etiquetas o la información de la aplicación en Azure Portal. Para hacerlo, vaya a **Configuración** -> **Etiqueta (App-Info)**.  

![][1]    

2) Haga clic en **Nueva etiqueta (app-info)**, especifique el nombre como *user_name* y el tipo como *cadena*, y haga clic en **Enviar**. 

![][2]

3) Por último, verá esta información de app-info registrada de esta forma:

![][3]

## <a name="send-app-info-from-the-client-sdk"></a>Envío de app-info desde el SDK cliente
Aquí usamos el ejemplo de aplicación universal de Windows, pero también existen métodos equivalentes para nuestros demás SDK. 

Si damos por supuesto que dispone de un método en la aplicación móvil donde obtiene la información de perfil de los usuarios, así como sus nombres, probablemente después de autenticarlos, llamará al método `SendAppInfo` aquí y rellenará el valor de la app-info `user_name` que registró antes en el back-end del servicio Mobile Engagement. 

    Dictionary<object, object> appInfo = new Dictionary<object, object>();
    appInfo.Add("user_name", str);
    EngagementAgent.Instance.SendAppInfo(appInfo); 

## <a name="send-personalized-notifications"></a>Envío de notificaciones personalizadas
Ahora está listo para enviar notificaciones con este **user_name**. 

1) Vaya a la pestaña **Cobertura** del portal de Mobile Engagement para crear una notificación y use este marcador de posición con el formato siguiente en cualquier lugar del título o el cuerpo de la notificación. 

![][4]    

> [!NOTE]
> Los usuarios para los que no se establece la app-info user_name, no recibirán ninguna notificación. Si ejecuta la campaña de notificación en modo de prueba y no tiene información de aplicación establecida, enviaremos el carácter '?' para reemplazar el marcador de posición. 
> 
> 

2) Cuando Mobile Engagement seleccione un dispositivo para enviar esta notificación, usará esta app-info y reemplazará el valor en el marcador de posición.  
Por ejemplo, si hemos configurado `str = "Scott"` para un usuario, el registro del dispositivo se asociará a la app-info de **user_name = SCOTT** para este usuario, que verá la notificación de inserción de la aplicación con el formato siguiente. 

![][5]    

<!-- Images. -->
[1]: ./media/mobile-engagement-send-personalized-notifications/app-info.png
[2]: ./media/mobile-engagement-send-personalized-notifications/create-app-info.png
[3]: ./media/mobile-engagement-send-personalized-notifications/app-info-user-name.png
[4]: ./media/mobile-engagement-send-personalized-notifications/personal-notification.png
[5]: ./media/mobile-engagement-send-personalized-notifications/notification.png


