---
title: Tipos de recursos que compatibles con Azure Resource Health | Microsoft Docs
description: "Tipos de recursos que se admiten a través de Azure Resource Health"
services: Resource health
documentationcenter: 
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 10/09/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: b67e72af8a34799af97cd46f968636050f4ce485
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Tipos de recursos y comprobaciones de estado en Azure Resource Health
A continuación se muestra una lista completa de todas las comprobaciones que se ejecutan a través de Resource Health por tipos de recursos.

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Comprobaciones ejecutadas|
|---|
|<ul><li>¿El servicio API Management está en funcionamiento?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Comprobaciones ejecutadas|
|---|
|<ul><li>¿Funcionan todos los nodos de la memoria caché?</li><li>¿Se puede acceder a la memoria caché desde el centro de datos?</li><li>¿Ha alcanzado la memoria caché el número máximo de conexiones?</li><li> ¿Ha agotado la caché su memoria disponible? </li><li>¿Sufre la memoria caché un gran número de errores de página?</li><li>¿Tiene mucha carga la memoria caché?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Comprobaciones ejecutadas|
|---|
|<ul> <li>¿Se ha detenido, eliminado o configurado mal alguno de los puntos de conexión?</li><li>¿Pueden acceder las operaciones de configuración de la red CDN al portal complementario?</li><li>¿Hay problemas de entrega en curso con los puntos de conexión de la red CDN?</li><li>¿Pueden los usuarios cambiar la configuración de sus recursos de la red CDN?</li><li>¿Se propagan los cambios en la configuración a la velocidad esperada?</li><li>¿Pueden los usuarios administrar la configuración de CDN mediante Azure Portal, Powershell o la API?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Comprobaciones ejecutadas|
|---|
|<ul><li>¿Está el servidor en funcionamiento?</li><li>¿Se ha completado el arranque del sistema operativo host?</li><li>¿Está l contenedor de la máquina virtual aprovisionado y encendido?</li><li>¿Hay conectividad de red entre el host y la cuenta de almacenamiento?</li><li>¿Se ha completado el arranque del SO invitado?</li><li>¿Hay mantenimiento planeado en curso?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Comprobaciones ejecutadas|
|---|
|<ul><li>¿Se puede acceder a la cuenta desde el centro de datos?</li><li>¿Está disponible el proveedor de recursos de Cognitive Services?</li><li>¿Está disponible Cognitive Services en la región adecuada?</li><li>¿Se pueden realizar operaciones de lectura en la cuenta de almacenamiento que contiene los metadatos de recursos?</li><li>¿Se ha alcanzado la cuota de llamadas a la API?</li><li>¿Se ha alcanzado el límite de lectura de las llamadas a la API?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Comprobaciones ejecutadas|
|---|
|<ul><li>¿Funciona el servidor que hospeda esta máquina virtual?</li><li>¿Se ha completado el arranque del sistema operativo host?</li><li>¿Está l contenedor de la máquina virtual aprovisionado y encendido?</li><li>¿Hay conectividad de red entre el host y la cuenta de almacenamiento?</li><li>¿Se ha completado el arranque del SO invitado?</li><li>¿Hay mantenimiento planeado en curso?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Comprobaciones ejecutadas|
|---|
|<ul><li>¿Pueden los usuarios enviar trabajos a Data Lake Analytics en la región?</li><li>¿Se ejecutan y completan correctamente los trabajos básicos en la región?</li><li>¿Pueden los usuarios enumerar elementos del catálogo en la región?</li>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Comprobaciones ejecutadas|
|---|
|<ul><li>¿Pueden los usuarios cargar datos en Data Lake Store en la región?</li><li>¿Pueden los usuarios descargar datos de Data Lake Store en la región?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs

