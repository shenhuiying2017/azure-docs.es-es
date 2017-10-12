---
title: "Integración con Operations Management Suite (OMS) | Microsoft Docs"
description: "Además de usar las funciones estándar de OMS, puede integrarlas con otras aplicaciones de administración y servicios para proporcionar un entorno de administración híbrido, proporcionar escenarios de administración personalizados únicos para su entorno o proporcionar una experiencia de administración personalizada para sus clientes.  Este artículo proporciona una visión general de las diferentes opciones para la integración con OMS y vínculos a artículos que proporcionan información técnica detallada."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: fc5f3a8a-77f7-4103-bd7e-744c15ffcca7
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 7a24df6f2c3b2c091d1b66b8b9c0a61035ffde11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="integrating-with-operations-management-suite-oms"></a>Integración con Operations Management Suite (OMS)
Operations Management Suite es la solución de administración de TI basada en la nube de Microsoft que le ayuda a administrar y proteger su infraestructura local y en la nube.  Además de usar las funciones estándar de OMS, puede integrarlas con otras aplicaciones de administración y servicios para proporcionar un entorno de administración híbrido, proporcionar escenarios de administración personalizados únicos para su entorno o proporcionar una experiencia de administración personalizada para sus clientes.  Este artículo proporciona una visión general de las diferentes opciones para la integración con vínculos y servicios de OMS que proporcionan información técnica detallada. 

## <a name="log-analytics"></a>Log Analytics
Todos los datos de administración que recopila Log Analytics se almacenan en un repositorio que se hospeda en Azure.  Todos los datos almacenados en el repositorio están disponible en las búsquedas de registro que proporcionan un análisis rápido a través de grandes cantidades de datos.  Los requisitos de integración pueden para llenar el repositorio con nuevos datos que están disponibles para el análisis, o para extraer los datos en el repositorio para proporcionar una visualización nueva o para integrarla con otra herramienta de administración.

Los datos del repositorio se almacenan como un registro.  Al completar el repositorio, debe proporcionar a los usuarios el tipo de registro que usa la solución y una descripción de sus propiedades.  Cuando se recuperan datos, necesitará esta información acerca de los datos con los que están trabajando.

![Completado del repositorio OMS](media/operations-management-suite-integration/repository.png)

### <a name="populate-the-log-analytics-repository"></a>Completo del repositorio de Log Analytics
Existen varios métodos para rellenar el repositorio OMS.  El método que utiliza dependerá de factores como dónde se encuentran los datos de origen, el formato de los datos y qué clientes necesita admitir.  Una vez que los datos se almacenan en el repositorio, no supone ninguna diferencia cómo se recopiló.

Las secciones siguientes describen las diferentes opciones para completar el repositorio OMS.

#### <a name="connected-sources-and-data-sources"></a>Orígenes de datos y orígenes conectados
Los orígenes conectados son las ubicaciones donde se pueden recuperar los datos para el repositorio OMS.  Los orígenes de datos y las soluciones se ejecutan en orígenes conectados y definen los datos específicos que se recopilan.  Si su aplicación escribe datos en uno de estos orígenes de datos, puede recopilarlos mediante la configuración del origen de datos.  Por ejemplo, si la aplicación crea eventos Syslog, pueden recopilarse mediante el origen de datos de Syslog en un agente de Linux.

