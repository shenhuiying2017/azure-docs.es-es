<properties
   pageTitle="Servicios, regiones, esquemas y versiones compatibles con el Administrador de recursos | Microsoft Azure"
   description="Describe los proveedores de recursos que admiten el Administrador de recursos, sus esquemas y las versiones disponibles de API y las regiones que pueden hospedar los recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/22/2015"
   ms.author="tomfitz"/>

# Proveedores, regiones, versiones de API y esquemas del Administrador de recursos

El Administrador de recursos de Azure le proporciona una nueva manera de implementar y administrar los servicios que conforman sus aplicaciones.
La mayoría de los servicios, aunque no todos, admiten el Administrador de recursos y algunos lo admiten solo parcialmente. Microsoft habilitará el Administrador de recursos para cada servicio que sea importante para las soluciones futuras, pero hasta que la compatibilidad sea coherente, debe conocer el estado actual de cada servicio. Este tema proporciona una lista de proveedores de recursos admitidos para el Administrador de recursos de Azure.

Al implementar sus recursos, también debe saber qué regiones admiten esos recursos y qué versiones de API están disponibles para los recursos. En la sección [Regiones admitidas](#supported-regions) se muestra cómo averiguar qué regiones funcionarán para su suscripción y recursos. En la sección [Versiones de API admitidas](#supported-api-versions) se muestra cómo determinar las versiones de API que puede usar.

Para ver los servicios que se admiten en el Portal de Azure y el Portal clásico, consulte el [gráfico de disponibilidad del Portal de Azure](https://azure.microsoft.com/features/azure-portal/availability/).

En las tablas siguientes se muestra qué servicios admiten la implementación y administración a través del Administrador de recursos y cuáles no. La columna titulada **Mover recursos** hace referencia a si los recursos de este tipo se pueden mover tanto a un nuevo grupo de recursos como a una nueva suscripción. El vínculo de la columna **Plantillas de inicio rápido** envía una consulta al repositorio de plantillas de inicio rápido de Azure para el proveedor de recursos especificado. Las plantillas de inicio rápido se agregan y se actualizan con frecuencia. La presencia de un vínculo para un servicio concreto no significa necesariamente que la consulta devolverá las plantillas del repositorio.


## Proceso

| Servicio | Administrador de recursos habilitado | Mover recursos | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | ------------------------ | -------------- |-------- | ------ | ------ |
| Máquinas virtuales | Sí | No | [Crear una máquina virtual](https://msdn.microsoft.com/library/azure/mt163591.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [Microsoft.Compute](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%22&type=Code) |
| Lote | Sí | Sí | [REST de Lote](https://msdn.microsoft.com/library/azure/dn820158.aspx) | | [Microsoft.Batch](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Batch%22&type=Code) |
| Dynamics Lifecycle Services | Sí | | | | [Microsoft.DynamicsLcs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DynamicsLcs%22&type=Code)
| Service Fabric (vista previa) | Sí | | [Rest de Service Fabric](https://msdn.microsoft.com/library/azure/dn707692.aspx) | | [Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Máquinas virtuales (clásicas) | Limitado | Parcial (ver a continuación) | - | - |
| Remote App | No | - | - | - |

Máquinas virtuales (clásicas) hace referencia a recursos que se implementaron mediante el modelo de implementación clásica, en lugar de a través del modelo de implementación del Administrador de recursos. En general, estos recursos no admiten las operaciones del Administrador de recursos, pero hay algunas operaciones que se han habilitado. Para obtener más información sobre estos modelos de implementación, consulte [Descripción de la implementación del Administrador de recursos y la implementación clásica](resource-manager-deployment-model.md).

Los recursos de Máquinas virtuales (clásicas) se pueden mover a un nuevo grupo de recursos, pero no a una nueva suscripción.

## Redes

| Servicio | Administrador de recursos habilitado | Mover recursos | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | -------  | -------------- | -------- | ------ | ------ |
| Puerta de enlace de aplicaciones | Sí | | | | [applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS | Sí | | [Creación de una zona DNS](https://msdn.microsoft.com/library/azure/mt130622.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| Equilibrador de carga | Sí | | [Cree un equilibrador de carga](https://msdn.microsoft.com/library/azure/mt163574.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Redes virtuales | Sí | No | [Cree una red virtual:](https://msdn.microsoft.com/library/azure/mt163661.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| Administrador de tráfico | Sí | | [Creación de un perfil del Administrador de tráfico](https://msdn.microsoft.com/library/azure/mt163581.aspx) | [2015-11-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json) | [trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| ExpressRoute | Sí | No | [REST de ExpressRoute](https://msdn.microsoft.com/library/azure/mt586720.aspx) | | [expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |

## Datos y almacenamiento

| Servicio | Administrador de recursos habilitado | Mover recursos | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | ------- | -------------- | -------- | ------ | ------- | ------ |
| DocumentDB | Sí | Sí | [REST de DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) | [2015-04-08](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json) | [Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Almacenamiento | Sí | No | [Creación de almacenamiento](https://msdn.microsoft.com/library/azure/mt163564.aspx) | [Cuenta de almacenamiento](resource-manager-template-storage.md) | [Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| Caché en Redis | Sí | Sí | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Cache.json) | [Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| Base de datos SQL | Sí | Sí | [Crear base de datos](https://msdn.microsoft.com/library/azure/mt163685.aspx) | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) | [Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| Search | Sí | Sí | [REST de Búsqueda](https://msdn.microsoft.com/library/azure/dn798935.aspx) | | [Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| Almacenamiento de datos SQL | Sí | | | |
| StorSimple | No | - | - | - |
| Memoria caché administrada | No | - | - | - |

## Web y móvil

| Servicio | Administrador de recursos habilitado | Mover recursos | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Administración de API | Sí | Sí | [Create API](https://msdn.microsoft.com/library/azure/dn781423.aspx#CreateAPI) | | [Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) | 
| Aplicaciones de API | Sí | | | [2015-03-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) | [Aplicaciones de API](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| Aplicaciones web | Sí | Sí, con limitaciones (ver a continuación) | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |
| Centros de notificaciones | Sí | Sí | [Creación de un Centro de notificaciones](https://msdn.microsoft.com/library/azure/dn223269.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) | [Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |
| Aplicaciones lógicas | Sí | Sí | | | [Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Compromisos móviles | Sí | Sí | | | [Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |

Al trabajar con aplicaciones web, no se puede mover solo un plan del Servicio de aplicaciones. Para mover las aplicaciones web, las opciones son:

- Mover todos los recursos de un grupo de recursos a otro grupo de recursos, si el grupo de recursos de destino no tiene ya recursos Microsoft.Web.
- Mover las aplicaciones web a un grupo de recursos distinto, pero mantener el plan del Servicio de aplicaciones del grupo de recursos original.

## Análisis

| Servicio | Administrador de recursos habilitado | Mover recursos | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | -------  | -------------- | -------- | ------ | ------ |
| Centro de eventos | Sí | | [Creación de un Centro de eventos](https://msdn.microsoft.com/library/azure/dn790676.aspx) | | [Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code) |
| Análisis de transmisiones | Sí | | [REST de Análisis de transmisiones](https://msdn.microsoft.com/library/azure/dn835031.aspx) | | [Microsoft.StreamAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.StreamAnalytics%22&type=Code) |
| HDInsights | Sí | Sí | [Crear clúster](https://msdn.microsoft.com/library/azure/mt622302.aspx) | | [Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Factoría de datos | Sí | Sí | [Crear factoría de datos](https://msdn.microsoft.com/library/azure/dn906717.aspx) | | [Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Aprendizaje automático | No | - | - | - |
| Catálogo de datos | No | - | - | - |

## Multimedia y CDN

| Servicio | Administrador de recursos habilitado | Mover recursos | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Servicio CDN | Sí | | [REST de CDN](https://msdn.microsoft.com/library/azure/mt634456.aspx) | | [Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Servicio multimedia | No | | | |


## Integración híbrida

| Servicio | Administrador de recursos habilitado | Mover recursos | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Servicios de BizTalk | Sí | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) | [Microsoft.BizTalkServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.BizTalkServices%22&type=Code) |
| Bus de servicio | Sí | | [REST de Bus de servicio](https://msdn.microsoft.com/library/azure/hh780717.aspx) | | [Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |
| Copia de seguridad | No | - | - | - |
| Recuperación del sitio | No | - | - | - |

## Administración de identidad y acceso 

| Servicio | Administrador de recursos habilitado | Portal | Mover recursos | API de REST | Esquema |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| Azure Active Directory | No | No | - | - | - | 
| Azure Active Directory B2C | No | No | - | - | - | 
| Multi-Factor Authentication | No | No | - | - | - |

## Servicios de desarrollador 

| Servicio | Administrador de recursos habilitado | Mover recursos | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Application Insights | Sí | No | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) | [Microsoft.insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Mapas de Bing | Sí | | | | [Microsoft.BingMaps](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.BingMaps%22&type=Code) |
| Laboratorios de DevTest (vista previa) | Sí | | | [Vista previa 2015-05-21](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-05-21-preview/Microsoft.DevTestLab.json) | [Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code) |
| Cuenta de Visual Studio | Sí | | | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) | [Microsoft.VisualStudio](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.VisualStudio%22&type=Code) |

## Administración y seguridad

| Servicio | Administrador de recursos habilitado | Mover recursos | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Automatización | Sí | Sí | | | [Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Almacén de claves | Sí | Sí | [REST de Almacén de claves](https://msdn.microsoft.com/library/azure/dn903609.aspx) | [Almacén de claves](resource-manager-template-keyvault.md)<br />[Secreto del almacén de claves](resource-manager-template-keyvault-secret.md) | [Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Programador | Sí | | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) | [Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Visión operativa | Sí | Sí | | | [Microsoft.OperationalInsights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.OperationalInsights%22&type=Code) |
| IoTHubs | Sí | | [Crear Centro de IoT](https://msdn.microsoft.com/library/azure/mt589013.aspx) | | [Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Seguridad (vista previa) | Sí | | | | [Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code) |

## Resource Manager

| Característica | Administrador de recursos habilitado | Mover recursos | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | ------- | -------- | -------------- | -------- | ------ | ------ |
| Autorización | Sí | N/D | [Bloqueos de administración](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[Control de acceso basado en rol](https://msdn.microsoft.com/library/azure/dn906885.aspx) | [Bloqueo de recurso](resource-manager-template-lock.md)<br />[Asignaciones de roles](resource-manager-template-role.md) | [Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Recursos | Sí | N/D | [Linked Resources](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [Vínculos de recursos](resource-manager-template-links.md) | [Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |


## Regiones admitidas

Al implementar recursos, normalmente debe especificar una región para los recursos. El Administrador de recursos se admite en todas las regiones, pero puede que los recursos que implementa no se admitan en todas las regiones. Además, puede haber limitaciones en su suscripción que le impidan utilizar algunas regiones que admiten el recurso. Estas limitaciones pueden estar relacionadas con problemas fiscales de su país de origen o ser el resultado de una directiva creada por su administrador de suscripción para su uso solo en determinadas regiones.

Para obtener una lista completa de todas las regiones compatibles con todos los servicios de Azure, consulte [Servicios por región](https://azure.microsoft.com/regions/#services), aunque esta lista puede incluir regiones no compatibles con su suscripción. Puede determinar las regiones para un tipo de recurso concreto que admite su suscripción mediante la ejecución de uno de los comandos siguientes.

### API de REST

Para detectar las regiones que están disponibles para un tipo de recurso en concreto de su suscripción, use la operación [Enumerar todos los proveedores de recursos](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

En el ejemplo siguiente se muestra cómo obtener las regiones admitidas para sitios web mediante Azure PowerShell 1.0. Para más información sobre la versión 1.0, vea [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
La salida debe ser similar a:

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

Para Azure PowerShell 0.9.8, use el comando siguiente:

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

### Azure CLI

El ejemplo siguiente devuelve todas las ubicaciones admitidas para cada tipo de recurso.

    azure location list

También puede filtrar los resultados de ubicación con una herramienta como **jq**. Para obtener información sobre herramientas como jq, consulte [Herramientas útiles para interactuar con Azure](/virtual-machines/resource-group-deploy-debug/#useful-tools-to-interact-with-azure).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Que devuelve:

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## Versiones de API compatibles

Cuando implemente una plantilla, debe especificar una versión de API que se usará para crear cada recurso. La versión de API se corresponde a una versión de operaciones de API de REST que se publican por el proveedor de recursos. Conforme un proveedor de recursos habilite nuevas características, publicará una nueva versión de la API de REST. Por lo tanto, la versión de la API que especifica en la plantilla afecta a qué propiedades puede especificar en la plantilla. En general, deseará seleccionar la versión de la API más reciente al crear nuevas plantillas. Para las plantillas existentes, puede decidir si quiere continuar usando una versión anterior de la API o actualizar la plantilla para que la versión más reciente aproveche las nuevas características.

### API de REST

Para detectar las versiones de la API que están disponibles para los tipos de recursos, use la operación [Enumerar todos los proveedores de recursos](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

En el ejemplo siguiente se muestra cómo obtener las versiones de API disponibles para un tipo de recurso concreto mediante Azure PowerShell 1.0.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    
La salida debe ser similar a:
    
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

Para Azure PowerShell 0.9.8, use:

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### Azure CLI

Puede guardar la información (incluidas las versiones disponibles de la API) para un proveedor de recursos en un archivo con el siguiente comando.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Puede abrir el archivo y buscar el elemento **apiVersions**.

## Pasos siguientes

- Para obtener más información sobre la creación de plantillas del Administrador de recursos, consulte [Creación de plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).
- Para obtener más información sobre la implementación de recursos, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).

<!----HONumber=AcomDC_1223_2015-->