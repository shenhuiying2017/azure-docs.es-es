---
title: Servicios compatibles con Resource Manager | Microsoft Docs
description: Describe los proveedores de recursos que admiten el Administrador de recursos, sus esquemas y las versiones disponibles de API y las regiones que pueden hospedar los recursos.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2016
ms.author: magoedte;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: dabe7d9796ab24a257ea904bc5d978cb71d7e149
ms.openlocfilehash: af96c2b6063f7200cdeb6b51e5c729aa4643127f


---
# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>Proveedores, regiones, versiones de API y esquemas del Administrador de recursos
El Administrador de recursos de Azure le proporciona una nueva manera de implementar y administrar los servicios que conforman sus aplicaciones. La mayoría de los servicios, aunque no todos, admiten el Administrador de recursos y algunos lo admiten solo parcialmente. Este tema proporciona una lista de proveedores de recursos admitidos para el Administrador de recursos de Azure.

Al implementar sus recursos, también debe saber qué regiones admiten esos recursos y qué versiones de API están disponibles para los recursos. En la sección [Regiones admitidas](#supported-regions) se muestra cómo averiguar qué regiones funcionarán para su suscripción y recursos. En la sección [Versiones de API admitidas](#supported-api-versions) se muestra cómo determinar las versiones de API que puede usar.

Para ver los servicios que se admiten en el Portal de Azure y el Portal clásico, consulte el [gráfico de disponibilidad del Portal de Azure](https://azure.microsoft.com/features/azure-portal/availability/). Para ver los servicios que admiten el traslado de recursos, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](resource-group-move-resources.md).

En las tablas siguientes se muestra qué servicios de Microsoft admiten la implementación y administración a través de Resource Manager y cuáles no. El vínculo de la columna **Plantillas de inicio rápido** envía una consulta al repositorio de plantillas de inicio rápido de Azure para el proveedor de recursos especificado. Las plantillas de inicio rápido se agregan y se actualizan con frecuencia. La presencia de un vínculo para un servicio concreto no significa necesariamente que la consulta devolverá las plantillas del repositorio. También hay muchos proveedores de recursos de terceros compatibles con Resource Manager. Obtenga información sobre cómo ver todos los proveedores de recursos en la sección [Tipos y proveedores de recursos](#resource-providers-and-types).

## <a name="compute"></a>Proceso
| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| --- | --- | --- | --- | --- |
| Lote |Sí |[REST de Lote](/rest/api/batchservice) |[Esquema de Batch](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-12-01/Microsoft.Batch.json) | |
| Contenedor |Sí |[REST de Servicio de contenedor](/rest/api/containerregistry) |[Esquema de contenedor](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.ContainerService.json) |[Microsoft.ContainerService](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ContainerService%22&type=Code) |
| Dynamics Lifecycle Services |Sí | | | |
| Conjuntos de escala |Sí |[REST de Conjunto de escala](/rest/api/compute/virtualmachinescalesets) |[Esquema de conjunto de escalado](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |[virtualMachineScaleSets](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=virtualMachineScaleSets&type=Code) |
| Service Fabric |Sí |[Rest de Service Fabric](/rest/api/servicefabric) | [Esquema de Service Fabric](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-09-01/Microsoft.ServiceFabric.json) |[Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Máquinas virtuales |Sí |[VM REST](/rest/api/compute/virtualmachines) |[Esquema de VM](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |[virtualMachines](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%2Fvirtualmachines%22&type=Code) |
| Máquinas virtuales (clásicas) |Limitado |- |- |- |
| RemoteApp |No |- |- |- |
| Servicios en la nube (clásico) |Limitado (consúltese a continuación) |- |- |- |

Máquinas virtuales (clásicas) hace referencia a recursos que se implementaron mediante el modelo de implementación clásica, en lugar de a través del modelo de implementación del Administrador de recursos. En general, estos recursos no admiten las operaciones del Administrador de recursos, pero hay algunas operaciones que se han habilitado. Para obtener más información sobre estos modelos de implementación, vea [Descripción de la implementación del Administrador de recursos y la implementación clásica](resource-manager-deployment-model.md). 

Cloud Services (clásico) se puede usar con otros recursos clásicos. Sin embargo, los recursos clásicos no aprovechan todas las características de Resource Manager y no son una buena opción para las soluciones futuras. En su lugar, considere la posibilidad de cambiar la infraestructura de aplicaciones para usar recursos de los espacios de nombres Microsoft.Compute, Microsoft.Storage y Microsoft.Network.

## <a name="networking"></a>Redes
| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| --- | --- | --- | --- | --- |
| Puerta de enlace de aplicaciones |Sí |[REST de Puerta de enlace de aplicaciones](https://msdn.microsoft.com/library/azure/mt684939.aspx) | |[applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS |Sí |[DNS REST](/rest/api/dns) |[Esquema de DNS](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Network.json) |[dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| ExpressRoute |Sí |[REST de ExpressRoute](https://msdn.microsoft.com/library/azure/mt586720.aspx) | |[expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |
| Equilibrador de carga |Sí |[REST del Equilibrador de carga](https://msdn.microsoft.com/library/azure/mt163651.aspx) |[Esquema de Load Balancer](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |[loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Administrador de tráfico |Sí |[REST para Administrador de tráfico](https://msdn.microsoft.com/library/azure/mt163667.aspx) |[Esquema de Traffic Manager](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json) |[trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| Redes virtuales |Sí |[REST para Virtual Network](https://msdn.microsoft.com/en-us/library/azure/mt163650.aspx) |[Esquema de red virtual](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |[virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| Puerta de enlace de VPN |Sí |[REST de puerta de enlace de red](https://msdn.microsoft.com/library/azure/mt163859.aspx) | |[virtualNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworkGateways%22&type=Code) <br /> [localNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FlocalNetworkGateways%22&type=Code) <br />[connections](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Fconnections%22&type=Code) |

## <a name="storage"></a>Almacenamiento
| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| --- | --- | --- | --- | --- | --- |
| Almacenamiento |Sí |[API de Almacenamiento](/rest/api/storagerp) |[Cuenta de almacenamiento](resource-manager-template-storage.md) |[Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| StorSimple |Sí | | | |

## <a name="databases"></a>Bases de datos
| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| --- | --- | --- | --- | --- | --- |
| DocumentDB |Sí |[REST de DocumentDB](/rest/api/documentdbresourceprovider) |[Esquema de DocumentDB](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json) |[Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Caché en Redis |Sí | [API de REST de Redis Cache](/rest/api/redis) |[Esquema de Redis](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Cache.json) |[Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| SQL Database |Sí |[REST de Base de datos SQL](/rest/api/sql) |[Esquema de SQL Database](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) |[Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| Almacenamiento de datos SQL |Sí | | | |

## <a name="web--mobile"></a>Web y móvil
| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| --- | --- | --- | --- | --- |
| Aplicaciones de API |Sí | [REST de App Service](/rest/api/appservice) |[Esquema de API Apps](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[Aplicaciones de API](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| Administración de API |Sí |[REST de Administración de API](/rest/api/apimanagement) |[Esquema de API Management](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-07-07/Microsoft.ApiManagement.json) |[Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) |
| Content Moderator |Sí | | | |
| Function App |Sí | [REST de Function App](/rest/api/appservice) | |[functionApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22functionApp%22&type=Code) |
| Logic Apps |Sí |[REST de Logic Apps](/rest/api/logic) |[Esquema de Logic Apps](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.Logic.json) |[Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Aplicaciones móviles |Sí | [REST de App Service](/rest/api/appservice) |[Esquema de Mobile Apps](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[mobileApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22mobileApp%22&type=Code) |
| Compromisos móviles |yes |[REST para Mobile Engagement](https://msdn.microsoft.com/library/azure/mt683754.aspx) | |[Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |
| Search |Sí |[REST de Búsqueda](/rest/api/searchservice) | [Esquema de Search](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-19/Microsoft.Search.json) |[Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| Web Apps |Sí | [REST de Web Apps](/rest/api/appservice/webapps) |[Esquema de Web Apps](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |

## <a name="intelligence--analytics"></a>Inteligencia y análisis
| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| --- | --- | --- | --- | --- |
| Analysis Services | Sí | [REST de Analysis Services](/rest/api/analysisservices) | [Esquema de Analysis Services](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-16/Microsoft.AnalysisServices.json) | |
| Cognitive Services |Sí | [REST de Cognitive Services](/rest/api/cognitiveservices) |[REST de Cognitive Services](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-01-preview/Microsoft.CognitiveServices.json) | |
| Catálogo de datos |Sí |[REST de Catálogo de datos](/rest/api/datacatalog) |[Esquema de Data Catalog](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) | |
| Data Factory |Sí |[REST de Factoría de datos](/rest/api/datafactory) | |[Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Análisis de Data Lake |Sí | [REST de Data Lake](/rest/api/datalakeanalytics) |[Esquema de Data Lake](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) |[Microsoft.DataLakeAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeAnalytics%22&type=Code) |
| Almacén de Data Lake |Sí |[REST de Almacén de Data Lake](/rest/api/datalakestore) |[Esquema de Data Lake](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) |[Microsoft.DataLakeStore](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeStore%22&type=Code) |
| HDInsights |Sí |[REST de HDInsights](/rest/api/hdinsight) | |[Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Aprendizaje automático |Sí |[REST de Aprendizaje automático](/rest/api/machinelearning) |[Esquema de Machine Learning](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-01-preview/Microsoft.MachineLearning.json) | |
| Stream Analytics |Sí |[REST de Análisis de transmisiones](/rest/api/streamanalytics) | | |
| Power BI |Sí |[REST de Power BI Embedded](/rest/api/powerbiembedded) |[Esquema de Power BI](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-01-29/Microsoft.PowerBI.json) | |


## <a name="internet-of-things"></a>Internet de las cosas
| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| --- | --- | --- | --- | --- |
| Centro de eventos |Sí |[REST del Centro de eventos](/rest/api/eventhub) |[Esquema del Centro de eventos](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.EventHub.json) |[Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code) |
| IoTHubs |Sí |[REST de Centro de IoT](/rest/api/iothub) |[Esquema de IoT Hub](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-03/Microsoft.Devices.json) |[Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Centros de notificaciones |Sí |[REST de Centro de notificaciones](/rest/api/notificationhubs) |[Esquema del Centro de notificaciones](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |[Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |

## <a name="media--cdn"></a>Multimedia y CDN
| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| --- | --- | --- | --- | --- |
| Servicio CDN |Sí |[REST de CDN](/rest/api/cdn) |[Esquema de CDN](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-02/Microsoft.Cdn.json) |[Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Servicio multimedia |Sí |[REST de Servicios multimedia](/rest/api/media) |[Esquema de medios](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01/Microsoft.Media.json) | |

## <a name="hybrid-integration"></a>Integración híbrida
| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| --- | --- | --- | --- | --- |
| Servicios de BizTalk |Sí | |[Esquema de BizTalk](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) | |
| Servicio de recuperación |Sí |[REST de Recovery Services](/rest/api/recoveryservices) |[Esquema de Recovery Services](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.RecoveryServices.json) |[Microsoft.RecoveryServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.RecoveryServices%22&type=Code) |
| Bus de servicio |yes |[REST para Service Bus](/rest/api/servicebus) |[Esquema de Service Bus](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.ServiceBus.json) |[Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |

## <a name="identity--access-management"></a>Administración de identidad y acceso
Azure Active Directory funciona con el administrador de recursos para habilitar el control de acceso basado en roles de la suscripción. Para más información acerca del uso del control de acceso basado en rol y Active Directory, consulte [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md).

## <a name="developer-services"></a>Servicios de desarrollador
| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| --- | --- | --- | --- | --- |
| Supervisión |Sí |[REST de Monitor](/rest/api/monitor) |[Esquema de Insights](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |[Microsoft.insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Mapas de Bing |Sí | | | |
| DevTest Labs |Sí | [REST de DevTest](/rest/api/dtl) |[Esquema del laboratorio de desarrollo y pruebas](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-15/Microsoft.DevTestLab.json) |[Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code) |
| Cuenta de Visual Studio |Sí | |[Esquema de Visual Studio](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) | |

## <a name="management-and-security"></a>Administración y seguridad
| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| --- | --- | --- | --- | --- |
| Automatización |Sí |[Operaciones de REST de automatización](https://msdn.microsoft.com/library/azure/mt662285.aspx) |[Esquema de automatización](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-31/Microsoft.Automation.json) |[Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Almacén de claves |Sí |[REST de Almacén de claves](/rest/api/keyvault) |[Key vault](resource-manager-template-keyvault.md)<br />[Secreto del almacén de claves](resource-manager-template-keyvault-secret.md) |[Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Visión operativa |Sí | | | |
| Programador |Sí |[REST de Programador](/rest/api/scheduler) |[Esquema de Scheduler](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-01/Microsoft.Scheduler.json) |[Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Seguridad (vista previa) |Sí |[REST de Seguridad](https://msdn.microsoft.com/library/azure/mt704034.aspx) | |[Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code) |

## <a name="resource-manager"></a>Resource Manager
| Característica | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| --- | --- | --- | --- | --- | --- |
| Autorización |Sí |[REST de autorización](/rest/api/authorization) |[Bloqueo de recursos](resource-manager-template-lock.md)<br />[Asignaciones de roles](resource-manager-template-role.md) |[Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Recursos |Sí |[REST de recursos](/rest/api/resources) |[Vínculos de recursos](resource-manager-template-links.md) |[Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |

## <a name="resource-providers-and-types"></a>Tipos y proveedores de recursos
Al implementar los recursos, con frecuencia necesitará recuperar información sobre los tipos y proveedores de recursos. Puede recuperar esta información a través de la API de REST, Azure PowerShell o CLI de Azure.

Para trabajar con un proveedor de recursos, dicho proveedor debe estar registrado con su cuenta. De forma predeterminada, muchos proveedores de recursos se registran automáticamente. Sin embargo, debe registrar manualmente algunos de ellos. Los ejemplos siguientes muestran cómo obtener el estado de registro de un proveedor de recursos y cómo registrarlo, si es necesario.

### <a name="portal"></a>Portal
Puede ver fácilmente una lista de proveedores de recursos admitidos seleccionando **Proveedores de recursos** en la hoja de suscripción. Para registrar un proveedor con un proveedor de recursos, seleccione el vínculo **Registrar**.
   
![Enumeración de proveedores de recursos](./media/resource-manager-supported-services/view-resource-providers.png)

### <a name="rest-api"></a>API de REST
Para obtener todos los proveedores de recursos disponibles, incluidos sus tipos, ubicaciones, versiones de API y estado de registro, use la operación [List all resource providers](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) (Enumerar todos los proveedores de recursos). Si necesita registrar un proveedor de recursos, consulte [Registrar una suscripción con un proveedor de recursos](https://docs.microsoft.com/rest/api/resources/providers#Providers_Register).

### <a name="powershell"></a>PowerShell
En el ejemplo siguiente se muestra cómo obtener todos los proveedores de recursos disponibles.

    Get-AzureRmResourceProvider -ListAvailable


En el ejemplo siguiente se muestra cómo obtener los tipos de recursos para un proveedor de recursos determinado.

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes

La salida es parecida a esta:

    ResourceTypeName                Locations                                         
    ----------------                ---------                                         
    sites/extensions                {Brazil South, East Asia, East US, Japan East...} 
    sites/slots/extensions          {Brazil South, East Asia, East US, Japan East...} .
    ...

Para registrar un proveedor de recursos, proporcione el espacio de nombres:

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement

### <a name="azure-cli"></a>CLI de Azure
En el ejemplo siguiente se muestra cómo obtener todos los proveedores de recursos disponibles.

    azure provider list

La salida es parecida a esta:

    info:    Executing command provider list
    + Getting registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.ApiManagement          Unregistered
    data:    Microsoft.AppService             Registered
    data:    Microsoft.Authorization          Registered
    ...

Puede guardar la información para un proveedor de recursos particular en un archivo con el siguiente comando:

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Para registrar un proveedor de recursos, proporcione el espacio de nombres:

    azure provider register -n Microsoft.ServiceBus

## <a name="supported-regions"></a>Regiones admitidas
Al implementar recursos, normalmente debe especificar una región para los recursos. El Administrador de recursos se admite en todas las regiones, pero puede que los recursos que implementa no se admitan en todas las regiones. Además, puede haber limitaciones en su suscripción que le impidan utilizar algunas regiones que admiten el recurso. Estas limitaciones pueden estar relacionadas con problemas fiscales de su país de origen o ser el resultado de una directiva creada por su administrador de suscripción para su uso solo en determinadas regiones. 

Para obtener una lista completa de todas las regiones admitidas para todos los servicios de Azure, consulte [Productos disponibles por región](https://azure.microsoft.com/regions/#services). Sin embargo, esta lista puede incluir regiones que la suscripción no admite. Puede determinar las regiones para un tipo de recurso concreto que admite su suscripción mediante el portal, la API de REST, PowerShell o la CLI de Azure.

### <a name="portal"></a>Portal
Puede ver las regiones admitidas para un tipo de recurso realizando los pasos siguientes:

1. Seleccione **Más servicios** > **Explorador de recursos**.
   
    ![Explorador de recursos](./media/resource-manager-supported-services/select-resource-explorer.png)
2. Abra el nodo **Proveedores**.
   
    ![Selección de proveedores](./media/resource-manager-supported-services/select-providers.png)
3. Seleccione un proveedor de recursos y vea las versiones de API y las regiones admitidas.
   
    ![Visualización de proveedores](./media/resource-manager-supported-services/view-provider.png)

### <a name="rest-api"></a>API de REST
Para detectar qué regiones están disponibles para un tipo de recurso determinado en su suscripción, use la operación [Enumerar todos los proveedores de recursos](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) . 

### <a name="powershell"></a>PowerShell
En el ejemplo siguiente se muestra cómo obtener las regiones admitidas para sitios web.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

La salida es parecida a esta:

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

### <a name="azure-cli"></a>CLI de Azure
El ejemplo siguiente devuelve todas las ubicaciones admitidas para cada tipo de recurso.

    azure location list

También puede filtrar los resultados de ubicación con una utilidad JSON como [jq](https://stedolan.github.io/jq/).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Que devuelve:

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## <a name="supported-api-versions"></a>Versiones de API admitidas
Cuando implemente una plantilla, debe especificar una versión de API que se usará para crear cada recurso. La versión de API se corresponde a una versión de operaciones de API de REST que se publican por el proveedor de recursos. Conforme un proveedor de recursos habilite nuevas características, publicará una nueva versión de la API de REST. Por lo tanto, la versión de la API que especifica en la plantilla afecta a qué propiedades puede especificar en la plantilla. En general, deseará seleccionar la versión de la API más reciente al crear nuevas plantillas. Para las plantillas existentes, puede decidir si quiere continuar usando una versión anterior de la API o actualizar la plantilla para que la versión más reciente aproveche las nuevas características.

### <a name="portal"></a>Portal
Determine las versiones de API compatibles de la misma manera que con las regiones (tal y como se ha mostrado anteriormente).

### <a name="rest-api"></a>API de REST
Para detectar las versiones de la API que están disponibles para los tipos de recursos, use la operación [Enumerar todos los proveedores de recursos](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) . 

### <a name="powershell"></a>PowerShell
En el ejemplo siguiente se muestra cómo obtener las versiones de API disponibles para un tipo de recurso concreto.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

La salida es parecida a esta:

    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

### <a name="azure-cli"></a>CLI de Azure
Puede guardar la información (incluidas las versiones disponibles de la API) para un proveedor de recursos en un archivo con el siguiente comando:

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Puede abrir el archivo y buscar el elemento **apiVersions** .

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información sobre la creación de plantillas del Administrador de recursos, consulte [Creación de plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).
* Para obtener más información sobre la implementación de recursos, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).




<!--HONumber=Dec16_HO2-->


