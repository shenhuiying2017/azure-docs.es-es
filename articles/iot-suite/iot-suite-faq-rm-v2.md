---
title: Preguntas frecuentes sobre el acelerador de la solución de supervisión remota | Microsoft Azure
description: Preguntas frecuentes sobre el acelerador de la solución de supervisión remota
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: d1cc260710d025428a1ca77c41c104dc172447e6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Preguntas frecuentes sobre el acelerador de la solución de supervisión remota

Consulte, además, las [preguntas más frecuentes](iot-suite-faq.md) generales.

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>¿Cuánto cuesta aprovisionar la nueva solución de supervisión remota?

El nuevo acelerador de la solución ofrece dos opciones de implementación:

* Una opción *básica* diseñada para desarrolladores que buscan el menor costo de desarrollo o para clientes que desean crear una demostración o una prueba de concepto.
* Una opción *estándar* diseñada para empresas que desean implementar una infraestructura para entornos de producción.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>¿Cómo me aseguro de mantener mis costos mientras desarrollo la solución?

Además de proporcionar dos implementaciones diferenciadas, la nueva solución de supervisión remota tiene una configuración para habilitar o deshabilitar todos los dispositivos simulados a petición. Deshabilitar la simulación reduce la ingestión de datos en la solución y, por lo tanto, el costo total de los datos.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>¿Cuál es la diferencia entre los niveles básico y estándar de las opciones de implementación estándar? ¿Cómo decido entre las dos opciones de implementación?

Cada opción de implementación responde a necesidades distintas. La implementación básica está diseñada para empezar a trabajar y desarrollar pequeños proyectos piloto y pruebas de concepto. Proporciona una arquitectura simplificada con los recursos mínimos necesarios y un costo menor. La implementación estándar está diseñada para crear y personalizar una solución para entornos de producción y dispone de los elementos necesarios para materializarla. En cuanto a la confiabilidad y el escalado, los microservicios de aplicación se crean como contenedores de Docker y se implementan con un orquestador (Kubernetes, de forma predeterminada). El orquestador es responsable de la implementación, el escalado y la administración de la aplicación. Debe elegir una opción en función de sus necesidades actuales. Puede usar una u otra, o bien una combinación de ambas, según la fase del proyecto.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>¿Cómo configuro un mapa dinámico en el panel?

Para obtener más información, vea [Upgrade map key to see devices on a dynamic map](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) (Actualización de la clave de mapa para ver dispositivos en un mapa dinámico).

### <a name="next-steps"></a>Pasos siguientes

También puede explorar algunas de las demás características y funcionalidades de los aceleradores de soluciones de IoT:

* [Exploración de las funcionalidades del acelerador de la solución de supervisión remota](iot-suite-remote-monitoring-explore.md)
* [Introducción al acelerador de la solución de mantenimiento predictivo](iot-suite-predictive-overview.md)
* [Información general del acelerador de soluciones de factoría conectada](iot-suite-connected-factory-overview.md)
* [Seguridad de Internet de las cosas desde el principio](securing-iot-ground-up.md)