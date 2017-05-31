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
ms.date: 03/20/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 70a598c9a5bf514f62baa0c5275b2e44cd9776b8
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>Proveedores, regiones, versiones de API y esquemas del Administrador de recursos
Este tema proporciona una lista de proveedores de recursos compatibles con Azure Resource Manager.

Al implementar sus recursos, también debe saber qué regiones admiten esos recursos y qué versiones de API están disponibles para los recursos. En la sección [Regiones admitidas](#supported-regions) se muestra cómo averiguar qué regiones funcionarán para su suscripción y recursos. En la sección [Versiones de API admitidas](#supported-api-versions) se muestra cómo determinar las versiones de API que puede usar.

Para ver los servicios que se admiten en el Portal de Azure y el Portal clásico, consulte el [gráfico de disponibilidad del Portal de Azure](https://azure.microsoft.com/features/azure-portal/availability/). Para ver los servicios que admiten el traslado de recursos, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](resource-group-move-resources.md).

En las tablas siguientes se muestra qué servicios de Microsoft admiten la implementación y administración a través de Resource Manager y cuáles no. También hay muchos proveedores de recursos de terceros compatibles con Resource Manager. Obtenga información sobre cómo ver todos los proveedores de recursos en la sección [Tipos y proveedores de recursos](#resource-providers-and-types).

## <a name="compute"></a>Proceso
| Servicio | Administrador de recursos habilitado | API de REST | Formato de plantilla |
| --- | --- | --- | --- |
| Batch |Sí |[REST de Lote](/rest/api/batchservice) |[Recursos de Batch](/azure/templates/microsoft.batch/batchaccounts) |
| Container Registry |Sí |[REST de Container Registry](/rest/api/containerregistry) |[Recursos de Container Registry](/azure/templates/microsoft.containerregistry/registries) |
| Container Service |Sí |[REST de Servicio de contenedor](/rest/api/compute/containerservices) |[Recursos de Container Service](/azure/templates/microsoft.containerservice/containerservices) |
| Dynamics Lifecycle Services |Sí | | |
| Conjuntos de escala |Sí |[REST de Conjunto de escala](/rest/api/virtualmachinescalesets/) |[Recursos de conjunto de escalado](/azure/templates/microsoft.compute/virtualmachinescalesets) |
| Service Fabric |Sí |[Rest de Service Fabric](/rest/api/servicefabric) | [Recursos de Service Fabric](/azure/templates/microsoft.servicefabric/clusters) |
| Máquinas virtuales |Sí |[VM REST](/rest/api/compute/virtualmachines) |[Recursos de máquina virtual](/azure/templates/microsoft.compute/virtualmachines) |
| Máquinas virtuales (clásicas) |Limitado |- |- |
| RemoteApp |No |- |- |
| Servicios en la nube (clásico) |Limitado (consúltese a continuación) |- |- |

Máquinas virtuales (clásicas) hace referencia a recursos que se implementaron mediante el modelo de implementación clásica, en lugar de a través del modelo de implementación del Administrador de recursos. En general, estos recursos no admiten las operaciones del Administrador de recursos, pero hay algunas operaciones que se han habilitado. Para obtener más información sobre estos modelos de implementación, vea [Descripción de la implementación del Administrador de recursos y la implementación clásica](resource-manager-deployment-model.md). 

Cloud Services (clásico) se puede usar con otros recursos clásicos. Sin embargo, los recursos clásicos no aprovechan todas las características de Resource Manager y no son una buena opción para las soluciones futuras. En su lugar, considere la posibilidad de cambiar la infraestructura de aplicaciones para usar recursos de los espacios de nombres Microsoft.Compute, Microsoft.Storage y Microsoft.Network.

## <a name="networking"></a>Redes
| Servicio | Administrador de recursos habilitado | API de REST | Formato de plantilla |
| --- | --- | --- | --- |
| Application Gateway |Sí |[REST de Puerta de enlace de aplicaciones](https://msdn.microsoft.com/library/azure/mt684939.aspx) | [Recursos de Application Gateway](/azure/templates/microsoft.network/applicationgateways) |
| DNS |Sí |[DNS REST](/rest/api/dns) |[Recursos DNS](/azure/templates/microsoft.network/dnszones) |
| ExpressRoute |Sí |[REST de ExpressRoute](https://msdn.microsoft.com/library/azure/mt586720.aspx) | [Recursos de ExpressRoute](/azure/templates/microsoft.network/expressroutecircuits) |
| Load Balancer |Sí |[REST del Equilibrador de carga](https://msdn.microsoft.com/library/azure/mt163651.aspx) |[Recursos de Load Balancer](/azure/templates/microsoft.network/loadbalancers) |
| Administrador de tráfico |Sí |[REST para Administrador de tráfico](https://msdn.microsoft.com/library/azure/mt163667.aspx) |[Recursos de Traffic Manager](/azure/templates/microsoft.network/trafficmanagerprofiles) |
| Redes virtuales |Sí |[REST para Virtual Network](https://msdn.microsoft.com/library/azure/mt163650.aspx) |[Recursos de virtual Network](/azure/templates/microsoft.network/virtualnetworks) |
| Puerta de enlace de red |Sí |[REST de puerta de enlace de red](https://msdn.microsoft.com/library/azure/mt163859.aspx) | [Recursos de conexión](/azure/templates/microsoft.network/connections) <br /> [Recursos de puerta de enlace de red local](/azure/templates/microsoft.network/localnetworkgateways) <br /> [Recursos de puerta de enlace de red virtual](/azure/templates/microsoft.network/virtualnetworkgateways) |

## <a name="storage"></a>Storage
| Servicio | Administrador de recursos habilitado | API de REST | Formato de plantilla |
| --- | --- | --- | --- | --- |
| Import/Export | Sí | [REST de Import/Export](/rest/api/storageimportexport/) | [Recursos de Import/Export](/azure/templates/microsoft.importexport/jobs) |
| Storage |Sí |[REST de Storage](/rest/api/storagerp) |[Recursos de Storage](/azure/templates/microsoft.storage/storageaccounts) |
| StorSimple |Sí | | |

## <a name="databases"></a>Bases de datos
| Servicio | Administrador de recursos habilitado | API de REST | Formato de plantilla |
| --- | --- | --- | --- | --- |
| Azure Cosmos DB |Sí |[REST de Azure Cosmos DB](/rest/api/documentdbresourceprovider) |[Recursos de Azure Cosmos DB](/azure/templates/microsoft.documentdb/databaseaccounts) |
| Redis Cache |Sí | [API de REST de Redis Cache](/rest/api/redis) |[Recursos de Redis](/azure/templates/microsoft.cache/redis) |
| SQL Database |Sí |[REST de Base de datos SQL](/rest/api/sql) |[Recursos de SQL Database](/azure/templates/microsoft.sql/servers) |
| SQL Data Warehouse |Sí | | |

## <a name="web--mobile"></a>Web y móvil
| Servicio | Administrador de recursos habilitado | API de REST | Formato de plantilla |
| --- | --- | --- | --- |
| API Apps |Sí | [REST de App Service](/rest/api/appservice) |[Recursos web](/azure/templates/microsoft.web/sites) |
| API Management |Sí |[REST de Administración de API](/rest/api/apimanagement) |[Recursos de API Management](/azure/templates/microsoft.apimanagement/service) |
| Registro de certificados | Sí | [REST de registro de certificados](/rest/api/appservice/appservicecertificateorders) | [Recursos de registro de certificados](/azure/templates/microsoft.certificateregistration/certificateorders)  |
| Content Moderator |Sí | | | |
| Registro de dominios | Sí | [Registro de dominios](/rest/api/appservice/domains) | [Recursos de registro de dominios](/azure/templates/microsoft.domainregistration/domains)  |
| Function App |Sí | [REST de Function App](/rest/api/appservice) | [Recursos web](/azure/templates/microsoft.web/sites) |
| Logic Apps |Sí |[REST de Logic Apps](/rest/api/logic) |[Recursos de Logic Apps](/azure/templates/microsoft.logic/workflows) |
| Mobile Apps |Sí | [REST de App Service](/rest/api/appservice) | [Recursos web](/azure/templates/microsoft.web/sites) |
| Compromisos móviles |yes |[REST para Mobile Engagement](https://msdn.microsoft.com/library/azure/mt683754.aspx) | |
| Search |Sí |[REST de Búsqueda](/rest/api/searchservice) | [Recursos de Search](/azure/templates/microsoft.search/searchservices) |
| Web Apps |Sí | [REST de Web Apps](/rest/api/appservice/webapps) | [Recursos web](/azure/templates/microsoft.web/sites) |

## <a name="intelligence--analytics"></a>Inteligencia y análisis
| Servicio | Administrador de recursos habilitado | API de REST | Formato de plantilla | 
| --- | --- | --- | --- |
| Analysis Services | Sí | [REST de Analysis Services](/rest/api/analysisservices) | [Recursos de Analysis Services](/azure/templates/microsoft.analysisservices/servers) |
| Cognitive Services |Sí | [REST de Cognitive Services](/rest/api/cognitiveservices) |[Recursos de Cognitive Services](/azure/templates/microsoft.cognitiveservices/accounts) |
| Catálogo de datos |Sí |[REST de Catálogo de datos](/rest/api/datacatalog) |[Esquema de Data Catalog](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) |
| Data Factory |Sí |[REST de Factoría de datos](/rest/api/datafactory) | |
| Data Lake Analytics |Sí | [REST de Data Lake](/rest/api/datalakeanalytics) |[Recursos de Data Lake Analytics](/azure/templates/microsoft.datalakeanalytics/accounts) |
| Data Lake Store |Sí |[REST de Almacén de Data Lake](/rest/api/datalakestore) |[Recursos de Data Lake Store](/azure/templates/microsoft.datalakestore/accounts) |
| HDInsights |Sí |[REST de HDInsights](/rest/api/hdinsight) | |
| Machine Learning |Sí |[REST de Aprendizaje automático](/rest/api/machinelearning) |[Recursos de Machine Learning](/azure/templates/microsoft.machinelearning/commitmentplans) |
| Stream Analytics |Sí |[REST de Análisis de transmisiones](/rest/api/streamanalytics) | |
| Power BI |Sí |[REST de Power BI Embedded](/rest/api/powerbiembedded) |[Recursos de Power BI](/azure/templates/microsoft.powerbi/workspacecollections) |


## <a name="internet-of-things"></a>Internet de las cosas
| Servicio | Administrador de recursos habilitado | API de REST | Formato de plantilla |
| --- | --- | --- | --- |
| Centro de eventos |Sí |[REST del Centro de eventos](/rest/api/eventhub) |[Recursos de Event Hubs](/azure/templates/microsoft.eventhub/namespaces) |
| IoTHubs |Sí |[REST de Centro de IoT](/rest/api/iothub) |[Recursos de IoT Hub](/azure/templates/microsoft.devices/iothubs) |
| Notification Hubs |Sí |[REST de Centro de notificaciones](/rest/api/notificationhubs) |[Recursos de Notification Hubs](/azure/templates/microsoft.notificationhubs/namespaces) |

## <a name="media--cdn"></a>Multimedia y CDN
| Servicio | Administrador de recursos habilitado | API de REST | Formato de plantilla |
| --- | --- | --- | --- |
| CDN |Sí |[REST de CDN](/rest/api/cdn) |[Recursos de CDN](/azure/templates/microsoft.cdn/profiles) |
| Servicio multimedia |Sí |[REST de Servicios multimedia](/rest/api/media) |[Recursos multimedia](/azure/templates/microsoft.media/mediaservices) |

## <a name="enterprise-integration"></a>Integración empresarial
| Servicio | Administrador de recursos habilitado | API de REST | Formato de plantilla |
| --- | --- | --- | --- |
| Servicios de BizTalk |Sí | |[Esquema de BizTalk](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| Retransmisión | Sí |  | [Recursos de Relay](/azure/templates/microsoft.relay/namespaces) |
| Service Bus |yes |[REST para Service Bus](/rest/api/servicebus) |[Recursos de Service Bus](/azure/templates/microsoft.servicebus/namespaces) |

## <a name="identity--access-management"></a>Administración de identidad y acceso
Azure Active Directory funciona con el administrador de recursos para habilitar el control de acceso basado en roles de la suscripción. Para más información acerca del uso del control de acceso basado en rol y Azure Active Directory, consulte [Control de acceso basado en rol de Azure](../active-directory/role-based-access-control-configure.md).

## <a name="developer-services"></a>Servicios de desarrollador
| Servicio | Administrador de recursos habilitado | API de REST | Formato de plantilla |
| --- | --- | --- | --- |
| Supervisión |Sí |[REST de Monitor](/rest/api/monitor) |[Recursos de Insights](/azure/templates/microsoft.insights/alertrules) |
| Mapas de Bing |Sí | | |
| DevTest Labs |Sí | [REST de DevTest Labs](/rest/api/dtl) |[Recursos de DevTest Labs](/azure/templates/microsoft.devtestlab/labs) |
| Cuenta de Visual Studio |Sí | |[Esquema de Visual Studio](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## <a name="management-and-security"></a>Administración y seguridad
| Servicio | Administrador de recursos habilitado | API de REST | Formato de plantilla |
| --- | --- | --- | --- |
| Advisor | Sí | [REST de Advisor](/rest/api/advisor/) | - |
| Automation |Sí |[Operaciones de REST de automatización](https://msdn.microsoft.com/library/azure/mt662285.aspx) |[Recursos de Automation](/azure/templates/microsoft.automation/automationaccounts) |
| Facturación | Sí | [REST de facturación](/rest/api/billing/) | - |
| Key Vault |Sí |[REST de Almacén de claves](/rest/api/keyvault) |[Recursos de Key Vault](/azure/templates/microsoft.keyvault/vaults) |
| Visión operativa |Sí | | |
| Servicio de recuperación |Sí |[REST de Recovery Services](/rest/api/recoveryservices) |[Recursos de Recovery Services](/azure/templates/microsoft.recoveryservices/vaults) |
| Programador |Sí |[REST de Programador](/rest/api/scheduler) |[Recursos de Scheduler](/azure/templates/microsoft.scheduler/jobcollections) |
| Seguridad |Sí |[REST de Seguridad](https://msdn.microsoft.com/library/azure/mt704034.aspx) | |
| Servidor de administración | Sí | [REST de Server Management](/rest/api/servermanagement/) | [Recursos de Server Management](/azure/templates/microsoft.servermanagement/gateways) |

## <a name="resource-manager"></a>Resource Manager
| Característica | Administrador de recursos habilitado | API de REST | Formato de plantilla |
| --- | --- | --- | --- | --- |
| Autorización |Sí |[REST de autorización](/rest/api/authorization) |[Recursos de autorización](/azure/templates/microsoft.authorization/locks) |
| Recursos |Sí |[REST de recursos](/rest/api/resources) |[Recursos de implementación](/azure/templates/microsoft.resources/deployments) |

## <a name="resource-providers-and-types"></a>Tipos y proveedores de recursos
Al implementar los recursos, con frecuencia necesitará recuperar información sobre los tipos y proveedores de recursos. Puede recuperar esta información a través de la API de REST, Azure PowerShell o CLI de Azure.

Para trabajar con un proveedor de recursos, dicho proveedor debe estar registrado con su cuenta. De forma predeterminada, muchos proveedores de recursos se registran automáticamente. Sin embargo, debe registrar manualmente algunos de ellos. Los ejemplos de esta sección muestran cómo obtener el estado de registro de un proveedor de recursos y cómo registrarlo.

### <a name="portal"></a>Portal
Puede ver fácilmente una lista de proveedores de recursos admitidos seleccionando **Proveedores de recursos** en la hoja de suscripción. Para registrar un proveedor con un proveedor de recursos, seleccione el vínculo **Registrar**.
   
![Enumeración de proveedores de recursos](./media/resource-manager-supported-services/view-resource-providers.png)

### <a name="rest-api"></a>API de REST
Para obtener todos los proveedores de recursos disponibles, incluidos sus tipos, ubicaciones, versiones de API y estado de registro, use la operación [List all resource providers](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) (Enumerar todos los proveedores de recursos). Si necesita registrar un proveedor de recursos, consulte [Registrar una suscripción con un proveedor de recursos](https://docs.microsoft.com/rest/api/resources/providers#Providers_Register).

### <a name="powershell"></a>PowerShell
En el ejemplo siguiente se muestra cómo obtener todos los proveedores de recursos disponibles.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```


En el ejemplo siguiente se muestra cómo obtener los tipos de recursos para un proveedor de recursos determinado.

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
```

Para registrar un proveedor de recursos, proporcione el espacio de nombres:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement
```

### <a name="azure-cli"></a>CLI de Azure
En el ejemplo siguiente se muestra cómo obtener todos los proveedores de recursos disponibles.

```azurecli
az provider list
```

Puede ver la información de un proveedor de recursos particular con el siguiente comando:

```azurecli
az provider show --namespace Microsoft.Web
```

Para registrar un proveedor de recursos, proporcione el espacio de nombres:

```azurecli
az provider register --namespace Microsoft.ServiceBus
```

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

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

### <a name="azure-cli"></a>Azure CLI
En el ejemplo siguiente se muestra cómo obtener las ubicaciones admitidas para sitios web.

```azurecli
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```


## <a name="supported-api-versions"></a>Versiones de API admitidas
Cuando implemente una plantilla, debe especificar una versión de API que se usará para crear cada recurso. La versión de API se corresponde a una versión de operaciones de API de REST que se publican por el proveedor de recursos. Conforme un proveedor de recursos habilite nuevas características, publicará una nueva versión de la API de REST. Por lo tanto, la versión de la API que especifica en la plantilla afecta a qué propiedades puede especificar en la plantilla. En general, deseará seleccionar la versión de la API más reciente al crear nuevas plantillas. Para las plantillas existentes, puede decidir si quiere continuar usando una versión anterior de la API o actualizar la plantilla para que la versión más reciente aproveche las nuevas características.

### <a name="portal"></a>Portal
Determine las versiones de API compatibles de la misma manera que con las regiones (tal y como se ha mostrado anteriormente).

### <a name="rest-api"></a>API de REST
Para detectar las versiones de la API que están disponibles para los tipos de recursos, use la operación [Enumerar todos los proveedores de recursos](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) . 

### <a name="powershell"></a>PowerShell
En el ejemplo siguiente se muestra cómo obtener las versiones de API disponibles para un tipo de recurso concreto.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

La salida es parecida a esta:

```powershell
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
```

### <a name="azure-cli"></a>Azure CLI
Las versiones de API disponibles para un proveedor de recursos se obtienen con el siguiente comando:

```azurecli
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].apiVersions"
```

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información sobre la creación de plantillas del Administrador de recursos, consulte [Creación de plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).
* Para obtener más información sobre la implementación de recursos, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).


