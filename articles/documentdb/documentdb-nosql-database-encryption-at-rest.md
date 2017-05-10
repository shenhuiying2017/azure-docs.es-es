---
title: 'Cifrado de base de datos NoSQL en reposo: Azure DocumentDB | Microsoft Docs'
description: "Obtenga información sobre cómo Azure DocumentDB proporciona cifrado predeterminado de todos los datos NoSQL."
services: documentdb
author: voellm
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/31/2017
ms.author: voellm
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f291186c6a68dea8aa00b846a2e6f3ad0d7996c
ms.openlocfilehash: 9c97f76d581935cb85f33b0016c192ded3b6f880
ms.contentlocale: es-es
ms.lasthandoff: 04/28/2017


---

# <a name="documentdb-nosql-database-encryption-at-rest"></a>Cifrado de base de datos NoSQL en reposo en DocumentDB

El cifrado en reposo es una frase que se refiere normalmente al cifrado de datos en dispositivos de almacenamiento no volátil, como unidades de estado sólido (SSD) y discos duros (HDD).  DocumentDB almacena sus bases de datos principales en SSD y sus copias de seguridad y archivos adjuntos multimedia en blobs de Azure, que suelen estar respaldados por HDD.  Con el lanzamiento del cifrado en reposto para DocumentDB, todas las bases de datos, los archivos adjuntos multimedia y las copias de seguridad ahora sí están cifradas.  Esto significa que los datos ahora se cifran en tránsito (a través de la red) y en reposo (almacenamiento no volátil), lo que le ofrece un cifrado de un extremo a otro.

Como servicio PaaS, se ha trabajado duro para que DocumentDB resulte muy fácil de usar.  Teniendo esto en cuenta, todos los datos de usuario almacenados en DocumentDB se cifran en reposo y en tránsito, sin que el usuario tenga que realizar ninguna acción.  Otra opción de configuración consiste en activar de forma predeterminada el cifrado en reposo.  No hay ningún control para activar o desactivar esta opción y se ha proporcionado esta característica cumplimiento en todo momento con el [SLA de disponibilidad y rendimiento](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/).

## <a name="how-does-encryption-at-rest-work"></a>¿Cómo funciona el cifrado en reposo?

El cifrado en reposo se implementa mediante una serie de tecnologías de seguridad, como sistemas seguros de almacenamiento de claves, redes cifradas y API criptográficas.  En el diagrama siguiente se muestra cómo se separan el almacenamiento de datos cifrados y la administración de claves.  Los sistemas que descifran y procesan datos tienen que comunicarse con los sistemas que administran claves.

![Diagrama de diseño](./media/documentdb-nosql-database-encryption-at-rest/design-diagram.png)

El flujo básico de una solicitud de usuario es el siguiente:
- La cuenta de base de datos del usuario se prepara y las claves de almacenamiento se recuperan a través de una solicitud al proveedor de recursos del servicio de administración.
- Un usuario crea una conexión a DocumentDB a través de HTTPS y transporte seguro (los SDK resumen los detalles).
- Un usuario envía un documento JSON para almacenarlo a través de la conexión segura creada anteriormente.
- El documento JSON se indiza a menos que el usuario haya desactivado la indización.
- El documento JSON y los datos de índice se escriben en el almacenamiento seguro.
- Los datos se leen periódicamente desde el almacenamiento seguro y se realiza una copia de seguridad de ellos en el almacenamiento de blobs cifrado de Azure.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="q-how-much-more-does-azure-storage-cost-if-sse-is-enabled"></a>P: ¿El costo de Azure Storage sube si se habilita SSE?
R: No hay costo adicional.

### <a name="q-who-manages-the-encryption-keys"></a>P: ¿Quién administra las claves de cifrado?
R: Las administra Microsoft.

### <a name="q-can-i-use-my-own-encryption-keys"></a>P: ¿Puedo usar mis propias claves de cifrado?
R: DocumentDB es un servicio de PaaS y se ha trabajado duro para que el servicio siga siendo fácil de usar.  Se ha observado que esta pregunta suele plantearse como una pregunta de proxy para cumplir una norma de cumplimiento, como PCI-DSS.  Como parte de la creación de esta característica, se ha colaborado con los auditores de cumplimiento para garantizar que los clientes que usan DocumentDB cumplan los requisitos sin tener que administrar las claves por su cuenta.
Este es el motivo por el que actualmente ofrecemos a los usuarios la opción de encargarse de la administración de claves.

### <a name="q-what-regions-have-encryption-turned-on"></a>P: ¿Qué regiones tienen activado el cifrado?
R: Todas las regiones de DocumentDB tienen activado el cifrado de todos los datos de usuario.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información general sobre la seguridad y las últimas mejoras de DocumentDB, vea [Seguridad de bases de datos de DocumentDB NoSQL](documentdb-nosql-database-security.md).

Si quiere saber más sobre las certificaciones de Microsoft, visite el [Centro de confianza de Azure](https://azure.microsoft.com/en-us/support/trust-center/).

