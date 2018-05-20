---
title: Información general sobre la biblioteca de BulkExecutor de Azure Cosmos DB | Microsoft Docs
description: Obtenga información sobre la biblioteca de BulkExecutor de Azure Cosmos DB, las ventajas derivadas del uso de la biblioteca y su arquitectura.
keywords: Ejecutor en masa de Java
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: d395376ad6cf191f8f355f6308f27e525da2911f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="azure-cosmos-db-bulkexecutor-library-overview"></a>Información general sobre la biblioteca de BulkExecutor de Azure Cosmos DB
 
Azure Cosmos DB es un servicio de base de datos rápido, flexible y distribuido globalmente que está diseñado para escalar horizontalmente de forma flexible, a fin de admitir: 

* Grandes capacidades de proceso de lectura y escritura (millones de operaciones por segundo).  
* Almacenamiento de grandes volúmenes de (cientos de terabytes o incluso más) y datos transaccionales y operativos con una latencia predecible de milisegundos.  

La biblioteca de BulkExecutor permite aprovechar esta capacidad de proceso y almacenamiento en masa. Además, permite realizar operaciones en masa en Azure Cosmos DB mediante las API de actualización e importación masivas. Puede obtener más información sobre las características de la biblioteca de BulkExecutor en las siguientes secciones. 

> [!NOTE] 
> Actualmente, la biblioteca de Bulkxecutor admite las operaciones de importación y actualización y solo es compatible con las cuentas de API de SQL de Azure Cosmos DB. Vea las notas de la versión de [.NET](sql-api-sdk-bulk-executor-dot-net.md) y [Java](sql-api-sdk-bulk-executor-java.md) para consultar las actualizaciones de la biblioteca.
 
## <a name="key-features-of-the-bulkexecutor-library"></a>Características clave de la biblioteca de BulkExecutor  
 
* Reduce significativamente los recursos de proceso del lado cliente necesarios para saturar la capacidad de proceso asignada a un contenedor. Una sola aplicación multiproceso que escribe datos mediante la API de importación masiva logra diez veces más capacidad de proceso de escritura en comparación con una aplicación multiproceso que escribe datos en paralelo mientras se satura la CPU del equipo cliente.  

* Abstrae las tareas tediosas de escribir lógica de aplicación para controlar la limitación de solicitudes, los tiempo de expiración de las solicitudes y otras excepciones transitorias mediante un control eficaz de ellas en la biblioteca.  

* Proporciona un mecanismo simplificado para aplicaciones que realizan operaciones en masa para escalabilidad horizontal. Una única instancia de BulkExecutor en ejecución en una máquina virtual de Azure puede consumir más de 500 K RU/s y puede conseguir mayor velocidad de la capacidad de proceso con la adición de instancias adicionales a las máquinas virtuales clientes individuales.  
 
* Puede importar en masa más de un terabyte de datos en una hora con el uso de una arquitectura de escalabilidad horizontal.  

* Puede actualizar en masa los datos existentes en los contenedores de Azure Cosmos DB como revisiones. 
 
## <a name="how-does-the-bulk-executor-operate"></a>¿Cómo funciona el ejecutor en masa? 

Cuando se desencadena una operación en masa para importar o actualizar documentos con un lote de entidades, inicialmente se ordenan aleatoriamente en cubos correspondientes a su rango con clave de particiones de Azure Cosmos DB. Dentro de cada cubo correspondiente a un rango con clave de particiones, se dividen en mini lotes y cada mini lote actúa como una carga útil que se confirma en el lado del servidor. La biblioteca de BulkExecutor tiene optimizaciones integradas para la ejecución simultánea de estos mini lotes dentro y entre los rangos con clave de particiones. En la siguiente imagen se ilustra cómo BulkExecutor procesa los datos por lotes en diferentes claves de particiones:  

![Arquitectura del ejecutor en masa](./media/bulk-executor-overview/bulk-executor-architecture.png)

La biblioteca de BulkExecutor se asegura de la máxima utilización de la capacidad de proceso asignada a una colección. Usa un  [mecanismo de control de congestión del estilo de AIMD](https://tools.ietf.org/html/rfc5681) para que cada rango con clave de particiones de Azure Cosmos DB controle con eficacia las limitaciones y los tiempos de expiración. 

## <a name="next-steps"></a>Pasos siguientes 
  
* Puede aprender más si prueba las aplicaciones de ejemplo que usan la biblioteca de BulkExecutor en [.NET](bulk-executor-dot-net.md) y [Java](bulk-executor-java.md).  
* Consulte la información sobre el SDK y las notas de la versión de BulkExecutor en [.NET](sql-api-sdk-bulk-executor-dot-net.md) y [Java](sql-api-sdk-bulk-executor-java.md).
* La biblioteca de BulkExecutor está integrada en el conector de Spark a Cosmos DB; para más información, vea el artículo sobre el [conector de Spark a Azure Cosmos DB](spark-connector.md).  
* La biblioteca de BulkExecutor también está integrada en una nueva versión del [conector de Azure Cosmos DB](https://aka.ms/bulkexecutor-adf-v2) para que Azure Data Factory copie datos.
