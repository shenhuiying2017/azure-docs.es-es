---
title: Preguntas frecuentes sobre los aceleradores de soluciones de Azure IoT | Microsoft Docs
description: Preguntas más frecuentes acerca de los aceleradores de soluciones de IoT
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
ms.openlocfilehash: fcaf0c2d4a8be1358e4510524c8b15e063c647bf
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Preguntas más frecuentes acerca de los aceleradores de soluciones de IoT

Consulte también [Preguntas más frecuentes específicas sobre fábricas conectadas](iot-suite-faq-cf.md) y [Preguntas más frecuentes específicas sobre supervisión remota](iot-suite-faq-rm-v2.md).

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>¿Dónde se puede encontrar el código fuente del acelerador de la solución?

El código fuente se almacena en los siguientes repositorios de GitHub:

* [Acelerador de soluciones de supervisión remota (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Acelerador de soluciones de supervisión remota (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Acelerador de la solución de mantenimiento predictivo](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Acelerador de la solución de factoría conectada](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>¿Qué SDK puedo usar para desarrollar clientes de dispositivo para los aceleradores de soluciones?

En los repositorios de GitHub de los [SDK de IoT de Microsoft Azure](https://github.com/Azure/azure-iot-sdks) encontrará vínculos a los SDK de dispositivo IoT de los distintos lenguajes (C, .NET, Java, Node.js, Python).

Si utiliza el dispositivo DevKit, encontrará recursos y ejemplos en el repositorio de GitHub del [SDK de IoT DevKit](https://github.com/Microsoft/devkit-sdk).

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>¿La nueva arquitectura de microservicios está disponible para los tres aceleradores de soluciones?

Actualmente, solo la solución de supervisión remota usa la arquitectura de microservicios, ya que abarca el escenario más amplio.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>¿Qué ventajas proporciona en la nueva actualización la nueva arquitectura basada en microservicios de código abierto?

En los dos últimos años, la arquitectura de nube ha evolucionado en gran medida. Los microservicios han surgido como un modelo excelente para lograr el escalado y la flexibilidad, sin sacrificar la velocidad de desarrollo. Este modelo de arquitectura se usa internamente en varios servicios de Microsoft con grandes resultados con respecto a la escalabilidad y la confiabilidad. Estamos poniendo en práctica estos resultados para que nuestros clientes se beneficien de ellos.

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>¿Está disponible el nuevo acelerador de soluciones en la misma región geográfica que la solución existente?

Sí, la nueva supervisión remota está disponible en las mismas regiones geográficas.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsuitecom"></a>¿Cuál es la diferencia entre eliminar un grupo de recursos en Azure Portal y hacer clic en Eliminar en un acelerador de soluciones en azureiotsuite.com?

* Si elimina el acelerador de soluciones en [azureiotsuite.com](https://www.azureiotsuite.com/), elimina todos los recursos aprovisionados cuando se creó el acelerador de la solución. Si agrega recursos adicionales al grupo de recursos, dichos recursos también se eliminan.
* Si elimina el grupo de recursos en [Azure Portal](https://portal.azure.com), solo se eliminan los recursos de ese grupo de recursos. También debe eliminar la aplicación de Azure Active Directory asociada al acelerador de la solución.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>¿Puedo seguir aprovechando mi inversión en aceleradores de soluciones de Azure IoT?

Sí. Cualquier solución que exista en la actualidad continúa funcionando en su suscripción de Azure y el código fuente sigue estando disponible en GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>¿Cuántas instancias de IoT Hub se pueden aprovisionar en una suscripción?

De forma predeterminada, puede aprovisionar [diez centros de IoT Hub por suscripción](../azure-subscription-service-limits.md#iot-hub-limits). Puede crear un [vale de soporte de Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar este límite. Como consecuencia, dado que cada acelerador de soluciones aprovisiona una nueva instancia de IoT Hub, solo puede aprovisionar hasta diez aceleradores de soluciones en una suscripción determinada.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>¿Cuántas instancias de Azure Cosmos DB se pueden aprovisionar en una suscripción?

Cincuenta. Puede crear un [vale de soporte de Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar este límite, pero de forma predeterminada solo se pueden aprovisionar 50 instancias de Cosmos DB por suscripción.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>¿Cuántas API de Mapas de Bing gratis se pueden aprovisionar en una suscripción?

Dos. Solo puede crear dos planes de Mapas de Bing para empresa de nivel 1 de transacciones internas en una suscripción de Azure. La solución de supervisión se aprovisiona de forma predeterminada con el plan de nivel 1 de transacciones internas. Como consecuencia, solo se pueden aprovisionar un máximo de dos soluciones de supervisión remota en una suscripción sin modificaciones.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>¿Puedo crear un acelerador de soluciones si tengo Microsoft Azure para DreamSpark?

> [!NOTE]
> Microsoft Azure para DreamSpark ahora se conoce como Microsoft Imagine para estudiantes.

En este momento, no se puede crear un acelerador de soluciones con una cuenta de [Microsoft Azure para DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/). Sin embargo, puede crear una [cuenta de evaluación gratuita de Azure](https://azure.microsoft.com/free/) en un par de minutos, lo que le permite crear un acelerador de soluciones.

### <a name="can-i-create-a-solution-accelerator-if-i-have-cloud-solution-provider-csp-subscription"></a>¿Puedo crear un acelerador de soluciones si tengo alguna suscripción del Proveedor de soluciones en la nube (CSP)?

En este momento, no se puede crear un acelerador de soluciones con una suscripción del Proveedor de soluciones en la nube (CSP). Sin embargo, puede crear una [cuenta de evaluación gratuita de Azure](https://azure.microsoft.com/free/) en un par de minutos, lo que le permite crear un acelerador de soluciones.

### <a name="how-do-i-delete-an-aad-tenant"></a>¿Cómo se eliminan inquilinos de AAD?

Consulte la entrada del blog de Eric Golpe [Walkthrough of Deleting an Azure AD Tenant](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx) (Tutorial para la eliminación de inquilinos de Azure AD).

### <a name="next-steps"></a>Pasos siguientes

También puede explorar algunas de las demás características y funcionalidades de los aceleradores de soluciones de IoT:

* [Exploración de las funcionalidades del acelerador de la solución de supervisión remota](iot-suite-remote-monitoring-explore.md)
* [Introducción al acelerador de la solución de mantenimiento predictivo](iot-suite-predictive-overview.md)
* [Información general del acelerador de soluciones de factoría conectada](iot-suite-connected-factory-overview.md)
* [Seguridad de Internet de las cosas desde el principio](securing-iot-ground-up.md)