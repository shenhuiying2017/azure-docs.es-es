---
title: 'Cifrado de bases de datos en reposo: Azure Cosmos DB | Microsoft Docs'
description: "Obtenga información sobre cómo Azure Cosmos DB proporciona cifrado predeterminado de todos los datos."
services: cosmos-db
author: voellm
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: voellm
ms.openlocfilehash: d8967d4504a8ccabb444c7f3d5635e2d00f287c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-database-encryption-at-rest"></a>Cifrado de base de datos en reposo en Azure Cosmos DB

El cifrado en reposo es una frase que se refiere normalmente al cifrado de datos en dispositivos de almacenamiento permanente, como unidades de estado sólido (SSD) y discos duros (HDD). Cosmos DB almacena sus bases de datos principales en unidades SSD. Sus elementos multimedia adjuntos y las copias de seguridad se almacenan en Azure Blob Storage, cuyos archivos de copia de seguridad suelen encontrarse en unidades HDD. Con el lanzamiento del cifrado en reposo para Cosmos DB, todas las bases de datos, los elementos multimedia adjuntos y las copias de seguridad están cifrados. Ahora, los datos están cifrados en tránsito (por la red) y en reposo (almacenamiento permanente), lo que le proporciona cifrado de un extremo a otro.

Como servicio de PaaS que es, Cosmos DB es muy sencillo de usar. Dato que todos los datos de usuario almacenados en Cosmos DB están cifrados en reposo y en tránsito, no es necesario hacer nada. Otra forma de decirlo es que el cifrado en reposo está "activado" de forma predeterminada. No hay ningún mando para activarlo o desactivarlo. Ofrecemos esta característica a la vez que continuamos cumpliendo con nuestros [Acuerdos de Nivel de Servicio con respecto a disponibilidad y rendimiento](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="implement-encryption-at-rest"></a>Implementación de cifrado en reposo

El cifrado en reposo se implementa mediante una serie de tecnologías de seguridad, como sistemas seguros de almacenamiento de claves, redes cifradas y API criptográficas. Los sistemas que descifran y procesan datos tienen que comunicarse con los sistemas que administran claves. En el diagrama se muestra cómo se separan el almacenamiento de datos cifrados y la administración de claves. 

![Diagrama de diseño](./media/database-encryption-at-rest/design-diagram.png)

El flujo básico de una solicitud de usuario es el siguiente:
- La cuenta de la base de datos del usuario se prepara y las claves de almacenamiento se recuperan a través de una solicitud al proveedor de recursos del servicio de administración.
- Un usuario crea una conexión a Cosmos DB a través de HTTPS o transporte seguro. (En los SDK se incluye una breve descripción al respecto).
- El usuario envía un documento JSON para almacenarlo a través de la conexión segura creada anteriormente.
- El documento JSON se indexa a menos que el usuario haya desactivado la indexación.
- Tanto el documento JSON como los datos de índice se escriben en un almacenamiento seguro.
- Los datos se leen periódicamente desde el almacenamiento seguro y se realiza una copia de seguridad de ellos en el almacén de blobs cifrado de Azure.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>P: ¿Cuánto aumenta el costo de Azure Storage si se habilita el cifrado del servicio Storage?
R: No hay costo adicional.

### <a name="q-who-manages-the-encryption-keys"></a>P: ¿Quién administra las claves de cifrado?
R: Las administra Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>P: ¿Con qué frecuencia se alternan las claves de cifrado?
R: Microsoft cuenta con un conjunto de directrices internas para la rotación de claves de cifrado y Cosmos DB las sigue. Estas directrices específicas no se han publicado. Microsoft sí que publica el [Ciclo de vida de desarrollo de seguridad (SDL) de Microsoft](https://www.microsoft.com/sdl/default.aspx), que se considera un subconjunto de orientaciones internas e incluye procedimientos recomendados para desarrolladores de gran utilidad.

### <a name="q-can-i-use-my-own-encryption-keys"></a>P: ¿Puedo usar mis propias claves de cifrado?
R: Cosmos DB es un servicio de PaaS y se ha trabajado mucho para que siga siendo fácil de usar. Se ha observado que esta pregunta suele plantearse en relación con proxys y con la satisfacción de algún requisito de conformidad, como PCI DSS. Como parte de la compilación de esta característica, se colaboró con auditores de conformidad normativa para garantizar que los clientes que usen Cosmos DB cumplan sus requisitos sin tener que administrar las claves por ellos mismos.
Por esta razón, que actualmente no permitimos a los usuarios la opción de encargarse ellos mismos de la administración de claves.

### <a name="q-what-regions-have-encryption-turned-on"></a>P: ¿Qué regiones tienen activado el cifrado?
R: Todas las regiones de Azure Cosmos DB tienen activado el cifrado de todos los datos de usuario.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>P: ¿Afecta el cifrado a los Acuerdos de Nivel de Servicio de rendimiento y de latencia del rendimiento?
R: No hay ningún impacto ni cambios en el rendimiento de los Acuerdos de Nivel de Servicio ahora que está habilitado el cifrado en reposo para todas las cuentas nuevas y existentes. Puede leer más sobre la página del [Acuerdo de Nivel de Servicio de Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) para consultar las garantías más recientes.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>P: ¿Admite el emulador local admite el cifrado en reposo?
R: El emulador es una herramienta de desarrollo o prueba independiente y no emplea los servicios de administración de claves que usa el servicio de Cosmos DB. Nuestra recomendación es habilitar BitLocker en aquellas unidades de disco donde se almacenan los datos de prueba del emulador confidenciales. El [emulador admite los cambios de directorio de datos predeterminado](local-emulator.md), así como el uso de una ubicación conocida.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información general sobre la seguridad y las últimas mejoras de Cosmos DB, consulte [Seguridad de base de datos de Azure Cosmos DB](database-security.md).
Para obtener más información sobre las certificaciones de Microsoft, visite el [Centro de confianza de Azure](https://azure.microsoft.com/en-us/support/trust-center/).
