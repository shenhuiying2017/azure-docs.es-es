---
title: "Preguntas más frecuentes sobre supervisión remota de Azure IoT Suite | Microsoft Docs"
description: "Preguntas más frecuentes sobre la solución preconfigurada de supervisión remota de IoT Suite"
services: iot-suite
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 3885e40eb4ddbf61b03a467a71d4dd97d00a9042
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Preguntas más frecuentes sobre el Conjunto de aplicaciones de IoT

Consulte, además, las [preguntas más frecuentes](iot-suite-faq.md) generales.

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>¿Cuánto cuesta aprovisionar la nueva solución de supervisión remota?

La nueva solución preconfigurada ofrece dos opciones de implementación:

* Una opción *básica* diseñada para desarrolladores que buscan el menor costo de desarrollo o para clientes que desean crear una demostración o una prueba de concepto.
* Una opción *estándar* diseñada para empresas que desean implementar una infraestructura para entornos de producción.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>¿Cómo me aseguro de mantener mis costos mientras desarrollo la solución?

Además de proporcionar dos implementaciones diferenciadas, la nueva solución de supervisión remota tiene una configuración para habilitar o deshabilitar todos los dispositivos simulados a petición. Deshabilitar la simulación reduce la ingestión de datos en la solución y, por lo tanto, el costo total de los datos.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>¿Cuál es la diferencia entre los niveles básico y estándar de las opciones de implementación estándar? ¿Cómo decido entre las dos opciones de implementación?

Cada opción de implementación responde a necesidades distintas. La implementación básica está diseñada para empezar a trabajar y desarrollar pequeños proyectos piloto y pruebas de concepto. Proporciona una arquitectura simplificada con los recursos mínimos necesarios y un costo menor. La implementación estándar está diseñada para crear y personalizar una solución para entornos de producción y dispone de los elementos necesarios para materializarla. En cuanto a la confiabilidad y el escalado, los microservicios de aplicación se crean como contenedores de Docker y se implementan con un orquestador (Kubernetes, de forma predeterminada). El orquestador es responsable de la implementación, el escalado y la administración de la aplicación. Debe elegir una opción en función de sus necesidades actuales. Puede usar una u otra, o bien una combinación de ambas, según la fase del proyecto.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>¿Cómo configuro un mapa dinámico en el panel?

Para obtener más información, vea [Upgrade map key to see devices on a dynamic map](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) (Actualización de la clave de mapa para ver dispositivos en un mapa dinámico).

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>¿Cuántas API de Mapas de Bing gratis se pueden aprovisionar en una suscripción?

Dos. Solo puede crear dos planes de Mapas de Bing para empresa de nivel 1 de transacciones internas en una suscripción de Azure. La solución de supervisión se aprovisiona de forma predeterminada con el plan de nivel 1 de transacciones internas. Como consecuencia, solo se pueden aprovisionar un máximo de dos soluciones de supervisión remota en una suscripción sin modificaciones.

### <a name="next-steps"></a>pasos siguientes

También puede explorar algunas de las demás características y funcionalidades de las soluciones preconfiguradas del conjunto de aplicaciones de IoT:

* [Exploración de las funcionalidades de la solución preconfigurada de supervisión remota](iot-suite-remote-monitoring-explore.md)
* [Información general de la solución preconfigurada de mantenimiento predictivo](iot-suite-predictive-overview.md)
* [Introducción a la solución preconfigurada de fábrica conectada](iot-suite-connected-factory-overview.md)
* [Seguridad de Internet de las cosas desde el principio](securing-iot-ground-up.md)