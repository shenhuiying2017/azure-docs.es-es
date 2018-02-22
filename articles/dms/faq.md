---
title: "Preguntas más frecuentes sobre el uso de Azure Database Migration Service | Microsoft Docs"
description: "Conozca las preguntas más frecuentes sobre cómo usar Azure Database Migration Service para realizar migraciones de base de datos."
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: 3c1c259cc58eb1adab39d9c0ca376726b798186e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="faq-about-using-the-azure-database-migration-service"></a>Preguntas más frecuentes sobre el uso de Azure Database Migration Service
En este artículo aparecen las preguntas más comunes sobre el uso de Azure Database Migration Service junto con las respuestas relacionadas.

### <a name="q-what-is-azure-database-migration-service"></a>P: ¿Qué es Azure Database Migration Service?
Azure Database Migration Service es un servicio totalmente administrado diseñado para permitir migraciones completas desde varios orígenes de base de datos hasta las plataformas de datos de Azure con un tiempo de inactividad mínimo. El servicio está actualmente en versión preliminar pública y el trabajo de desarrollo está centrado en los siguientes aspectos:
- Confiabilidad y rendimiento
- Adición iterativa de pares de origen-destino
- Inversión continuada en migraciones libres de problemas

### <a name="q-what-source-target-pairs-does-the-azure-database-migration-service-currently-support"></a>P: ¿Qué pares origen-destino admite actualmente Azure Database Migration Service?
El servicio en versión preliminar pública actualmente admite migraciones desde SQL Server a Azure SQL Database y ahora se puede ir a Azure Portal para comenzar a usar Azure Database Migration Service para este escenario. Otros pares origen-destino, como SQL Server a Instancia administrada de Azure SQL Database y Oracle a Azure SQL Database, están disponibles a través de una versión preliminar privada. Si desea una oportunidad para participar en la versión preliminar privada limitada de estos escenarios, suscríbase [aquí](https://sqldatabase-migrationpreview.azurewebsites.net/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-other-microsoft-database-migration-tools-such-as-the-database-migration-assistant-dma-or-sql-server-migration-assistant-ssma"></a>P: ¿Cómo se compara Azure Database Migration Service con otras herramientas de migración de bases de datos de Microsoft, como Database Migration Assistant (DMA) o SQL Server Migration Assistant (SSMA)?
Azure Database Migration Service es el método preferido para la migración de bases de datos a Microsoft Azure a escala. Para más detalles sobre cómo se compara Azure Database Migration Service con otras herramientas de migración de bases de datos de Microsoft y para recomendaciones sobre cómo usar el servicio en distintos escenarios, consulte esta entrada de blog sobre [cómo diferenciar los servicios y las herramientas de migración de bases de datos de Microsoft](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-the-azure-migrate-offering"></a>P: ¿Cómo se comparar Azure Database Migration Service con la oferta de Azure Migrate?
El servicio Azure Migrate Service ayuda con la migración de máquinas virtuales locales a IaaS de Azure. El servicio evalúa la idoneidad de la migración y el ajuste de tamaño basado en el rendimiento, y proporciona estimaciones del costo que supone la ejecución de máquinas virtuales locales en Azure. Azure Migrate es útil para las migraciones mediante lift-and-shift de cargas de trabajo basadas en VM locales a máquinas virtuales de IaaS de Azure. Sin embargo, a diferencia de Azure Database Migration Service, Azure Migrate no es una oferta de servicios de migración de bases de datos especializada para las plataformas de bases de datos relacionales de PaaS de Azure, como Azure SQL Database, SQL Azure o Instancia administrada de Azure SQL Database.

### <a name="q-what-is-a-summary-of-the-steps-required-to-use-the-azure-database-migration-service-to-perform-a-database-migration"></a>P: ¿Cuál es un resumen de los pasos que se necesitan para usar Azure Database Migration Service para realizar una migración de bases de datos?
Durante una migración de base de datos sencilla típica, debe:
1.  Crear bases de datos de destino.
2.  Migrar el esquema de las bases de datos mediante [Database Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=53595).
3.  Crear una instancia de Azure Database Migration Service.
4.  Crear un proyecto de migración mediante la especificación de las bases de datos de origen, las bases de datos de destino y las tablas que se migrarán.
5.  Iniciar toda la carga.
6.  Seleccionar la validación posterior.
7.  Realizar un cambio manual del entorno de producción a la nueva base de datos basada en la nube. 

### <a name="q-what-are-the-prerequisites-for-using-the-azure-database-migration-service"></a>P: ¿Cuáles son los requisitos previos para usar Azure Database Migration Service?
Hay varios requisitos previos necesarios para garantizar que Azure Database Migration Service se ejecute sin problemas al realizar migraciones de bases de datos. Algunos de los requisitos previos se aplican en todos los escenarios (pares origen-destino) compatibles con el servicio, mientras que otros son exclusivos para un escenario específico.
Los requisitos de Azure Database Migration Service que son comunes en todos los escenarios de migración compatibles incluyen la necesidad de:
- Crear una red virtual para Azure Database Migration Service mediante el modelo de implementación de Azure Resource Manager, que proporciona conectividad de sitio a sitio a los servidores de origen local utilizando [ExpressRoute](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Asegúrese de que el grupo de seguridad de red de Azure Virtual Network (VNET) no bloquea los puertos de comunicación 443, 53, 9354, 445 y 12000. Para obtener información más detallada sobre el filtrado de tráfico con NSG de Azure VNET, vea el artículo [Filtrado del tráfico de red con grupos de seguridad de red](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg).
- Cuando se usa un dispositivo de firewall frente a las bases de datos de origen, puede que sea necesario agregar reglas de firewall para permitir que Azure Database Migration Service acceda a las bases de datos de origen para realizar la migración.
 
Para una lista de todos los requisitos previos que se necesitan para completar los escenarios de migración específicos mediante Azure Database Migration Service, consulte los tutoriales relacionados en la [documentación](https://docs.microsoft.com/en-us/azure/dms/dms-overview) de Azure Database Migration Service que se encuentra en docs.microsoft.com.

### <a name="q-how-do-i-find-the-ip-address-for-the-azure-database-migration-service-so-that-i-can-create-an-allow-list-for-the-firewall-rules-used-to-access-my-source-database-for-migration"></a>P: ¿Cómo busco la dirección IP de Azure Database Migration Service para poder crear una lista de las reglas de firewall permitidas que se usa para acceder a mi base de datos de origen para la migración?
Puede que tenga que agregar reglas de firewall para permitir que Azure Database Migration Service acceda a la base de datos de origen para la migración. La dirección IP del servicio es dinámica, pero si se usa ExpressRoute, esta dirección la asigna la red corporativa de manera privada. La manera más sencilla de identificar la dirección IP adecuada es buscar en el mismo grupo de recursos del recurso de Azure Database Migration Service aprovisionado para buscar la interfaz de red asociada. Habitualmente, el nombre del recurso de interfaz de red comienza con el prefijo NIC y continúa con una secuencia única de caracteres y números, como NIC-jj6tnztnmarpsskr82rbndyp. Al seleccionar este recurso de interfaz de red, puede ver la dirección IP que se debe incluir en la lista de permitidos en la página de información general de los recursos de Azure Portal.

Puede que también tenga que incluir en la lista de permitidos el origen de puerto en que SQL Server escucha. De manera predeterminada, se trata del puerto 1433, pero SQL Server de origen también puede estar configurado para escuchar en otros puertos. En este caso, debe incluir también estos puertos en la lista de permitidos. Puede determinar el puerto en que SQL Server escuchar mediante una consulta de vista de administración dinámica:

```sql
    SELECT DISTINCT 
        local_tcp_port 
    FROM sys.dm_exec_connections 
    WHERE local_tcp_port IS NOT NULL
```
También puede determinar el puerto en que SQL Server escucha mediante una consulta del registro de errores de SQL Server:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on' 
    GO
```

### <a name="q-are-there-any-recommendations-for-optimizing-the-performance-of-the-azure-database-migration-service"></a>P: ¿Existen recomendaciones sobre cómo optimizar el rendimiento de Azure Database Migration Service?
Hay algunas opciones que puede realizar para acelerar la migración de las bases de datos mediante el servicio:
- Use el plan de tarifa de uso general de varias CPU cuando cree la instancia de servicio para permitir que el servicio aproveche las diversas CPU virtuales para la paralelización y realizar una transferencia de datos más rápida.
- De manera temporal, escale verticalmente la instancia de destino de Azure SQL Database a la SKU de nivel Premium durante la operación de migración de datos para minimizar la limitación de Azure SQL Database que puede afectar las actividades de transferencia de datos al usar las SKU de nivel inferior.

### <a name="q-how-do-i-set-up-an-azure-virtual-network"></a>P: ¿Cómo se configura una instancia de Azure Virtual Network?
Si bien existen varios tutoriales de Microsoft que pueden guiarlo en el proceso de configurar una red virtual de Azure, la documentación oficial aparece en el artículo [Azure Virtual Network](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview).

### <a name="q-why-is-my-azure-database-migration-service-unavailable-or-stopped"></a>P: ¿Por qué mi instancia de Azure Database Migration Service no está disponible o está detenida?
Si el usuario detiene explícitamente la instancia de Azure Database Migration Service (DMS) o si el servicio está inactivo durante un período de 24 horas, este estará en pausa o detenido. En cada caso, el servicio no estará disponible y se encontrará en estado detenido.  Para reanudar las migraciones activas, reinicie el servicio.

### <a name="q-where-can-i-leave-feedback-about-the-azure-database-migration-service"></a>P: ¿Dónde puedo dejar comentarios sobre Azure Database Migration Service?
Queremos saber de usted. Envíe sus comentarios e ideas sobre Azure Database Migration Service a través de UserVoice, [aquí](https://feedback.azure.com/forums/906100-azure-database-migration-service).

## <a name="next-steps"></a>pasos siguientes
Para información general sobre Azure Database Migration Service y la disponibilidad regional durante la versión preliminar pública, consulte el artículo [¿Qué es la versión preliminar de Azure Database Migration Service?](dms-overview.md) 