* [Orígenes de datos en Log Analytics](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>Soluciones
Las soluciones amplían las funcionalidades de OMS.  Una solución puede recopilar datos del origen conectado o puede realizar un análisis en sobre los registros que ya se han recopilado en el repositorio.  Cada solución proporcionada por Microsoft tiene un artículo individual que proporciona los detalles sobre los datos que recopila.

* [Soluciones en Log Analytics](../log-analytics/log-analytics-add-solutions.md)

#### <a name="http-data-collector-api"></a>API del recopilador de datos HTTP
La API del recopilador de datos HTTP de Log Analytics es una API de REST que le permite agregar datos JSON en el repositorio de Log Analytics.  Puede aprovechar esta API cuando tiene una aplicación que no proporciona datos a través de uno de los otros orígenes de datos o soluciones.  Puede utilizarse para completar el repositorio desde cualquier cliente que puede llamar a la API y no se basa en la programación de la colección de cualquier origen de datos o solución.

* [API del recopilador de datos HTTP de Log Analytics](../log-analytics/log-analytics-data-collector-api.md)

### <a name="retrieve-data-from-the-log-analytics-repository"></a>Recuperación de datos del repositorio de Log Analytics
Existen varios métodos para recuperar los datos del repositorio de OMS.  Puede que desee que los usuarios recuperen datos mediante la consola OMS y proporcionarles distintos tipos de análisis y visualizaciones.  También puede recuperar los datos de un proceso externo, como otra solución de administración.

#### <a name="log-searches"></a>Búsqueda de registros
Todos los datos almacenados en el repositorio OMS están disponible a través de búsquedas de registros.  Los usuarios pueden realizar su propio análisis ad-hoc en la consola OMS o crear un panel con una visualización para una búsqueda de registro concreta.  Las soluciones pueden contener vistas personalizadas con visualizaciones basadas en búsquedas predefinidas.  Puede utilizar la API de búsqueda de registro para obtener acceso a los datos en el repositorio OMS desde una herramienta de administración o la aplicación externa.  

* [Búsquedas de registros en Log Analytics](../log-analytics/log-analytics-log-searches.md)
* [API de REST de búsqueda de registros de Log Analytics](../log-analytics/log-analytics-log-search-api.md)
* [Cmdlets deLog Analytics](https://msdn.microsoft.com/library/mt188224.aspx)

#### <a name="custom-views"></a>Vistas personalizadas
El Diseñador de vistas permite crear vistas personalizadas en la consola OMS que proporciona a los usuarios la visualización y el análisis de los datos en la solución.  Cada vista incluye un icono que se muestra en la página principal de la consola y cualquier número de elementos de visualización que se basan en las búsquedas de registro que define.

* [Diseñador de vistas de Log Analytics](../log-analytics/log-analytics-view-designer.md)

#### <a name="power-bi"></a>Power BI
Log Analytics puede exportar automáticamente datos desde el repositorio de OMS a Power BI con lo que podrá aprovechar sus herramientas de análisis y visualizaciones.  Realiza esta exportación en una programación, por lo que los datos se mantienen actualizados. 

* [Exportación de datos de Log Analytics a Power BI](../log-analytics/log-analytics-powerbi.md)

## <a name="automation"></a>Automatización
OMS puede automatizar los procesos para reaccionar a los datos recopilados o realizar otras funciones de administración.  Puede recopilar datos de la aplicación e insertarlos en el repositorio OMS, o puede automatizar la corrección de un problema conocido en respuesta a los datos que se encuentran en el repositorio. 

![Automatización](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Runbooks
Los runbooks en Azure Automation ejecutan flujos de trabajo y scripts de PowerShell en la nube de Azure.  Puede usarlos para administrar recursos de Azure o cualquier otro recurso que puede obtenerse de la nube.  También se pueden ejecutar runbooks en un centro de datos local con Hybrid Runbook Worker.  Puede iniciar un runbook desde Azure Portal o desde procesos externos mediante una serie de métodos como PowerShell o Automation API.

* [Inicio de un runbook en Azure Automation](../automation/automation-starting-a-runbook.md)
* [Cmdlets de Azure Automation](https://msdn.microsoft.com/library/dn690262.aspx)
* [API de REST de Automation](https://msdn.microsoft.com/library/mt662285.aspx)
* [Automation .NET](https://msdn.microsoft.com//library/mt465763.aspx)

### <a name="alerts"></a>Alertas
Las reglas de alerta ejecutan automáticamente búsquedas de registros según una programación.  Si los resultados coinciden con determinados criterios, la alerta resultante puede iniciar un runbook en Azure Automation o llamar a un webhook que puede iniciar un proceso externo.  Dos de estas respuestas pueden incluir detalles de la alerta que incluye los datos devueltos en la búsqueda de registros.

* [Alertas de Log Analytics](../log-analytics/log-analytics-alerts.md)
* [API de alertas de Log Analytics](../log-analytics/log-analytics-api-alerts.md)

## <a name="backup-and-site-recovery"></a>Backup y Site Recovery
Azure Backup y Site Recovery proporcionan servicios para la protección de datos de empresa y asegurar la disponibilidad de servidores y aplicaciones.  Puede aprovechar estos servicios para realizar operaciones como proporcionar servicios de copia de seguridad de la aplicación o iniciar una conmutación por error de una máquina virtual.

* [Cmdlets de Azure Backup](https://msdn.microsoft.com/library/mt619253.aspx)
* [API de REST de Azure Site Recovery](https://msdn.microsoft.com/library/azure/mt750497.aspx)
* [Cmdlets de Azure Site Recovery](https://msdn.microsoft.com/library/mt637930.aspx)

## <a name="custom-solutions"></a>Soluciones personalizadas
Puede encapsular la lógica de integración en una solución personalizada para ejecutarla en el área de trabajo o en el área de trabajo del cliente.  La solución puede incluir cualquiera de los métodos de integración en este artículo, además de otros recursos para proporcionar un escenario de administración completa.  Los recursos de la solución se empaquetan de forma que cuando se quita la solución, se quitan todos los recursos que creó del área de trabajo OMS y la suscripción de Azure.

Por ejemplo, la solución podría incluir un runbook de Automation para recopilar y procesar los datos y, a continuación, completar el repositorio de Log Analytics mediante la API de recopilador de datos HTTP.  También puede incluir una vista personalizada que presenta y analiza los datos recopilados.  

* Creación de soluciones personalizadas (próximamente)    

## <a name="next-steps"></a>Pasos siguientes
* Consulte el [SDK de OMS](operations-management-suite-sdk.md) para obtener información técnica sobre la automatización de servicios OMS.  

