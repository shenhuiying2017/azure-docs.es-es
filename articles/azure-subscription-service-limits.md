---
title: "Límites y cuotas de suscripción de Azure | Microsoft Docs"
description: "Se proporciona una lista de límites, cuotas y restricciones de suscripción y servicio comunes de Azure. Esto incluye información acerca de cómo aumentar los límites junto con los valores máximos."
services: 
documentationcenter: 
author: rothja
manager: jeffreyg
editor: 
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: byvinyal
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 13f968b5eeaeb74c6364b9b7c29651657f73e052
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017


---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure
Este documento enumeran algunos de los límites más comunes de Microsoft Azure, que a veces se denominan cuotas. Actualmente, este documento no cubre todos los servicios de Azure. Con el tiempo, esta lista se expandirá y actualizará para abarcar más de la plataforma.

Visite [Precios de Azure de un vistazo](https://azure.microsoft.com/pricing/) para más información sobre precios de Azure. Allí, puede calcular los costos mediante la [Calculadora de precio](https://azure.microsoft.com/pricing/calculator/)s o visitando la página de detalles de precios para un servicio (por ejemplo, [Máquinas virtuales Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)). Si quiere obtener sugerencias para ayudar a administrar los costos, vea [Prevención de costos inesperados con la administración de costos y facturación de Azure](billing/billing-getting-started.md).

> [!NOTE]
> Si desea aumentar el límite o la cuota por encima del **Límite predeterminado**, [puede abrir una solicitud de soporte técnico al cliente en línea sin cargo alguno](azure-supportability/resource-manager-core-quotas-request.md). Los límites no se pueden subir por encima del valor de **Límite máximo** que se muestra en las tablas siguientes. Si la columna **Límite máximo** no existe, el recurso no tiene límites ajustables. 
> 
> Las suscripciones de evaluación gratuita no son aptas para aumentar el límite ni la cuota. Si tiene una versión de evaluación gratuita, puede actualizar a una suscripción de [Pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/) . Para obtener más información, consulte [Actualización de evaluación gratuita de Azure a pago por uso](billing/billing-upgrade-azure-subscription.md).
> 

## <a name="limits-and-the-azure-resource-manager"></a>Límites y Azure Resource Manager
Ahora es posible combinar varios recursos de Azure en un único grupo de recursos de Azure. Al utilizar grupos de recursos, los límites que una vez fueron globales se convierten en administrados a nivel regional con Azure Resource Manager. Para más información sobre los grupos de recursos de Azure, consulte [Información general de Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

En los límites siguientes, se ha agregado una nueva tabla para reflejar las diferencias en los límites cuando se usa Azure Resource Manager. Por ejemplo, hay una tabla de **Límites de suscripción** y una tabla de **Límites de suscripción - Azure Resource Manager**. Cuando un límite se aplica a ambos escenarios, solo se muestra en la primera tabla. A menos que se indique lo contrario, los límites son globales en todas las regiones.

> [!NOTE]
> Es importante destacar que las cuotas de los recursos de los grupos de recursos de Azure son accesibles para su suscripción en función de la región y no en función de la suscripción, como las cuotas de administración de servicios. Usemos las cuotas de núcleo como ejemplo. Si necesita solicitar un aumento de cuota con compatibilidad para núcleos, deberá decidir el número de núcleos que desea usar en las distintas regiones y, a continuación, realizar una solicitud específica para las cuotas principales del grupo de recursos de Azure para las cantidades y regiones que desee. Por lo tanto, si necesita usar 30 núcleos en Europa Occidental para ejecutar la aplicación, deberá solicitar específicamente 30 núcleos en Europa Occidental. Pero no tendrá un aumento de la cuota de núcleos en ninguna otra región: solo Europa Occidental tendrá la cuota de 30 núcleos.
> <!-- -->
> Como resultado, puede que le resulte útil considerar decidir cuáles deben ser sus cuotas de grupos de recursos de Azure para su carga de trabajo en cada región, y solicitar esa cantidad en cada región en la que está considerando efectuar la implementación. Consulte [solucionar problemas de implementación](resource-manager-common-deployment-errors.md) para obtener más ayuda para descubrir las cuotas actuales para regiones específicas.
> 
> 

## <a name="service-specific-limits"></a>Límites específicos del servicio
* [Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Automation](#automation-limits)
* [Azure Redis Cache](#azure-redis-cache-limits)
* [Azure RemoteApp](#azure-remoteapp-limits)
* [Backup](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [SERVICIO CDN](#cdn-limits)
* [Cloud Services](#cloud-services-limits)
* [Factoría de datos](#data-factory-limits)
* [Análisis de Data Lake](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [DNS](#dns-limits)
* [DocumentDB](#documentdb-limits)
* [Event Hubs](#event-hubs-limits)
* [IoT Hub](#iot-hub-limits)
* [Key Vault](#key-vault-limits)
* [Log Analytics/Operational Insights](#log-analytics-limits)
* [Media Services](#media-services-limits)
* [Mobile Engagement](#mobile-engagement-limits)
* [Mobile Services](#mobile-services-limits)
* [Supervisión](#monitor-limits)
* [Multi-Factor Authentication](#multi-factor-authentication)
* [Redes](#networking-limits)
* [Network Watcher](#network-watcher-limits)
* [Servicio Notification Hubs](#notification-hub-service-limits)
* [Grupo de recursos](#resource-group-limits)
* [Scheduler](#scheduler-limits)
* [Search](#search-limits)
* [Service Bus](#service-bus-limits)
* [Recuperación de sitios](#site-recovery-limits)
* [SQL Database](#sql-database-limits)
* [Storage](#storage-limits)
* [Sistema de StorSimple](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Suscripción](#subscription-limits)
* [Traffic Manager](#traffic-manager-limits)
* [Virtual Machines](#virtual-machines-limits)
* [Conjuntos de escalado de máquina virtual](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Límites de suscripción
#### <a name="subscription-limits"></a>Límites de suscripción
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Límites de suscripción - Azure Resource Manager
Los límites siguientes se aplican al usar Azure Resource Manager y los grupos de recursos de Azure. Los límites que no han cambiado con Azure Resource Manager no se enumeran a continuación. Consulte la tabla anterior para obtener información acerca de esos límites.

Para información sobre el control de límites en las solicitudes de Resource Manager, consulte ///[Throttling Resource Manager requests](resource-manager-request-limits.md) (Limitación de las solicitudes de Resource Manager).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Límites de grupos de recursos
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Límites de Virtual Machines
#### <a name="virtual-machine-limits"></a>Límites de Virtual Machines
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Límites de Virtual Machines: Azure Resource Manager
Los límites siguientes se aplican al usar Azure Resource Manager y los grupos de recursos de Azure. Los límites que no han cambiado con Azure Resource Manager no se enumeran a continuación. Consulte la tabla anterior para obtener información acerca de esos límites.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Límites de los conjuntos de escalas de máquinas virtuales
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="networking-limits"></a>Límites de red
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Límites de red
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Límites de Application Gateway
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Límites de Network Watcher
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Límites de Traffic Manager
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>Límites de DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>Límites de Storage
Para más información sobre los límites de la cuenta de almacenamiento, vea [Objetivos de escalabilidad y rendimiento de Azure Storage](storage/storage-scalability-targets.md).
<!--like # storage accts --> 
#### <a name="storage-service-limits"></a>Límites del servicio de Storage
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Límites de discos de máquinas virtuales 
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Consulte [Tamaños de máquina virtual](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obtener información adicional.

#### <a name="managed-virtual-machine-disks"></a>Discos de máquinas virtuales administrados

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Discos de máquinas virtuales no administrados

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### <a name="storage-resource-provider-limits"></a>Límites de proveedor de recursos de Storage
[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="cloud-services-limits"></a>Límites de Cloud Services
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>Límites de App Service
Entre los siguientes límites de App Service se incluyen límites para Web Apps, Mobile Apps, API Apps y Logic Apps.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Límites de Scheduler
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Límites de Batch
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Límites de BizTalk Services
La tabla siguiente muestra los límites de Azure BizTalk Services.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="documentdb-limits"></a>Límites de DocumentDB
DocumentDB es una base de datos de escala global en el que se pueden escalar el rendimiento y almacenamiento para gestionar todo lo que requiera la aplicación. Si tiene alguna pregunta sobre la escala que DocumentDB proporciona, envíe un correo electrónico a askdocdb@microsoft.com.

### <a name="mobile-engagement-limits"></a>Límites de Mobile Engagement
[!INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]

### <a name="search-limits"></a>Límites de Search
Los planes de tarifa determinan la capacidad y los límites de su servicio de búsqueda. Los planes incluyen:

* *Gratis* , compartido con otros suscriptores de Azure, se ha diseñado para proyectos de evaluación y de desarrollo de pequeña envergadura.
* *Básico* proporciona recursos informáticos dedicados para cargas de trabajo de producción en una escala menor, con hasta tres réplicas para cargas de trabajo de consulta de alta disponibilidad.
* *Estándar (S1, S2, S3, S3 de alta densidad)* es para mayores cargas de trabajo de producción. Existen varios niveles dentro del nivel estándar para que pueda elegir una configuración de recursos para escenarios que se adapte mejor al perfil de la carga de trabajo.

**Límites por suscripción**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Límites por servicio de búsqueda**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Para más información sobre otros límites, incluido el tamaño de documento, las consultas por segundo, las claves, las solicitudes y las respuestas, consulte [Límites de servicio en Azure Search](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Límites de Media Services
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>Límites de red CDN
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Límites de Mobile Services
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>Límites de Monitor
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Límites de servicio de Notification Hubs
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Límites de Event Hubs
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Límites de Service Bus
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Límites de IoT Hub
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="data-factory-limits"></a>Límites de Data Factory
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Límites de Data Lake Analytics
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Límites de Data Lake Store
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="stream-analytics-limits"></a>Límites de Stream Analytics
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Límites de Active Directory
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="azure-remoteapp-limits"></a>Límites de Azure RemoteApp
[!INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### <a name="storsimple-system-limits"></a>Límites del sistema StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Límites de Log Analytics
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Límites de Backup
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>Límites de Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Límites de Application Insights
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Límites de API Management
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Límites de Azure Redis Cache
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Límites de Key Vault
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Límites de Automation
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>Límites de SQL Database
Para conocer los límites de SQL Database, vea [Límites de recursos de SQL Database](sql-database/sql-database-resource-limits.md).

## <a name="see-also"></a>Otras referencias
[Concepto de límites de Azure y aumento de los mismos](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Tamaños de máquinas virtuales y servicios en la nube de Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Tamaños de Cloud Services](cloud-services/cloud-services-sizes-specs.md)


