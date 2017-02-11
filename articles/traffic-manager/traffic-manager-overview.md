---
title: "¿Qué es Traffic Manager? | Microsoft Docs"
description: "Este artículo lo ayudará a entender qué es el Administrador de tráfico y si se trata de la opción de enrutamiento de tráfico adecuada para su aplicación."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 69b94c93ad3e9c9745af8485766b4237cac0062c
ms.openlocfilehash: 79c14e713fd58d3f69f5978e550a78e8e84d286d

---

# <a name="overview-of-traffic-manager"></a>Introducción a Traffic Manager

Microsoft Azure Traffic Manager permite controlar la distribución del tráfico de los usuarios para puntos de conexión de servicio en distintos centros de datos. Entre los puntos de conexión de servicio compatibles con Traffic Manager, se incluyen máquinas virtuales de Azure, Web Apps y servicios en la nube. También puede utilizar el Administrador de tráfico con puntos de conexión externos, que no forman parte de Azure.

Traffic Manager usa el sistema de nombres de dominio (DNS) para dirigir las solicitudes del cliente al punto de conexión más adecuado en función de un [método de enrutamiento del tráfico](traffic-manager-routing-methods.md) y el estado de los puntos de conexión. Traffic Manager proporciona una serie de métodos de enrutamiento del tráfico para satisfacer las necesidades de distintas aplicaciones, la [supervisión](traffic-manager-monitoring.md) del estado de los puntos de conexión y la conmutación automática por error. Traffic Manager es resistente a errores, incluidos los que afecten a toda una región de Azure.

## <a name="traffic-manager-benefits"></a>Ventajas del Administrador de tráfico

El Administrador de tráfico puede ayudarle a:

* **Mejorar la disponibilidad de las aplicaciones esenciales**

    Traffic Manager ofrece alta disponibilidad para las aplicaciones al supervisar los puntos de conexión y proporcionar la conmutación automática por error cuando un punto de conexión deja de funcionar.

* **Mejorar la capacidad de respuesta para las aplicaciones de alto rendimiento**

    Azure permite ejecutar servicios en la nube en los centros de datos ubicados en todo el mundo. Traffic Manager mejora la capacidad de respuesta de las aplicaciones al dirigir el tráfico al punto de conexión con la menor latencia de red para el cliente.

* **Realizar el mantenimiento del servicio sin tiempo de inactividad**

    Puede realizar operaciones de mantenimiento planeado en sus aplicaciones sin tiempo de inactividad. Traffic Manager dirige el tráfico a puntos de conexión alternativos mientras el mantenimiento está en curso.

* **Combinar aplicaciones locales y basadas en la nube**

    Traffic Manager admite puntos de conexión externos (que no forman parte de Azure) que pueden emplearse en implementaciones locales y de nube híbrida, entre ellos los escenarios de implementación, migración y conmutación por error a la nube.

* **Distribuir el tráfico para implementaciones grandes y complejas**

    Mediante los [perfiles anidados de Traffic Manager](traffic-manager-nested-profiles.md), se pueden combinar métodos de enrutamiento del tráfico para crear reglas complejas y flexibles que satisfagan las necesidades de las implementaciones más grandes y complejas.

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre [cómo funciona el Administrador de tráfico](traffic-manager-how-traffic-manager-works.md).
* Aprenda a desarrollar aplicaciones de alta disponibilidad mediante la [supervisión de puntos de conexión del Administrador de tráfico](traffic-manager-monitoring.md).
* Encuentre más información sobre los [métodos de enrutamiento de tráfico](traffic-manager-routing-methods.md) que admite el Administrador de tráfico.
* [Creación de un perfil del Administrador de tráfico](traffic-manager-manage-profiles.md).



<!--HONumber=Nov16_HO3-->


