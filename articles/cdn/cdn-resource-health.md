---
title: "Supervisión del estado de los recursos de una red CDN de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo supervisar el estado de los recursos de una red CDN de Azure con Estado de los recursos de Azure."
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: 
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 37fe208f5087f318e665e76825127854b4a11c98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Supervisión del estado de los recursos de una red CDN de Azure
  
El estado de los recursos de la red CDN de Azure es un subconjunto de [Estado de los recursos de Azure](../resource-health/resource-health-overview.md).  Puede usar Estado de los recursos de Azure para supervisar el estado de los recursos de una red CDN y recibir instrucciones procesables para solucionar problemas.

>[!IMPORTANT] 
>El estado de los recursos de la red CDN de Azure solo tiene en cuenta el estado de la entrega de la red CDN global y las funciones de la API.  El estado de los recursos de la red CDN de Azure no comprueba los puntos de conexión individuales de la red CDN.
>
>Las señales que recibe el estado de los recursos de la red CDN de Azure pueden retrasarse hasta 15 minutos.

## <a name="how-to-find-azure-cdn-resource-health"></a>Buscar el estado de los recursos de una red CDN de Azure

1. En [Azure Portal](https://portal.azure.com), vaya a su perfil de la red CDN.

2. Haga clic en el botón **Configuración**.

    ![Botón Configuración](./media/cdn-resource-health/cdn-profile-settings.png)

3. En *Soporte y solución de problemas*, haga clic en **Estado de los recursos**.

    ![Estado de los recursos de la red CDN](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>También encontrará los recursos de la red CDN en el icono *Estado de los recursos* de la hoja *Ayuda y soporte técnico*.  Para acceder rápidamente a *Ayuda y soporte técnico*, haga clic en el círculo con un signo de interrogación (**?**) en la esquina superior derecha del portal.
>
> ![Ayuda y soporte técnico](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Mensajes específicos de la red CDN de Azure

Los estados relacionados con el estado de los recursos de una red CDN de Azure se muestra a continuación.

|Message | Acción recomendada |
|---|---|
|Es posible que haya detenido, quitado o configurado de forma incorrecta uno o más de los puntos de conexión de la red CDN. | Es posible que haya detenido, quitado o configurado de forma incorrecta uno o más de los puntos de conexión de la red CDN.|
|El servicio de administración de la red CDN no está disponible en estos momentos. | Vuelva aquí para ver actualizaciones de estado. Si el problema persiste después del tiempo de resolución esperado, póngase en contacto con el soporte técnico.|
|Es posible que los puntos de conexión de la red CDN se vean afectados por problemas continuos con algunos de nuestros proveedores de redes CDN. | Vuelva aquí para ver actualizaciones de estado. Use la herramienta de solución de problemas para obtener información sobre cómo probar el origen y el punto de conexión de la red CDN. Si el problema persiste después del tiempo de resolución esperado, póngase en contacto con el soporte técnico. |
|Los cambios de configuración en los puntos de conexión de la red CDN están experimentando retrasos en la propagación. | Vuelva aquí para ver actualizaciones de estado. Si los cambios de configuración no se propagan por completo en el tiempo esperado, póngase en contacto con el soporte técnico.|
|Tenemos problemas al cargar el portal complementario. | Vuelva aquí para ver actualizaciones de estado. Si el problema persiste después del tiempo de resolución esperado, póngase en contacto con el soporte técnico.|
Tenemos problemas con algunos de nuestros proveedores de red CDN. | Vuelva aquí para ver actualizaciones de estado. Si el problema persiste después del tiempo de resolución esperado, póngase en contacto con el soporte técnico. |

## <a name="next-steps"></a>Pasos siguientes

- [Información general sobre el estado de los recursos de Azure](../resource-health/resource-health-overview.md)
- [Solución de problemas de la compresión en la red CDN](./cdn-troubleshoot-compression.md)
- [Solución de problemas de errores 404](./cdn-troubleshoot-endpoint.md)