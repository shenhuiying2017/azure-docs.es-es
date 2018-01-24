---
title: "Compilación de una solución de administración en OMS | Microsoft Docs"
description: "Las soluciones de administración amplían la funcionalidad de Operations Management Suite (OMS) proporcionando escenarios de administración empaquetados que los clientes pueden agregar a su área de trabajo de OMS.  En este artículo se proporciona información sobre cómo crear soluciones de administración que se pueden usar en su propio entorno o ponerse a disposición de sus clientes."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a17c73393ecbdff693e9b200d1506887e0f1d71e
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="design-and-build-a-management-solution-in-operations-management-suite-oms-preview"></a>Diseño y compilación de una solución de administración en Operations Management Suite (OMS) (versión preliminar)
> [!NOTE]
> La versión de la documentación para crear soluciones de administración de OMS está actualmente en fase preliminar. Cualquier esquema descrito a continuación está sujeto a cambios.

Las [soluciones de administración](operations-management-suite-solutions.md) amplían la funcionalidad de Operations Management Suite (OMS) proporcionando escenarios de administración empaquetados que los clientes pueden agregar a su área de trabajo de OMS.  Este artículo presenta un proceso básico para diseñar y compilar una solución de administración que es adecuada para los requisitos más comunes.  Si no está familiarizado con la creación de soluciones de administración puede utilizar este proceso como punto de partida, y luego aprovechar los conceptos para soluciones más complejas a medida que sus requisitos evolucionan.

## <a name="what-is-a-management-solution"></a>¿Qué es una solución de administración?

Las soluciones de administración contienen OMS y recursos de Azure que funcionan conjuntamente para lograr un escenario de supervisión determinado.  Se implementan como [plantillas de administración de recursos](../azure-resource-manager/resource-manager-template-walkthrough.md) que contienen detalles de cómo instalar y configurar sus recursos contenidos cuando se instala la solución.

La estrategia básica consiste en iniciar la solución de administración mediante la creación de los componentes individuales en el entorno de Azure.  Una vez que tenga la funcionalidad funcionando correctamente, puede empezar a empaquetarlos en un [archivo de solución de administración](operations-management-suite-solutions-solution-file.md). 


## <a name="design-your-solution"></a>Diseño de la solución
El modelo más común para una solución de administración se muestra en el diagrama siguiente.  Los distintos componentes en este modelo se describen a continuación.

