---
title: "Preguntas más frecuentes sobreAzure IoT Suite | Microsoft Docs"
description: "Preguntas más frecuentes sobre el Conjunto de aplicaciones de IoT"
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
ms.openlocfilehash: c79c90c4f6c28153d4d299015a06a6bc37145081
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Preguntas más frecuentes sobre el Conjunto de aplicaciones de IoT

Consulte también [Preguntas más frecuentes específicas sobre fábricas conectadas](iot-suite-faq-cf.md) y [Preguntas más frecuentes específicas sobre supervisión remota](iot-suite-faq-rm-v2.md).

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>¿Dónde se puede encontrar el código fuente de la solución preconfigurada?

El código fuente se almacena en los siguientes repositorios de GitHub:

* [Solución preconfigurada de supervisión remota (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Solución preconfigurada de supervisión remota (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Solución preconfigurada de mantenimiento predictivo](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Solución preconfigurada de fábrica conectada](https://github.com/Azure/azure-iot-connected-factory)

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-preconfigured-solutions"></a>¿La nueva arquitectura de microservicios está disponible para las tres soluciones preconfiguradas?

Actualmente, solo la solución de supervisión remota usa la arquitectura de microservicios, ya que abarca el escenario más amplio.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>¿Qué ventajas proporciona en la nueva actualización la nueva arquitectura basada en microservicios de código abierto?

En los dos últimos años, la arquitectura de nube ha evolucionado en gran medida. Los microservicios han surgido como un modelo excelente para lograr el escalado y la flexibilidad, sin sacrificar la velocidad de desarrollo. Este modelo de arquitectura se usa internamente en varios servicios de Microsoft con grandes resultados con respecto a la escalabilidad y la confiabilidad. Estamos poniendo en práctica estos resultados para que nuestros clientes se beneficien de ellos.

### <a name="is-the-new-preconfigured-solution-available-in-the-same-geographic-region-as-the-existing-solution"></a>¿Está disponible la nueva solución preconfigurada en la misma región geográfica que la solución existente?

Sí, la nueva supervisión remota está disponible en las mismas regiones geográficas.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>¿Cuál es la diferencia entre eliminar un grupo de recursos en el Portal de Azure y hacer clic en Eliminar en una solución preconfigurada en azureiotsuite.com?

* Si elimina la solución preconfigurada en [azureiotsuite.com](https://www.azureiotsuite.com/), elimina todos los recursos aprovisionados cuando se creó la solución preconfigurada. Si agrega recursos adicionales al grupo de recursos, dichos recursos también se eliminan.
* Si elimina el grupo de recursos en [Azure Portal](https://portal.azure.com), solo se eliminan los recursos de ese grupo de recursos. También debe eliminar la aplicación de Azure Active Directory asociada a la solución preconfigurada.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-suite"></a>¿Puedo seguir aprovechando mi inversión en Azure IoT Suite?

Sí. Cualquier solución que exista en la actualidad continúa funcionando en su suscripción de Azure y el código fuente sigue estando disponible en GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>¿Cuántas instancias de IoT Hub se pueden aprovisionar en una suscripción?

De forma predeterminada, puede aprovisionar [diez centros de IoT Hub por suscripción](../azure-subscription-service-limits.md#iot-hub-limits). Puede crear un [vale de soporte de Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar este límite. Como consecuencia, dado que todas las soluciones preconfiguradas aprovisionan una nueva instancia de IoT Hub, solo se pueden aprovisionar hasta 10 soluciones preconfiguradas en una suscripción determinada.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>¿Cuántas instancias de Azure Cosmos DB se pueden aprovisionar en una suscripción?

Cincuenta. Puede crear un [vale de soporte de Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar este límite, pero de forma predeterminada solo se pueden aprovisionar 50 instancias de Cosmos DB por suscripción.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>¿Cuántas API de Mapas de Bing gratis se pueden aprovisionar en una suscripción?

Dos. Solo puede crear dos planes de Mapas de Bing para empresa de nivel 1 de transacciones internas en una suscripción de Azure. La solución de supervisión se aprovisiona de forma predeterminada con el plan de nivel 1 de transacciones internas. Como consecuencia, solo se pueden aprovisionar un máximo de dos soluciones de supervisión remota en una suscripción sin modificaciones.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>¿Puedo crear una solución preconfigurada si tengo Microsoft Azure para DreamSpark?

> [!NOTE]
> Microsoft Azure para DreamSpark ahora se conoce como Microsoft Imagine para estudiantes.

En este momento, no se puede crear una solución preconfigurada con una cuenta de [Microsoft Azure para DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/). Sin embargo, puede crear una [cuenta de evaluación gratuita de Azure](https://azure.microsoft.com/free/) en un par de minutos, lo que le permite crear una solución preconfigurada.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>¿Puedo crear una solución preconfigurada si tengo alguna suscripción del Proveedor de soluciones en la nube (CSP)?

En este momento, no se puede crear una solución preconfigurada con una suscripción del Proveedor de soluciones en la nube (CSP). Sin embargo, puede crear una [cuenta de evaluación gratuita de Azure](https://azure.microsoft.com/free/) en un par de minutos, lo que le permite crear una solución preconfigurada.

### <a name="how-do-i-delete-an-aad-tenant"></a>¿Cómo se eliminan inquilinos de AAD?

Consulte la entrada del blog de Eric Golpe [Walkthrough of Deleting an Azure AD Tenant](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx) (Tutorial para la eliminación de inquilinos de Azure AD).

### <a name="next-steps"></a>pasos siguientes

También puede explorar algunas de las demás características y funcionalidades de las soluciones preconfiguradas del conjunto de aplicaciones de IoT:

* [Exploración de las funcionalidades de la solución preconfigurada de supervisión remota](iot-suite-remote-monitoring-explore.md)
* [Información general de la solución preconfigurada de mantenimiento predictivo](iot-suite-predictive-overview.md)
* [Introducción a la solución preconfigurada de fábrica conectada](iot-suite-connected-factory-overview.md)
* [Seguridad de Internet de las cosas desde el principio](securing-iot-ground-up.md)