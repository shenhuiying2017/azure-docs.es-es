---
title: Servicios disponibles de Azure Government | Microsoft Docs
description: "Proporciona información general de los servicios disponibles en Azure Government."
services: azure-government
cloud: gov
documentationcenter: 
author: smichelotti
manager: liki
ms.assetid: a453a23c-bc0f-4203-9075-0f579dea7e23
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: stemi
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 21c2a0faad87b84058093f02c831b374a644b4b6
ms.lasthandoff: 03/06/2017


---
# <a name="available-services-on-azure-government"></a>Servicios disponibles en Azure Government
Azure Government está expandiendo continuamente los servicios que están disponibles.  Estos servicios se implementan con el mismo código que el usado en Azure público.  Esta sección documenta los servicios que están actualmente disponibles en Azure Government, incluidos los dos tipos principales de información:

* **Variaciones:** variaciones debido a características que aún no se han implementado, o propiedades (por ejemplo, direcciones URL) que son únicas en el entorno del gobierno.  
* **Consideraciones:** detalles de implementación específicos de Government para garantizar que los datos permanecen dentro del límite de cumplimiento.

Todo lo que necesita saber sobre estos servicios se puede encontrar en su documentación general.

Para obtener la lista más reciente de servicios, consulte los [productos por región](https://azure.microsoft.com/regions/services/). 

En las tablas siguientes, los servicios especificados como Resource Manager habilitado tienen proveedores de recursos y se pueden administrar mediante PowerShell. Para obtener más información sobre los proveedores de Resource Manager, las versiones de API y los esquemas, consulte [este artículo](../azure-resource-manager/resource-manager-supported-services.md). Los servicios especificados como disponibles en el portal se pueden administrar en el portal de [Azure Government](https://portal.azure.us/). 


## <a name="computedocumentation-government-computemd"></a>[Proceso](documentation-government-compute.md)

| Servicio | Administrador de recursos habilitado | Portal |
| --- | --- | --- |
| [Máquinas virtuales](documentation-government-compute.md#virtual-machines) | Sí | Sí |
| Lote | Sí | Sí |
| Servicios en la nube | Sí | Sí |
| Service Fabric | Sí | Sí |
| Conjuntos de escalas de máquina virtual | Sí | Sí |


## <a name="networkingdocumentation-government-networkingmd"></a>[Redes](documentation-government-networking.md)

| Servicio | Administrador de recursos habilitado | Portal |
| --- | --- | --- |
| [ExpressRoute](documentation-government-networking.md#expressroute-private-connectivity) | Sí | Sí |
| Red virtual | Sí | Sí |
| [Load Balancer](documentation-government-networking.md#support-for-load-balancer) | Sí | Sí |
| [Traffic Manager](documentation-government-networking.md#support-for-traffic-manger) | Sí | Sí |
| [VPN Gateway](documentation-government-networking.md#support-for-vpn-gateway) | Sí | Sí |
| Puerta de enlace de aplicaciones | Sí | Sí |
| ExpressRoute | Sí | Sí |



## <a name="storagedocumentation-government-services-storagemd"></a>[Almacenamiento](documentation-government-services-storage.md)

| Servicio | Administrador de recursos habilitado | Portal |
| --- | --- | --- |
| [Storage: blobs](documentation-government-services-storage.md#azure-storage) | Sí | Sí |
| [Storage: tablas](documentation-government-services-storage.md#azure-storage) | Sí | Sí |
| [Storage: colas](documentation-government-services-storage.md#azure-storage) | Sí | Sí |
| [Storage: archivos](documentation-government-services-storage.md#azure-storage) | Sí | Sí |
| [Storage: discos](documentation-government-services-storage.md#azure-storage) | Sí | Sí |
| [StorSimple](documentation-government-services-storage.md) | Sí | Sí |
| [Copia de seguridad](documentation-government-services-storage.md#azure-storage) | Sí | Sí |
| [Recuperación de sitios](documentation-government-services-storage.md#azure-storage) | Sí | Sí |
| [Importación/Exportación](documentation-government-services-storage.md#azure-storage) | Sí | No |



## <a name="web--mobiledocumentation-government-services-webandmobilemd"></a>[Web y móvil](documentation-government-services-webandmobile.md)

| Servicio | Administrador de recursos habilitado | Portal |
| --- | --- | --- |
| [App Service: aplicaciones web](documentation-government-services-webandmobile.md#app-services) | Sí | Sí |
| [App Service: aplicaciones de API](documentation-government-services-webandmobile.md#app-services) | Sí | Sí |
| [App Service: aplicaciones móviles](documentation-government-services-webandmobile.md#app-services) | Sí | Sí |
| Servicios multimedia | Sí | Sí |


## <a name="databasesdocumentation-government-services-databasemd"></a>[Bases de datos](documentation-government-services-database.md)

| Servicio | Administrador de recursos habilitado | Portal |
| --- | --- | --- |
| [Base de datos SQL](documentation-government-services-database.md#sql-database) | Sí | Sí |
| SQL Data Warehouse | Sí | Sí |
| SQL Server Stretch Database | Sí | Sí |
| [Redis Cache](documentation-government-services-database.md#azure-redis-cache) | Sí | Sí |


## <a name="intelligence--analyticsdocumentation-government-services-intelligenceandanalyticsmd"></a>[Inteligencia y análisis](documentation-government-services-intelligenceandanalytics.md)

| Servicio | Administrador de recursos habilitado | Portal |
| --- | --- | --- |
| [HDInsights](documentation-government-services-intelligenceandanalytics.md#hdinsight) | Sí | Sí |
| [Power BI Pro](documentation-government-services-intelligenceandanalytics.md#power-bi) | No | No (Portal de administración de Office 365) |


## <a name="internet-of-things-iot"></a>Internet de las cosas (IoT)

| Servicio | Administrador de recursos habilitado | Portal |
| --- | --- | --- |
| Centros de eventos | Sí | Sí |
| Centros de notificaciones | No | No (vaya al [portal heredado](https://manage.windowsazure.us/)) |


## <a name="enterprise-integration"></a>Enterprise Integration

| Servicio | Administrador de recursos habilitado | Portal |
| --- | --- | --- |
| SERVICE BUS | Sí | Sí |
| [StorSimple](documentation-government-services-storage.md) | Sí | Sí |
| SQL Server Stretch Database | Sí | Sí |



## <a name="security--identitydocumentation-government-services-securityandidentitymd"></a>[Seguridad + Identidad](documentation-government-services-securityandidentity.md)

| Servicio | Administrador de recursos habilitado | Portal |
| --- | --- | --- |
| Azure Active Directory | Sí | Sí |
| [Key Vault](documentation-government-services-securityandidentity.md#key-vault) | Sí | No (próximamente) |
| Multi-Factory Authentication | Sí | Sí |


## <a name="intelligence--analytics"></a>Inteligencia y análisis

| Servicio | Administrador de recursos habilitado | Portal |
| --- | --- | --- |
| Power BI | Sí | No |
| HDInsight | Sí | Sí |



## <a name="monitoring--managementdocumentation-government-services-monitoringandmanagementmd"></a>[Supervisión + Administración](documentation-government-services-monitoringandmanagement.md)

| Servicio | Administrador de recursos habilitado | Portal |
| --- | --- | --- |
| [Automatización](documentation-government-services-monitoringandmanagement.md#automation) | Sí | Sí |
| [Copia de seguridad](documentation-government-services-backup.md) | Sí | Sí |
| [Log Analytics](documentation-government-services-monitoringandmanagement.md#log-analytics) | Sí | Sí |
| [Recuperación de sitios](documentation-government-services-monitoringandmanagement.md#site-recovery) | Sí | Sí |
| Programador | Sí | No |
| Supervisión y diagnóstico | Sí | Sí |




## <a name="next-steps"></a>Pasos siguientes
Para obtener información complementaria y actualizaciones, suscríbase al [blog de Microsoft Azure Government](https://blogs.msdn.microsoft.com/azuregov/).