![Información general de la solución OMS](media/operations-management-suite-solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Orígenes de datos
El primer paso para diseñar una solución es determinar los datos que necesita del repositorio de Log Analytics.  Los datos se pueden recopilar mediante un [origen de datos](../log-analytics/log-analytics-data-sources.md) u [otra solución](operations-management-suite-solutions.md), o puede que la solución tenga que proporcionar el proceso para recopilarlos.

Hay varias maneras de recopilar orígenes de datos en el repositorio de Log Analytics, como se describe en [Orígenes de datos en Log Analytics](../log-analytics/log-analytics-data-sources.md).  Esto incluye los eventos en el registro de eventos de Windows o los generados por Syslog, además de los contadores de rendimiento para los clientes de Windows y Linux.  También puede recoger datos de recursos de Azure recopilados por Azure Monitor.  

Si necesita datos que no son accesibles a través de cualquiera de los orígenes de datos disponibles, puede usar la [API del recopilador de datos HTTP](../log-analytics/log-analytics-data-collector-api.md) que le permite escribir datos en el repositorio de Log Analytics desde cualquier cliente que pueda llamar a una API de REST.  La forma más común de recopilación de datos personalizados en una solución de administración consiste en crear un [runbook en Azure Automation](../automation/automation-runbook-types.md) que recopila los datos necesarios de los recursos de Azure o de recursos externos, y utiliza la API del recopilador de datos para escribir en el repositorio.  

### <a name="log-searches"></a>Búsqueda de registros
Las [búsquedas de registros](../log-analytics/log-analytics-log-searches.md) se usan para extraer y analizar datos en el repositorio de Log Analytics.  Las utilizan vistas y alertas, y además permiten al usuario realizar análisis ad hoc de los datos en el repositorio.  

Debe definir las consultas que cree que le resultarán útiles para el usuario, incluso si no hay vistas o alertas que las utilicen.  Estas estarán disponibles para los usuarios como búsquedas guardadas en el portal y también se pueden incluir en un [elemento de visualización Lista de consultas](../log-analytics/log-analytics-view-designer-parts.md#list-of-queries-part) en la vista personalizada.

### <a name="alerts"></a>Alertas
[Alertas de Log Analytics](../log-analytics/log-analytics-alerts.md) identifican los problemas a través de [búsquedas de registros](#log-searches) con los datos en el repositorio.  Estas alertas o bien envían una notificación al usuario o ejecutan automáticamente una acción en respuesta. Debe identificar las diferentes condiciones de alerta para la aplicación e incluir reglas de alerta correspondientes en el archivo de solución.

Si el problema potencialmente puede corregirse con un proceso automatizado, entonces usted normalmente creará un runbook en Azure Automation para realizar esta corrección.  La mayoría de los servicios de Azure pueden administrarse con [cmdlets](/powershell/azure/overview) que el runbook aprovechará para realizar esta funcionalidad.

Si la solución requiere una funcionalidad externa en respuesta a una alerta, puede usar una [respuesta webhook](../log-analytics/log-analytics-alerts-actions.md).  Esto le permite llamar a un servicio web externo enviando información de la alerta.

### <a name="views"></a>Vistas
Las vistas de análisis de Log Analytics se usan para visualizar los datos del repositorio de Log Analytics.  Cada solución normalmente contendrá una sola vista con un [icono](../log-analytics/log-analytics-view-designer-tiles.md) que se muestra en el panel principal del usuario.  La vista puede contener cualquier número de [elementos de visualización](../log-analytics/log-analytics-view-designer-parts.md) para proporcionar al usuario diferentes visualizaciones de los datos recopilados.

[Cree vistas personalizadas usando el Diseñador de vistas](../log-analytics/log-analytics-view-designer.md) que más adelante puede exportar para su inclusión en el archivo de solución.  


## <a name="create-solution-file"></a>Creación del archivo de solución
Una vez que haya configurado y probado los componentes que van a formar parte de la solución, puede [crear el archivo de solución](operations-management-suite-solutions-solution-file.md).  Para ello implementará los componentes de la solución en un [plantilla de Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) que incluye un [recurso de solución](operations-management-suite-solutions-solution-file.md#solution-resource) con relaciones con los otros recursos en el archivo.  


## <a name="test-your-solution"></a>Prueba de la solución
Mientras se desarrolla la solución, tiene que instalarla y probarla en el área de trabajo.  Puede hacerlo usando cualquiera de los métodos disponibles para [probar e instalar plantillas de Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="publish-your-solution"></a>Publicación de la solución
Una vez haya finalizado y probado la solución, puede ponerla a disposición de los clientes a través de los siguientes orígenes.

- **Plantillas de inicio rápido de Azure**.  [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/) es un conjunto de plantillas de Resource Manager aportadas por la comunidad a través de GitHub.  Puede hacer que su solución esté disponible siguiendo la información en la [guía de contribución](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **Azure Marketplace**.  [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) le permite distribuir y vender su solución a otros desarrolladores, fabricantes de software independientes y profesionales de TI.  Puede obtener información sobre cómo publicar la solución en Azure Marketplace en [Publicación y administración de una oferta en Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).



## <a name="next-steps"></a>pasos siguientes
* Obtenga información acerca de cómo [crear un archivo de solución](operations-management-suite-solutions-solution-file.md) para la solución de administración.
* Obtenga más información en [Creación de plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Búsqueda de [plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates) para obtener ejemplos de diferentes plantillas de Resource Manager.