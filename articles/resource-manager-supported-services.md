<properties
   pageTitle="Servicios y regiones admitidos por el Administrador de recursos | Microsoft Azure"
   description="Describe los proveedores de recursos que admiten el Administrador de recursos y las regiones que pueden alojar los recursos."
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
   ms.date="12/01/2015"
   ms.author="tomfitz"/>

# Compatibilidad del Administrador de recursos para servicios, regiones y versiones de API

El Administrador de recursos de Azure le proporciona una nueva manera de implementar y administrar los servicios que conforman sus aplicaciones. La mayoría de los servicios, aunque no todos, admiten el Administrador de recursos y algunos lo admiten solo parcialmente. Microsoft habilitará el Administrador de recursos para cada servicio que sea importante para las soluciones futuras, pero hasta que la compatibilidad sea coherente, debe conocer el estado actual de cada servicio. Este tema proporciona una lista de proveedores de recursos admitidos para el Administrador de recursos de Azure.

Al implementar sus recursos, también debe saber qué regiones admiten esos recursos y qué versiones de API están disponibles para los recursos. En la sección [Regiones admitidas](#supported-regions) se muestra cómo averiguar qué regiones funcionarán para su suscripción y recursos. En la sección [Versiones de API admitidas](#supported-api-versions) se muestra cómo determinar las versiones de API que puede usar.

En las tablas siguientes se muestra qué servicios admiten la implementación y administración a través del Administrador de recursos y cuáles no. La columna titulada **Mover recursos** hace referencia a si los recursos de este tipo se pueden mover tanto a un nuevo grupo de recursos como a una nueva suscripción. La columna titulada **Portal** indica si puede crear el servicio a través del [Portal de Azure](https://portal.azure.com).


## Proceso

| Servicio | Administrador de recursos habilitado | Portal | Mover recursos | API de REST | Esquema |
| ------- | ------------------------ | -------------- | -------------- |-------- | ------ |
| Máquinas virtuales | Sí | Sí, muchas opciones | No | [Crear una máquina virtual](https://msdn.microsoft.com/library/azure/mt163591.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |
| Lote | Sí | [í (solo clásico)](https://portal.azure.com/#create/Microsoft.BatchAccount) | Sí | [REST de Lote](https://msdn.microsoft.com/library/azure/dn820158.aspx) | |
| Dynamics Lifecycle Services | Sí | No | | | |
| Máquinas virtuales (clásicas) | Limitado | Sí, muchas opciones | Parcial (ver a continuación) | - | - | | RemoteApp | No | No | - | - | - | | Service Fabric | No | No | - | - | - |

Máquinas virtuales (clásicas) hace referencia a recursos que se implementaron mediante el modelo de implementación clásica, en lugar de a través del modelo de implementación del Administrador de recursos. En general, estos recursos no admiten las operaciones del Administrador de recursos, pero hay algunas operaciones que se han habilitado. Para obtener más información sobre estos modelos de implementación, consulte [Descripción de la implementación del Administrador de recursos y la implementación clásica](resource-manager-deployment-model.md).

Los recursos de Máquinas virtuales (clásicas) se pueden mover a un nuevo grupo de recursos, pero no a una nueva suscripción.

## Redes

| Servicio | Administrador de recursos habilitado | Portal | Mover recursos | API de REST | Esquema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Puerta de enlace de aplicaciones | Sí | | | | |
| DNS | Sí | | | [Creación de una zona DNS](https://msdn.microsoft.com/library/azure/mt130622.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Equilibrador de carga | Sí | | | [Cree un equilibrador de carga](https://msdn.microsoft.com/library/azure/mt163574.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Redes virtuales | Sí | [Sí](https://portal.azure.com/#create/Microsoft.VirtualNetwork-ARM) | No | [Cree una red virtual:](https://msdn.microsoft.com/library/azure/mt163661.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Administrador de tráfico | Sí | No | | [Creación de un perfil del Administrador de tráfico](https://msdn.microsoft.com/library/azure/mt163581.aspx) | |
| ExpressRoute | Sí | No | No | [REST de ExpressRoute](https://msdn.microsoft.com/library/azure/mt586720.aspx) | |

## Datos y almacenamiento

| Servicio | Administrador de recursos habilitado | Portal | Mover recursos | API de REST | Esquema |
| ------- | ------- | ------- | -------------- | -------- | ------ |
| DocumentDB | Sí | [Sí](https://portal.azure.com/#create/Microsoft.DocumentDB) | Sí | [REST de DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) | |
| Almacenamiento | Sí | [Sí](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) | No | [Creación de almacenamiento](https://msdn.microsoft.com/library/azure/mt163564.aspx) | [Cuenta de almacenamiento](resource-manager-template-storage.md) |
| Caché en Redis | Sí | [Sí](https://portal.azure.com/#create/Microsoft.Cache.1.0.4) | Sí | | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Cache.json) |
| Base de datos SQL | Sí | [Sí](https://portal.azure.com/#create/Microsoft.SQLDatabase.0.5.9-preview) | Sí | [Crear base de datos](https://msdn.microsoft.com/library/azure/mt163685.aspx) | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) |
| Search | Sí | [Sí](https://portal.azure.com/#create/Microsoft.Search) | Sí | [REST de Búsqueda](https://msdn.microsoft.com/library/azure/dn798935.aspx) | |
| Almacenamiento de datos SQL | Sí | [Sí](https://portal.azure.com/#create/Microsoft.SQLDataWarehouse.0.1.12-preview) | | | |
| StorSimple | No | No | - | - | - | | Memoria caché administrada | No | No | - | - | - |

## Web y móvil

| Servicio | Administrador de recursos habilitado | Portal | Mover recursos | API de REST | Esquema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Administración de API | Sí | No | Sí | [Create API](https://msdn.microsoft.com/library/azure/dn781423.aspx#CreateAPI) | |
| Aplicaciones de API | Sí | [Sí](https://portal.azure.com/#create/microsoft_com.ApiApp.0.2.0-preview) | | | [2015-03-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) |
| Aplicaciones web | Sí | [Sí](https://portal.azure.com/#create/Microsoft.WebSite) | Sí, con limitaciones (ver a continuación) | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |
| Centros de notificaciones | Sí | [Sí](https://portal.azure.com/#create/Microsoft.NotificationHub) | Sí | [Creación de un Centro de notificaciones](https://msdn.microsoft.com/library/azure/dn223269.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |
| Aplicaciones lógicas | Sí | [Sí](https://portal.azure.com/#create/Microsoft.EmptyWorkflow.0.2.0-preview) | Sí | | |
| Compromisos móviles | Sí | No | Sí | | |

Al trabajar con aplicaciones web, no se puede mover solo un plan del Servicio de aplicaciones. Para mover las aplicaciones web, las opciones son:

- Mover todos los recursos de un grupo de recursos a otro grupo de recursos, si el grupo de recursos de destino no tiene ya recursos Microsoft.Web.
- Mover las aplicaciones web a un grupo de recursos distinto, pero mantener el plan del Servicio de aplicaciones del grupo de recursos original.

## Análisis

| Servicio | Administrador de recursos habilitado | Portal | Mover recursos | API de REST | Esquema |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| Centro de eventos | Sí | No | | [Creación de un Centro de eventos](https://msdn.microsoft.com/library/azure/dn790676.aspx) | |
| Análisis de transmisiones | Sí | [Sí](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) | | | |
| HDInsights | Sí | [Sí](https://portal.azure.com/#create/Microsoft.HDInsightCluster) | Sí | | |
| Factoría de datos | Sí | [Sí](https://portal.azure.com/#create/Microsoft.DataFactory) | Sí | [Crear factoría de datos](https://msdn.microsoft.com/library/azure/dn906717.aspx) | |
| Aprendizaje automático | No | No | - | - | - | | Catálogo de datos | No | No | - | - | - |

## Multimedia y CDN

| Servicio | Administrador de recursos habilitado | Portal | Mover recursos | API de REST | Esquema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Servicio CDN | Sí (vista previa) | No | | | |
| Servicio multimedia | No | No | | | |


## Integración híbrida

| Servicio | Administrador de recursos habilitado | Portal | Mover recursos | API de REST | Esquema |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| Servicios de BizTalk | Sí | No | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| Bus de servicio | Sí | No | | [REST de Bus de servicio](https://msdn.microsoft.com/library/azure/hh780717.aspx) | |
| Copia de seguridad | No | No | - | - | - | | Recuperación del sitio | No | No | - | - | - |

## Administración de identidad y acceso 

| Servicio | Administrador de recursos habilitado | Portal | Mover recursos | API de REST | Esquema |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| Azure Active Directory | No | No | - | - | - | | Azure Active Directory B2C | No | No | - | - | - | | Multi-Factor Authentication | No | No | - | - | - |

## Servicios de desarrollador 

| Servicio | Administrador de recursos habilitado | Portal | Mover recursos | API de REST | Esquema |
| ------- | ------- | ---------- | -------------- | -------- | ------ |
| Application Insights | Sí | [Sí](https://portal.azure.com/#create/Microsoft.AppInsights.0.2.3-preview) | No | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |
| Mapas de Bing | Sí | [Sí](https://portal.azure.com/#create/bingmaps.mapapis.1.0.4) | | | |
| Cuenta de Visual Studio | Sí | | | | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## Administración 

| Servicio | Administrador de recursos habilitado | Portal | Mover recursos | API de REST | Esquema |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| Automatización | Sí | [Sí](https://portal.azure.com/#create/Microsoft.AutomationAccount.1.0.2-preview) | Sí | | |
| Almacén de claves | Sí | No | Sí | [REST de Almacén de claves](https://msdn.microsoft.com/library/azure/dn903609.aspx) | |
| Programador | Sí | No | | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) |
| Visión operativa | Sí | No | Sí | | |
| IoTHubs | Sí | [Sí](https://portal.azure.com/#create/Microsoft.IotHub) | | | |

## Resource Manager

| Característica | Administrador de recursos habilitado | Portal | Mover recursos | API de REST | Esquema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Autorización | Sí | N/D | N/D | [Bloqueos de administración](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[Control de acceso basado en roles](https://msdn.microsoft.com/library/azure/dn906885.aspx) | [Bloqueo de recurso](resource-manager-template-lock.md)<br />[Asignaciones de roles](resource-manager-template-role.md) |
| Recursos | Sí | N/D | N/D | [Linked Resources](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [Vínculos de recursos](resource-manager-template-links.md) |


## Regiones admitidas

Al implementar recursos, normalmente debe especificar una región para los recursos. El Administrador de recursos se admite en todas las regiones, pero puede que los recursos que implementa no se admitan en todas las regiones. Además, puede haber limitaciones en su suscripción que le impidan utilizar algunas regiones que admiten el recurso. Estas limitaciones pueden estar relacionadas con problemas fiscales de su país de origen o ser el resultado de una directiva creada por su administrador de suscripción para su uso solo en determinadas regiones.

Para obtener una lista completa de todas las regiones admitidas para todos los servicios de Azure, consulte [Servicios por región](https://azure.microsoft.com/regions/#services). Sin embargo, esta lista puede incluir regiones no compatibles con su suscripción. Puede determinar las regiones para un tipo de recurso concreto que admite su suscripción mediante la ejecución de uno de los comandos siguientes.

### API de REST

Para detectar qué regiones están disponibles para un tipo de recurso determinado en su suscripción, use la operación [Enumerar todos los proveedores de recursos](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

En el ejemplo siguiente se muestra cómo obtener las regiones admitidas para sitios web mediante Azure PowerShell 1.0. Para obtener más información sobre la versión 1.0, consulte [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

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

Para detectar qué versiones de la API están disponibles para los tipos de recursos, use la operación [Enumerar todos los proveedores de recursos](https://msdn.microsoft.com/library/azure/dn790524.aspx).

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
- Para obtener información sobre cómo implementar recursos, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).

<!---HONumber=AcomDC_1203_2015-->