|Comprobaciones ejecutadas|
|---|
|<ul><li>¿Se está ejecutando el centro de IoT?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Comprobaciones ejecutadas|
|---|
|<ul><li>¿Ha habido solicitudes de bases de datos o recopilaciones que no se han atendido debido a una falta de disponibilidad del servicio Azure Cosmos DB?</li><li>¿Ha habido solicitudes de documentos que no se han atendido debido a una falta de disponibilidad del servicio Azure Cosmos DB?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Comprobaciones ejecutadas|
|---|
|<ul><li>¿Está conectado el túnel VPN?</li><li>¿Existen conflictos de configuración en la conexión?</li><li>¿Están configuradas correctamente las claves precompartidas?</li><li>¿Se puede acceder al dispositivo local de VPN?</li><li>¿Hay discrepancias en la directiva de seguridad de IPSec/IKE?</li><li>¿Está la conexión VPN de sitio a sitio aprovisionada correctamente o está en estado de error?</li><li>¿Está la conexión entre redes virtuales aprovisionada correctamente o está en estado de error?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Comprobaciones ejecutadas|
|---|
|<ul><li>¿Se puede acceder desde Internet a VPN Gateway?</li><li>¿Está VPN Gateway en modo de espera?</li><li>¿Se ejecuta el servicio VPN en la puerta de enlace?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Comprobaciones ejecutadas|
|---|
|<ul><li> ¿Se pueden realizar operaciones de tiempo de ejecución, como el registro, la instalación o el envío, en el espacio de nombres?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Comprobaciones ejecutadas|
|---|
|<ul><li>¿Está el SO del host en funcionamiento?</li><li>¿Se puede acceder a workspaceCollection desde fuera del centro de datos?</li><li>¿Está disponible el proveedor de recursos de Power BI?</li><li>¿Está disponible el servicio de PowerBI en la región adecuada?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Comprobaciones ejecutadas|
|---|
|<ul><li>¿Se pueden realizar operaciones de diagnóstico en el clúster?</li></ul>|

## <a name="microsoftsqlserverdatabase"></a>Microsoft.SQL/Server/database
|Comprobaciones ejecutadas|
|---|
|<ul><li> ¿Se han producido inicios de sesión en la base de datos?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Comprobaciones ejecutadas|
|---|
|<ul><li>¿Están en funcionamiento todos los hosts en los que se ejecuta el trabajo?</li><li>¿No pudo iniciarse el trabajo?</li><li>¿Hay actualizaciones del runtime en curso?</li><li>¿Está el trabajo en un estado esperado (por ejemplo, en ejecución o detenido por el cliente)?</li><li>¿Ha encontrado el trabajo excepciones de memoria insuficiente?</li><li>¿Hay actualizaciones del proceso programado en curso?</li><li>¿Está disponible Execution Manager (plan de control)?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Comprobaciones ejecutadas|
|---|
|<ul><li>¿Está el servidor en funcionamiento?</li><li>¿Se ejecuta Internet Information Services (IIS)?</li><li>¿Funciona el equilibrador de carga?</li><li>¿Se puede acceder al plan de servicio web desde el centro de datos?</li><li>¿Hospeda la cuenta de almacenamiento el contenido de los sitios de serverFarm disponible?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Comprobaciones ejecutadas|
|---|
|<ul><li>¿Está el servidor en funcionamiento?</li><li>¿Se ejecuta Internet Information Server?</li><li>¿Funciona el equilibrador de carga?</li><li>¿Se puede acceder a la aplicación web desde el centro de datos?</li><li>¿Hospeda la cuenta de almacenamiento el contenido del sitio disponible?</li></ul>|

# <a name="next-steps"></a>Pasos siguientes
-  Consulte la [introducción a Azure Service Health](service-health-overview.md) y la [introducción a Azure Resource Health](resource-health-overview.md) para más información sobre ellos. 
-  [Preguntas más frecuentes sobre Azure Resource Health](resource-health-faq.md)
- Configure alertas de forma que se le notifiquen los problemas de estado. Para más información, consulte el artículo de [configuración de alertas para Service Health](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 