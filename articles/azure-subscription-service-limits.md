<properties
	pageTitle="Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure"
	description="Proporciona una lista de límites, cuotas y restricciones de suscripción y servicio comunes de Azure. Esto incluye información acerca de cómo aumentar los límites junto con los valores máximos."
	services=""
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettem"
	editor="cgronlun"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2015"
	ms.author="jroth"/>

# Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure

## Información general

En este documento se especifican algunos de los límites más comunes de Microsoft Azure. Tenga en cuenta que actualmente esto no cubre todos los servicios de Azure. Con el tiempo, estos límites se expandirán y actualizarán para abarcar más de la plataforma.

> [AZURE.NOTE]Si desea aumentar el límite por encima del **Límite predeterminado**, [abra una solicitud de servicio al cliente en línea sin cargo alguno](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Los límites no se pueden subir por encima del valor de **Límite máximo** que figura en las tablas siguientes. Si la columna **Límite máximo** no existe, el recurso especificado no tiene límites ajustables.


### Límites y Administrador de recursos de Azure

Ahora es posible combinar varios recursos de Azure en un único grupo de recursos de Azure. Al utilizar grupos de recursos, los límites que una vez fueron globales se convierten en administrados a nivel regional con el Administrador de recursos de Azure. Para obtener más información sobre los grupos de recursos de Azure, consulte [Uso de grupos de recursos para administrar los recursos de Azure](resource-group-portal.md).

En los límites siguientes, se ha agregado una nueva tabla para reflejar las diferencias en los límites cuando se usa el Administrador de recursos de Azure. Por ejemplo, hay una tabla de **Límites de suscripción** y una tabla de **Límites de suscripción - Administrador de recursos de Azure**. Cuando un límite se aplica a ambos escenarios, solo se muestra en la primera tabla. A menos que se indique lo contrario, los límites son globales en todas las regiones.

> [AZURE.NOTE]Es importante destacar que las cuotas de los recursos de los grupos de recursos de Azure son accesibles para su suscripción en función de la región y no en función de la suscripción, como las cuotas de administración de servicios. Usemos las cuotas de núcleo como ejemplo. Si necesita solicitar un aumento de cuota con compatibilidad para núcleos, deberá decidir el número de núcleos que desea usar en las distintas regiones y, a continuación, realizar una solicitud específica para las cuotas principales del grupo de recursos de Azure para las cantidades y regiones que desee. Por lo tanto, si necesita usar 30 núcleos en Europa Occidental para ejecutar la aplicación, deberá solicitar específicamente 30 núcleos en Europa Occidental. Pero no tendrá un aumento de la cuota de núcleos en ninguna otra región: solo Europa Occidental tendrá la cuota de 30 núcleos. <!-- --> Como resultado, puede que le resulte útil considerar el decidir cuáles deben ser sus cuotas de grupos de recursos de Azure para su carga de trabajo en cada región, y solicitar esa cantidad en cada región en la que está considerando efectuar la implementación. Consulte [solucionar problemas de implementación](resource-group-deploy-debug.md##authentication-subscription-role-and-quota-issues) para obtener más ayuda para descubrir las cuotas actuales para regiones específicas.

## Límites de suscripción

[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

### Límites de suscripción - Administrador de recursos de Azure

Los límites siguientes se aplican al usar el Administrador de recursos de Azure y los grupos de recursos de Azure. Los límites que no han cambiado con el Administrador de recursos de Azure no se enumeran a continuación. Consulte la tabla anterior para obtener información acerca de esos límites.

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


## Límites de Grupos de recursos

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]


## Límites de máquinas virtuales

[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


### Límites de máquinas virtuales - Administrador de recursos de Azure

Los límites siguientes se aplican al usar el Administrador de recursos de Azure y los grupos de recursos de Azure. Los límites que no han cambiado con el Administrador de recursos de Azure no se enumeran a continuación. Consulte la tabla anterior para obtener información acerca de esos límites.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


## Límites de red

[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]


### Límites de redes - Administrador de recursos de Azure

Los límites siguientes se aplican al usar el Administrador de recursos de Azure y los grupos de recursos de Azure. Los límites que no han cambiado con el Administrador de recursos de Azure no se enumeran a continuación. Consulte la tabla anterior para obtener información acerca de esos límites.

[AZURE.INCLUDE [azure-virtual-network-limits-azure-resource-manager](../includes/azure-virtual-network-limits-azure-resource-manager.md)]


## Límites de almacenamiento

### Límites de almacenamiento estándar 

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Para obtener más información acerca de los límites de la cuenta de almacenamiento, consulte [Objetivos de escalabilidad y rendimiento de Almacenamiento de Azure](../articles/storage/storage-scalability-targets.md).


### Límites de Almacenamiento premium

[AZURE.INCLUDE [azure-storage-limits-premium-storage](../includes/azure-storage-limits-premium-storage.md)]


### Límites de almacenamiento - Administrador de recursos de Azure

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


## Límites de Servicios en la nube

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


## Límites del Servicio de aplicaciones: aplicaciones web, aplicaciones móviles, aplicaciones de API y aplicaciones lógicas

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

## Límites de lote

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]


## Límites de DocumentDB

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]


## Límites de Mobile Engagement

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


## Límites de búsqueda

[AZURE.INCLUDE [azure-search-limits](../includes/azure-search-limits.md)]

Para obtener más detalles sobre los límites de búsqueda de Azure, consulte [Límites y restricciones](https://msdn.microsoft.com/library/azure/dn798934.aspx).

## Límites de Base de datos SQL

[AZURE.INCLUDE [azure-sql-database-limits](../includes/azure-sql-database-limits.md)]

Para obtener más detalles sobre los límites de Base de datos SQL, consulte los temas siguientes:

 - [Niveles de servicio de base de datos SQL de Azure (ediciones)](http://msdn.microsoft.com/library/azure/dn741340.aspx)
 - [Niveles de servicio y niveles de rendimiento de la Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/dn741336.aspx)
 - [Cuotas de la Unidad de rendimiento de base de datos (DTU)](http://msdn.microsoft.com/library/azure/ee336245.aspx#DTUs)
 - [Límites de recursos de Base de datos SQL](sql-database/sql-database-resource-limits.md)

## Límites de Servicios multimedia

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

## Límites de Servicios móviles

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

## Límites de Bus de servicio

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

## Límites de Análisis de transmisiones

[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

## Límites de Active Directory

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


## Límites de Azure RemoteApp

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

## Límites del sistema de StorSimple

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


## Límites de Visión operativa

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

## Límites de Copia de seguridad

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

## Límites de Site Recovery

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

## Límites de Administración de API

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

## Límites de Caché en Redis de Azure

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

## Límites del Almacén de claves

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

## Multi-Factor Authentication
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

## Otras referencias

[Concepto de límites de Azure y aumento de los mismos](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Tamaños de máquinas virtuales y servicios en la nube de Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx)

<!---HONumber=July15_HO4-->