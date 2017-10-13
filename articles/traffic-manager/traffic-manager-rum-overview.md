---
title: Real User Measurements en Azure Traffic Manager | Microsoft Docs
description: "Introducción a Real User Measurements en Traffic Manager"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: a7e8ae605b6234341d9ab8b790f4c54d8627f29f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Introducción a Real User Measurements en Traffic Manager

>[!NOTE]
>La característica Real User Measurements de Traffic Manager está en versión preliminar pública y es posible que no tenga el mismo nivel de disponibilidad y confiabilidad que las características que están en versión de disponibilidad general. Esta característica no se admite, puede tener funcionalidades limitadas y no estar disponible en todas las ubicaciones de Azure. Para obtener las notificaciones más recientes acerca de la disponibilidad y el estado de esta característica, consulte la página de [actualizaciones de Azure Traffic Manager](https://azure.microsoft.com/updates/?product=traffic-manager).

Al configurar un perfil de Traffic Manager para usar el método de enrutamiento de rendimiento, el servicio examina de dónde proceden las solicitudes de consulta DNS y toma decisiones de enrutamiento para dirigir a los solicitantes a la región de Azure que proporcione la latencia más baja. Esto se logra mediante el uso de la información de latencia de red que Traffic Manager mantiene para distintas redes de usuario final.

Real User Measurements le permite medir la latencia de red de regiones de Azure desde las aplicaciones cliente que utilizan los usuarios finales y hacer que Traffic Manager considere también esta información al tomar decisiones de enrutamiento. Si opta por usar Real User Measurements, puede aumentar la precisión de la ruta para las solicitudes procedentes de las redes en las que residen los usuarios finales. 

## <a name="how-real-user-measurements-work"></a>Cómo funciona Real User Measurements

Real User Measurements funciona haciendo que las aplicaciones cliente midan la latencia de las regiones de Azure tal como se ve desde las redes de usuario final en las que se utilizan. Por ejemplo, si tiene una página web a la que tienen acceso usuarios de distintas ubicaciones (por ejemplo, en las regiones de América del Norte), puede aprovechar la eficacia de Real User Measurements cuando se usa el método de enrutamiento de rendimiento para dirigirlos a la mejor de las regiones de Azure en las que la aplicación está hospedada.

Se comienza por insertar un código JavaScript proporcionado por Azure (que contiene una clave única) en las páginas web. Una vez hecho, siempre que un usuario visita la página web, el código JavaScript consulta a Traffic Manager para obtener información acerca de las regiones de Azure que debe medir. El servicio devuelve un conjunto de puntos de conexión al script que, a continuación, mide estas regiones consecutivamente mediante la descarga de una imagen de un solo píxel hospedada en esas regiones de Azure y anotando la latencia entre el momento en el que se envió la solicitud y la hora a la que se recibió el primer byte. Estas mediciones, a continuación, se notifican de vuelta al servicio Traffic Manager.

Con el tiempo, esto ocurre muchas veces y en muchas redes, proporcionando a Traffic Manager información más precisa sobre las características de latencia de las redes en las que residen los usuarios finales. Esta información comienza a incluirse en las decisiones de enrutamiento realizadas por Traffic Manager. Como resultado, se producirá una mayor precisión en esas decisiones basadas en las mediciones de usuarios reales enviadas.

Cuando use Real User Measurements, se le facturará en función del número de mediciones enviadas a Traffic Manager. Para más detalles sobre los precios, visite la [página de precios de Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="next-steps"></a>Pasos siguientes
- Obtenga información acerca de cómo usar [Real User Measurements con páginas web](traffic-manager-create-rum-web-pages.md)
- Aprenda [cómo funciona el Administrador de tráfico](traffic-manager-overview.md)
- Obtenga más información sobre [Mobile Center](https://docs.microsoft.com/mobile-center/).
- Aprenda más sobre los [métodos de enrutamiento de tráfico](traffic-manager-routing-methods.md) que admite el Administrador de tráfico.
- Aprenda a [crear un perfil de Administrador de tráfico](traffic-manager-create-profile.md)

