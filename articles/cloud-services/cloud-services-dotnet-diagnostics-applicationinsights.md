---
title: Solucionar problemas de Cloud Services con Application Insights | Microsoft Docs
description: "Aprenda a solucionar problemas del servicio en la nube mediante el uso de Application Insights para procesar datos de Diagnósticos de Azure."
services: cloud-services
documentationcenter: .net
author: sbtron
manager: timlt
editor: tysonn
ms.assetid: e93f387b-ef29-4731-ae41-0676722accb6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: saurabh
ms.openlocfilehash: 4001ca908ff00b1a40829d687589080e9b07b18a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshoot-cloud-services-using-application-insights"></a>Solucionar problemas de Servicios en la nube con Application Insights
Con [Azure SDK 2.8](https://azure.microsoft.com/downloads/) y la extensión 1.5 de Diagnósticos de Azure puede enviar los datos de Diagnósticos de Azure para su servicio en la nube directamente a Application Insights. Los registros recopilados por Azure Diagnostics, incluidos los registros de aplicaciones, los registros de eventos de Windows, los registros de ETW y los contadores de rendimiento, pueden enviarse a Application Insights. Después podrá visualizar esta información en la interfaz de usuario del portal de Application Insights. Podrá usar el SDK de Application Insights para obtener información sobre las métricas y los registros de la aplicación, y sobre los datos del sistema y de la infraestructura procedentes de Diagnósticos de Azure.

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Configuración de Diagnósticos de Azure para enviar datos a Application Insights
Siga estos pasos para configurar el proyecto del servicio en la nube para enviar datos de Diagnósticos de Azure a Application Insights.

1. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en un rol y seleccione **Propiedades** para abrir el diseñador de roles.

    ![Propiedades de rol del Explorador de soluciones][1]

2. En la sección **Diagnósticos** del diseñador de roles, seleccione la opción **Enviar datos de diagnóstico a Application Insights**.

    ![Diseñador de roles Enviar datos de diagnóstico a application insights][2]

3. En el cuadro de diálogo que aparece, seleccione el recurso de Application Insights al que le gustaría enviar los datos de Diagnósticos de Azure. El cuadro de diálogo le permite seleccionar un recurso de Application Insights existente en la suscripción o especificar manualmente una clave de instrumentación para un recurso de Application Insights. Para más información sobre la creación de un recurso de Application Insights, consulte [Creación de recursos en Application Insights](../application-insights/app-insights-create-new-resource.md).

    ![seleccionar recurso de application insights][3]

    Cuando haya agregado el recurso de Application Insights, la clave de instrumentación para ese recurso se almacenará como un valor de configuración de servicio con el nombre **APPINSIGHTS_INSTRUMENTATIONKEY**. Puede cambiar esta opción para cada configuración de servicio o de entorno. Para ello, seleccione una configuración diferente de la lista **Configuración del servicio** y especifique una nueva clave de instrumentación para esa configuración.

    ![seleccionar configuración de servicio][4]

    Visual Studio usa el valor de configuración **APPINSIGHTS_INSTRUMENTATIONKEY** para configurar la extensión de diagnósticos con la información de recursos apropiada de Application Insights durante la publicación. El valor de configuración es una manera cómoda de definir claves de instrumentación diferentes para distintas configuraciones del servicio. Visual Studio traducirá ese valor y lo insertará en la configuración pública de la extensión de diagnósticos en durante la publicación. Para simplificar el proceso de configuración de la extensión de diagnósticos con PowerShell, la salida del paquete de Visual Studio también contiene el XML de configuración público con la clave de instrumentación de Application Insights correspondiente. Los archivos de configuración públicos se crean en la carpeta Extensiones y siguen el patrón *PaaSDiagnostics.&lt;NombreDelRol&gt;.PubConfig.xml*. Cualquier implementación basada en PowerShell puede usar este patrón para asignar cada configuración a un rol.

4) Al habilitar **Enviar datos de diagnóstico a Application Insights** configurará automáticamente Diagnósticos de Azure para enviar todos los contadores de rendimiento y registros de nivel de error que recopila el agente de Diagnósticos de Azure a Application Insights. 

    Si desea configurar qué datos se envían a Application Insights, debe editar manualmente el archivo *diagnostics.wadcfgx* para cada rol. Consulte [Configuración de Diagnósticos de Azure para enviar datos a Application Insights](#configure-azure-diagnostics-to-send-data-to-application-insights) para obtener más información sobre cómo actualizar manualmente la configuración.

Una vez configurado el servicio en la nube para enviar datos de Diagnósticos de Azure a Application Insights, puede implementarlo en Azure como lo haría normalmente, asegurándose de que la extensión de Diagnósticos de Azure está habilitada. Para más información, consulte el artículo de [Publicación de un servicio en la nube con Visual Studio](../vs-azure-tools-publishing-a-cloud-service.md).  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>Visualización de datos de Diagnósticos de Azure en Application Insights
La telemetría de Diagnósticos de Azure aparecerá en el recurso de Application Insights configurado para el servicio en la nube.

Los distintos tipos de registro de Diagnósticos de Azure se asignan a los conceptos de Application Insights de las maneras siguientes:

* Los contadores de rendimiento se muestran como métricas personalizadas en Application Insights.
* Los registros de eventos de Windows se muestran como seguimientos y eventos personalizados en Application Insights.
* Los registros de aplicación, los registros de ETW y los registros de infraestructura de diagnóstico se muestran como seguimientos en Application Insights.

Para ver de datos de Diagnósticos de Azure en Application Insights, tiene las siguientes opciones:

* Use el [Explorador de métricas](../application-insights/app-insights-metrics-explorer.md) para visualizar los contadores de rendimiento personalizados o los recuentos de diferentes tipos de eventos del registro de eventos de Windows.

    ![Métricas personalizadas en el Explorador de métricas][5]

* Use [Búsqueda](../application-insights/app-insights-diagnostic-search.md) para buscar en los registros de seguimiento enviados por Diagnósticos de Azure. Por ejemplo, si una excepción no controlada ha provocado el bloqueo de un rol y el reciclado, la información sobre la excepción aparece en el canal *Aplicación* del *registro de eventos de Windows*. Puede utilizar la funcionalidad de búsqueda para ver el error del registro de eventos de Windows y obtener el seguimiento de la pila completo de la excepción para encontrar la causa del problema.

    ![Buscar seguimientos][6]

## <a name="next-steps"></a>Pasos siguientes
* [Agregar el SDK de Application Insights a su servicio en la nube](../application-insights/app-insights-cloudservices.md) para enviar datos acerca de solicitudes, excepciones, dependencias y cualquier telemetría personalizada desde la aplicación. Junto con los datos de Diagnósticos de Azure puede obtener una vista completa de la aplicación y el sistema en el mismo recurso de Application Insights.  

<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png
