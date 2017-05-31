---
title: 'Cifrado de base de datos en reposo: Azure Cosmos DB | Microsoft Docs'
description: "Obtenga información sobre cómo Azure Cosmos DB proporciona cifrado predeterminado de todos los datos."
services: cosmosdb
author: voellm
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/31/2017
ms.author: voellm
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8248b429cf9b7fdfc709a68c4d1e88b7b660020b
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---

# <a name="azure-cosmos-db-database-encryption-at-rest"></a>Cifrado de base de datos en reposo en Azure Cosmos DB

El cifrado en reposo es una frase que se refiere normalmente al cifrado de datos en dispositivos de almacenamiento no volátil, como unidades de estado sólido (SSD) y discos duros (HDD).  Azure Cosmos DB almacena sus bases de datos principales en SSD y sus copias de seguridad y archivos adjuntos multimedia en blobs de Azure, que suelen estar respaldados por HDD.  Con el lanzamiento del cifrado en reposo para Azure Cosmos DB, todas las bases de datos, los archivos adjuntos multimedia y las copias de seguridad ahora sí están cifradas.  Esto significa que los datos ahora se cifran en tránsito (a través de la red) y en reposo (almacenamiento no volátil), lo que le ofrece un cifrado de un extremo a otro.

Como servicio PaaS, se ha trabajado mucho para que Azure Cosmos DB resulte muy fácil de usar.  Teniendo esto en cuenta, todos los datos de usuario almacenados en Azure Cosmos DB se cifran en reposo y en transporte, sin que el usuario tenga que realizar ninguna acción.  Otra opción de configuración consiste en activar de forma predeterminada el cifrado en reposo.  No hay ningún control para activar o desactivar esta opción y se ha proporcionado esta característica cumplimiento en todo momento con el [SLA de disponibilidad y rendimiento](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/).

## <a name="how-does-encryption-at-rest-work"></a>¿Cómo funciona el cifrado en reposo?

El cifrado en reposo se implementa mediante una serie de tecnologías de seguridad, como sistemas seguros de almacenamiento de claves, redes cifradas y API criptográficas.  En el diagrama siguiente se muestra cómo se separan el almacenamiento de datos cifrados y la administración de claves.  Los sistemas que descifran y procesan datos tienen que comunicarse con los sistemas que administran claves.

![Diagrama de diseño](./media/documentdb-nosql-database-encryption-at-rest/design-diagram.png)

El flujo básico de una solicitud de usuario es el siguiente:
- La cuenta de base de datos del usuario se prepara y las claves de almacenamiento se recuperan a través de una solicitud al proveedor de recursos del servicio de administración.
- Un usuario crea una conexión a Azure Cosmos DB a través de HTTPS y transporte seguro (los SDK resumen los detalles).
- Un usuario envía un documento JSON para almacenarlo a través de la conexión segura creada anteriormente.
- El documento JSON se indiza a menos que el usuario haya desactivado la indización.
- El documento JSON y los datos de índice se escriben en el almacenamiento seguro.
- Los datos se leen periódicamente desde el almacenamiento seguro y se realiza una copia de seguridad de ellos en el almacenamiento de blobs cifrado de Azure.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="q-how-much-more-does-azure-storage-cost-if-sse-is-enabled"></a>P: ¿El costo de Azure Storage sube si se habilita SSE?
R: No hay costo adicional.

### <a name="q-who-manages-the-encryption-keys"></a>P: ¿Quién administra las claves de cifrado?
R: Las administra Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>P: ¿Con qué frecuencia se alternan las claves de cifrado?
R: Microsoft tiene un conjunto de directrices internas, que sigue DocumentDB.  Aunque las instrucciones específicas no están publicadas, Microsoft publica el (Ciclo de vida de desarrollo de seguridad, también conocido como SDL) [https://www.microsoft.com/sdl/default.aspx] que se considera como un subconjunto de instrucciones internas y tiene útiles procedimientos recomendados para los desarrolladores.

### <a name="q-can-i-use-my-own-encryption-keys"></a>P: ¿Puedo usar mis propias claves de cifrado?
R: Azure Cosmos DB es un servicio de PaaS y se ha trabajado duro para que el servicio siga siendo fácil de usar.  Se ha observado que esta pregunta suele plantearse como una pregunta de proxy para cumplir una norma de cumplimiento, como PCI-DSS.  Como parte de la creación de esta característica, se ha colaborado con los auditores de cumplimiento para garantizar que los clientes que usan Azure Cosmos DB cumplan los requisitos sin tener que administrar las claves por su cuenta.
Este es el motivo por el que actualmente ofrecemos a los usuarios la opción de encargarse de la administración de claves.


### <a name="q-what-regions-have-encryption-turned-on"></a>P: ¿Qué regiones tienen activado el cifrado?
R: Todas las regiones de Azure Cosmos DB tienen activado el cifrado de todos los datos de usuario.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>P: ¿Afecta el cifrado a los Acuerdos de Nivel de Servicio de rendimiento y de latencia del rendimiento?
R: No hay ningún impacto ni cambios en el rendimiento de los Acuerdos de Nivel de Servicio ahora que está habilitado el cifrado en reposo para todas las cuentas nuevas y existentes.  Puede leer más sobre la [página del Acuerdo de Nivel de Servicio de rendimiento](https://azure.microsoft.com/support/legal/sla/documentdb) para ver las garantías más recientes.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>P: ¿Admite el emulador local admite el cifrado en reposo?
R: El emulador es una herramienta de desarrollo o pruebas independiente y no utiliza los servicios de administración de claves que usa el servicio DocumentDB. Nuestra recomendación es habilitar BitLocker en aquellas unidades de disco donde se almacenan los datos de prueba del emulador confidenciales. El (emulador admite el cambio del directorio de datos predeterminado)[https://docs.microsoft.com/azure/documentdb/documentdb-nosql-local-emulator] así como el uso de una ubicación conocida.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información general sobre la seguridad y las últimas mejoras de Azure Cosmos DB, consulte [Seguridad de bases de datos de Azure Cosmos DB](documentdb-nosql-database-security.md).

Si quiere saber más sobre las certificaciones de Microsoft, visite el [Centro de confianza de Azure](https://azure.microsoft.com/en-us/support/trust-center/).

