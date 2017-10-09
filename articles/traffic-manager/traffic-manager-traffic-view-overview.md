---
title: Traffic View en Azure Traffic Manager | Microsoft Docs
description: "Introducción a Traffic View de Traffic Manager"
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: f1ab98ab173edc794bfabdf55d950ac689a37c0a
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="traffic-manager-traffic-view"></a>Traffic View de Traffic Manager

>[!NOTE]
>La característica Traffic View de Traffic Manager está en versión preliminar pública y es posible que no tenga el mismo nivel de disponibilidad y confiabilidad que las características que están en versión de disponibilidad general. Esta característica no se admite, puede tener funcionalidades limitadas y no estar disponible en todas las ubicaciones de Azure. Para obtener las notificaciones más recientes acerca de la disponibilidad y el estado de esta característica, consulte la página de [actualizaciones de Azure Traffic Manager](https://azure.microsoft.com/updates/?product=traffic-manager).

Traffic Manager proporciona enrutamiento en el nivel de DNS para que los usuarios finales sean dirigidos a puntos de conexión en buen estado en función del método de enrutamiento que se haya configurado cuando se creó el perfil. Esto proporciona a Traffic Manager una visión de las bases de usuarios (en un nivel de granularidad de resolución de DNS) y su patrón de tráfico. Cuando se habilita Traffic View, esta información se procesa para proporcionarle detalles sobre lo que actuar. 

Mediante el uso de Traffic View, puede:
- comprender dónde se encuentran las bases de usuarios (hasta una granularidad en el nivel de resolución DNS local).
- conocer el volumen de tráfico que se origina desde estas regiones.
- conocer la latencia representativa que experimentan estos usuarios.
- profundizar en los patrones de tráfico específicos de cada una de estas bases de usuarios para las regiones de Azure en la que tiene puntos de conexión. 

Por ejemplo, puede usar Traffic View para conocer qué regiones tienen una gran cantidad de tráfico pero experimentan latencias más altas. A continuación, puede utilizar esta información para planear la expansión de superficie a nuevas regiones de Azure para que estos usuarios pueden tener una experiencia de latencia inferior.

## <a name="how-traffic-view-works"></a>Cómo funciona Traffic View

Traffic View funciona haciendo que Traffic Manager busque las consultas entrantes recibidas en los últimos siete días con un perfil que tenga esta característica habilitada. De esta información extrae la dirección IP de origen de la resolución DNS, que se usa como una representación de la ubicación de los usuarios. A continuación, se agrupan en una granularidad en el nivel de resolución DNS para crear regiones de bases de usuarios mediante el uso de la información geográfica de direcciones IP mantenida por Traffic Manager. Traffic Manager, a continuación, busca en las regiones de Azure a las que se enrutó la consulta y construye un mapa de flujo de tráfico para los usuarios de esas regiones.
En el paso siguiente, Traffic Manager correlaciona la región de la base de usuarios con la asignación de región de Azure con las tablas de información de latencia de red que mantiene para diferentes redes de usuario final para conocer la latencia media que experimentan los usuarios de esas regiones cuando se conectan a regiones de Azure. Todos estos cálculos se tabulan en el nivel de dirección IP de resolución DNS local antes de ser presentados. Puede procesar esta información en un flujo de trabajo de análisis de su elección y también puede descargar esta información como un archivo CSV.
Cuando use Traffic View, se le facturará en función del número de puntos de datos utilizados para crear la información de detalle presentada. Actualmente, el único tipo de punto de datos utilizado son las consultas que recibe el perfil de Traffic Manager. Para más detalles sobre los precios, visite la [página de precios de Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Pasos siguientes

- Aprenda [cómo funciona el Administrador de tráfico](traffic-manager-overview.md)
- Aprenda más sobre los [métodos de enrutamiento de tráfico](traffic-manager-routing-methods.md) que admite el Administrador de tráfico.
- Aprenda a [crear un perfil de Administrador de tráfico](traffic-manager-create-profile.md)


