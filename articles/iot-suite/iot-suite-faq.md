---
title: "Preguntas más frecuentes sobre el conjunto de aplicaciones de IoT de Azure | Microsoft Docs"
description: "Preguntas más frecuentes sobre el Conjunto de aplicaciones de IoT"
services: 
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
ms.date: 09/15/2017
ms.author: corywink
ms.openlocfilehash: 5039dd30f85278b4e3bbba938ef40077af2c5c75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Preguntas más frecuentes sobre el Conjunto de aplicaciones de IoT

Consulte también, la sección [preguntas más frecuentes](iot-suite-faq-cf.md) de la fábrica conectada específica.

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>¿Dónde se puede encontrar el código fuente de la solución preconfigurada?

El código fuente se almacena en los siguientes repositorios de GitHub:
* [Solución preconfigurada de supervisión remota][lnk-remote-monitoring-github]
* [Solución preconfigurada de mantenimiento predictivo][lnk-predictive-maintenance-github]
* [Solución preconfigurada de fábrica conectada](https://github.com/Azure/azure-iot-connected-factory)

### <a name="how-do-i-update-to-the-latest-version-of-the-remote-monitoring-preconfigured-solution-that-uses-the-iot-hub-device-management-features"></a>¿Cómo puedo actualizar a la versión más reciente de la solución preconfigurada de supervisión remota que usa las características de administración de dispositivos de IoT Hub?

* Si implementa una solución preconfigurada desde el sitio https://www.azureiotsuite.com/, siempre se implementa una nueva instancia de la versión más reciente de la solución.
* Si implementa una solución preconfigurada mediante la línea de comandos, puede actualizar una implementación existente con nuevo código. Consulte [Implementación de nube][lnk-cloud-deployment] en el [repositorio][lnk-remote-monitoring-github] de GitHub.

### <a name="how-can-i-add-support-for-a-new-device-method-to-the-remote-monitoring-preconfigured-solution"></a>¿Cómo se puede agregar compatibilidad con un nuevo método de dispositivo a la solución preconfigurada de supervisión remota?

Consulte la sección [Add support for a new method to the simulator][lnk-add-method] (Adición de compatibilidad con un nuevo método al simulador) en el artículo [Personalizar una solución preconfigurada][lnk-customize].

### <a name="the-simulated-device-is-ignoring-my-desired-property-changes-why"></a>El dispositivo simulado está omitiendo los cambios de propiedades deseadas, ¿por qué?
En la solución preconfigurada de supervisión remota, el código de dispositivo simulado solo usa las propiedades deseadas **Desired.Config.TemperatureMeanValue** y **Desired.Config.TelemetryInterval** para actualizar las propiedades notificadas. Todos los demás cambios de propiedades deseadas se omiten.

### <a name="my-device-does-not-appear-in-the-list-of-devices-in-the-solution-dashboard-why"></a>El dispositivo no aparece en la lista de dispositivos en el panel de la solución, ¿por qué?

La lista de dispositivos en el panel de la solución usa una consulta para devolver la lista de dispositivos. Actualmente, una consulta no puede devolver más de 10 000 dispositivos. Intente que los criterios de búsqueda de su consulta sean más restrictivos.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>¿Cuál es la diferencia entre eliminar un grupo de recursos en el Portal de Azure y hacer clic en Eliminar en una solución preconfigurada en azureiotsuite.com?

* Si elimina la solución preconfigurada en [azureiotsuite.com][lnk-azureiotsuite], eliminará todos los recursos aprovisionados cuando se creó la solución preconfigurada. Si agrega recursos adicionales al grupo de recursos, dichos recursos también se eliminan. 
* Si elimina el grupo de recursos en [Azure Portal][lnk-azure-portal], solo se eliminan los recursos de ese grupo de recursos. También debe eliminar la aplicación de Azure Active Directory asociada a la solución preconfigurada en el [Portal de Azure clásico][lnk-classic-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>¿Cuántas instancias del Centro de IoT se pueden aprovisionar en una suscripción?

De forma predeterminada, puede aprovisionar [10 centros de IoT Hub por suscripción][link-azuresublimits]. Puede crear un [vale de soporte de Azure][link-azuresupportticket] para aumentar este límite. Como consecuencia, dado que todas las soluciones preconfiguradas aprovisionan una nueva instancia de IoT Hub, solo se pueden aprovisionar hasta 10 soluciones preconfiguradas en una suscripción determinada. 

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>¿Cuántas instancias de Azure Cosmos DB se pueden aprovisionar en una suscripción?

Cincuenta. Para aumentar este límite se puede crear una [incidencia de soporte técnico de Azure][link-azuresupportticket], pero de forma predeterminada solo se pueden aprovisionar 50 instancias de Cosmos DB por suscripción. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>¿Cuántas API de Mapas de Bing gratis se pueden aprovisionar en una suscripción?

Dos. Solo puede crear dos planes de Mapas de Bing para empresa de nivel 1 de transacciones internas en una suscripción de Azure. La solución de supervisión se aprovisiona de forma predeterminada con el plan de nivel 1 de transacciones internas. Como consecuencia, solo se pueden aprovisionar un máximo de dos soluciones de supervisión remota en una suscripción sin modificaciones.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>Tengo una implementación de soluciones de supervisión remota con un mapa estático, ¿cómo se puede agregar un mapa Bing interactivo?

1. Obtenga la QueryKey de Bing Maps API for Enterprise en [Azure Portal][lnk-azure-portal]: 
   
   1. Navegue al grupo de recursos donde está Bing Maps API for Enterprise en [Azure Portal][lnk-azure-portal].
   2. Haga clic en **Toda la configuración** y después en **Administración de claves**. 
   3. Verá dos claves: **MasterKey** y **QueryKey**. Copie el valor de **QueryKey**.
      
      > [!NOTE]
      > ¿No tiene una cuenta de la API de Mapas de Bing para empresa? Para crear una cuenta en el [Azure Portal][lnk-azure-portal] haga clic en + Nuevo, busque Bing Maps API for Enterprise y siga las indicaciones para crearla.
      > 
      > 
2. Descargue el código más reciente de [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].
3. Ejecute una implementación local o en la nube siguiendo las instrucciones de implementación de línea de comandos en la carpeta /docs/ del repositorio. 
4. Cuando haya ejecutado la implementación local o en la nube, busque en la carpeta raíz el archivo *.user.config creado durante la implementación. Abra este archivo en un editor de texto. 
5. Cambie la línea siguiente para incluir el valor copiado desde la clave **QueryKey**: 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>¿Puedo crear una solución preconfigurada si tengo Microsoft Azure para DreamSpark?

En este momento, no se puede crear una solución preconfigurada con una cuenta de [Microsoft Azure para DreamSpark][lnk-dreamspark]. Sin embargo, puede crear una [cuenta de evaluación gratuita de Azure][lnk-30daytrial] en un par de minutos, lo que le permite crear una solución preconfigurada.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>¿Puedo crear una solución preconfigurada si tengo alguna suscripción del Proveedor de soluciones en la nube (CSP)?

En este momento, no se puede crear una solución preconfigurada con una suscripción del Proveedor de soluciones en la nube (CSP). Sin embargo, puede crear una [cuenta de evaluación gratuita de Azure][lnk-30daytrial] en un par de minutos, lo que le permite crear una solución preconfigurada.

### <a name="how-do-i-delete-an-aad-tenant"></a>¿Cómo se eliminan inquilinos de AAD?

Consulte la entrada del blog de Eric Golpe [Walkthrough of Deleting an Azure AD Tenant][lnk-delete-aad-tennant] (Tutorial para la eliminación de inquilinos de Azure AD).

### <a name="next-steps"></a>Pasos siguientes

También puede explorar algunas de las demás características y funcionalidades de las soluciones preconfiguradas del conjunto de aplicaciones de IoT:

* [Información general de la solución preconfigurada de mantenimiento predictivo][lnk-predictive-overview]
* [Introducción a la solución preconfigurada de fábrica conectada](iot-suite-connected-factory-overview.md)
* [Seguridad de Internet de las cosas desde el principio][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx
[lnk-cloud-deployment]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
[lnk-add-method]: iot-suite-guidance-on-customizing-preconfigured-solutions.md#add-support-for-a-new-method-to-the-simulator
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-predictive-maintenance-github]: https://github.com/Azure/azure-iot-predictive-maintenance
