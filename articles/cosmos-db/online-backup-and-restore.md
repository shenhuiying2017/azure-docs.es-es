---
title: "Copias de seguridad y restauración en línea con Azure Cosmos DB | Microsoft Docs"
description: "Obtenga información sobre cómo ejecutar copias de seguridad y restauración automáticas de bases de datos de Azure Cosmos DB."
keywords: "copias de seguridad y restauración, copia de seguridad en línea"
services: cosmos-db
documentationcenter: 
author: RahulPrasad16
manager: jhubbard
editor: monicar
ms.assetid: 98eade4a-7ef4-4667-b167-6603ecd80b79
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/15/2017
ms.author: raprasa
ms.openlocfilehash: 3b421ca0d4ec612c5b0da25bcff712eb7ff9df85
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="automatic-online-backup-and-restore-with-azure-cosmos-db"></a>Copias de seguridad y restauración automáticas en línea con Azure Cosmos DB
Azure Cosmos DB crea automáticamente copias de seguridad de todos los datos a intervalos regulares. Las copias de seguridad automáticas se crean sin afectar el rendimiento ni la disponibilidad de las operaciones de bases de datos. Todas las copias de seguridad se almacenan por separado en otro servicio de almacenamiento y se replican globalmente para lograr resistencia frente a desastres regionales. Las copias de seguridad automáticas están pensadas para escenarios en los que se elimina accidentalmente el contenedor de Cosmos DB y que después requieren una solución de recuperación de datos o de recuperación ante desastres.  

Este artículo comienza con un resumen rápido de la disponibilidad y redundancia de datos en Cosmos DB y, luego, analiza las copias de seguridad. 

## <a name="high-availability-with-cosmos-db---a-recap"></a>Resumen de la alta disponibilidad con Cosmos DB
Cosmos DB está diseñado para su [distribución global](distribute-data-globally.md): le permite escalar el rendimiento en varias regiones de Azure junto con API transparentes de hospedaje múltiple y conmutación por error controlada por directivas. Azure Cosmos DB ofrece [Acuerdos de Nivel de Servicio con disponibilidad del 99,99 %](https://azure.microsoft.com/support/legal/sla/cosmos-db) para todas las cuentas de una sola región y todas las cuentas de varias regiones con coherencia moderada, y disponibilidad de lectura del 99,999 % para todas las cuentas de base de datos de varias regiones. Todas las escrituras de Azure Cosmos DB se confirman de forma duradera en los discos locales mediante un cuórum de réplicas dentro de un centro de datos local antes de informar al cliente. Tenga en cuenta que la alta disponibilidad de Cosmos DB se basa en el almacenamiento local y no depende de ninguna tecnología de almacenamiento externo. Además, si la cuenta de base de datos está asociada con más de una región de Azure, las escrituras se replicarán también en otras regiones. Para escalar el rendimiento y los datos de acceso a bajas latencias, puede tener todas las regiones de lectura asociadas que desee con la cuenta de base de datos. En cada región de lectura, los datos (replicados) persisten de forma duradera en un conjunto de réplicas.  

Tal como aparece en el diagrama siguiente, un único contenedor de Cosmos DB tiene [particiones horizontales](partition-data.md). En el siguiente diagrama, las "particiones" se indican con un círculo y cada una de ellas tiene alta disponibilidad a través de un conjunto de réplicas. Esta es la distribución local dentro de una región de Azure (se indica mediante el eje X). Además, cada partición (con su conjunto de réplicas correspondiente) se distribuye globalmente en varias regiones asociadas con su cuenta de base de datos (por ejemplo, en esta ilustración, las tres regiones: este de EE. UU., oeste de EE. UU. y centro de la India). El "conjunto de particiones" es una entidad distribuida globalmente que consta de varias copias de los datos en cada región (se indica mediante el eje Y). Puede asignar prioridad a las regiones asociadas con su cuenta de base de datos y Cosmos DB realizará una conmutación por error de forma transparente a la región siguiente en caso de desastre. También puede simular manualmente una conmutación por error para probar la disponibilidad completa de la aplicación.  

La imagen siguiente ilustra el alto grado de redundancia con Cosmos DB.

![Alto grado de redundancia con Cosmos DB](./media/online-backup-and-restore/redundancy.png)

![Alto grado de redundancia con Cosmos DB](./media/online-backup-and-restore/global-distribution.png)

## <a name="full-automatic-online-backups"></a>Copias de seguridad completas, automáticas y en línea
¡Vaya! Eliminé mi contenedor o base de datos. Con Cosmos DB, no solo los datos son altamente redundantes y resistentes ante los desastres regionales, sino que también las copias de seguridad de los mismos datos. Actualmente, estas copias de seguridad automatizadas se crean aproximadamente cada cuatro horas y se almacenan las dos copias de seguridad más recientes en todo momento. Si los datos se eliminan o dañan accidentalmente, [póngase en contacto con el soporte técnico de Azure](https://azure.microsoft.com/support/options/) en un plazo de ocho horas. 

Las copias de seguridad se crean sin afectar el rendimiento ni la disponibilidad de las operaciones de bases de datos. Cosmos DB crea la copia de seguridad en segundo plano y no consume las RU aprovisionadas ni tampoco afecta el rendimiento ni la disponibilidad de su base de datos. 

A diferencia de los datos almacenados en Cosmos DB, las copias de seguridad automáticas se almacenan en el servicio Azure Blob Storage. Para garantizar la carga eficaz/de baja latencia, la instantánea de la copia de seguridad se carga a una instancia de Azure Blob Storage en la misma región que la región de escritura actual de la cuenta de base de datos de Cosmos DB. Para lograr resistencia frente a algún desastre regional, cada instantánea de la copia de seguridad de los datos en Azure Blob Storage se vuelve a replicar mediante almacenamiento con redundancia geográfica (GRS) a otra región. El diagrama siguiente muestra que se crea una copia de seguridad de todo el contenedor de Cosmos DB (en este ejemplo, con las tres particiones principales en el oeste de EE. UU.) en una cuenta de Azure Blob Storage remota en el oeste de EE. UU. y, luego, se replica mediante GRS al este de EE. UU. 

La imagen siguiente ilustra copias de seguridad completas y periódicas de todas las entidades de Cosmos DB en Azure Storage de GRS.

![Copias de seguridad completas y periódicas de todas las entidades de Cosmos DB en Azure Storage de GRS](./media/online-backup-and-restore/automatic-backup.png)

## <a name="backup-retention-period"></a>Período de retención de copia de seguridad
Como se describió anteriormente, Azure Cosmos DB toma instantáneas de los datos cada cuatro horas en el nivel de partición. En un momento dado del tiempo, solo las dos últimas instantáneas se conservan. Sin embargo, si se elimina la colección o la base de datos, se conservan las instantáneas existentes para todas las particiones eliminadas dentro de la colección o base de datos determinada durante 30 días.

Si desea mantener sus propias instantáneas, puede utilizar la opción de exportación a JSON [herramienta de migración de datos](import-data.md#export-to-json-file) en la base de datos de Azure Cosmos para programar copias de seguridad adicionales.

## <a name="restoring-a-database-from-an-online-backup"></a>Restauración de una base de datos desde una copia de seguridad en línea
En caso de que elimine accidentalmente su base de datos o colección, puede [presentar un vale de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) o llamar al [servicio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) para restaurar los datos a partir de la copia de seguridad automática más reciente. Si necesita restaurar la base de datos debido a problemas de datos dañados (incluye los casos en los que se eliminan documentos de una colección), consulte [Control de datos dañados](#handling-data-corruption) ya que puede ser necesario realizar pasos adicionales para impedir que los datos dañados se escriban en las copias de seguridad existentes. Si se va a restaurar una instantánea específica de su copia de seguridad, Cosmos DB requiere que los datos hayan estado disponibles durante el ciclo de copia de seguridad de esa instantánea.

## <a name="handling-data-corruption"></a>Control de los datos dañados
Azure Cosmos DB conserva las dos últimas copias de seguridad de cada partición en la cuenta de la base de datos. Este modelo funciona bien cuando un contenedor (colección de documentos, grafos o tablas) o una base de datos se eliminan accidentalmente porque una de las últimas versiones se puede restaurar. Sin embargo, cuando se presenta un problema de daños en los datos, Azure Cosmos DB puede no ser consciente de los daños en los datos y es posible que los daños puedan haberse escrito en las copias de seguridad existentes. En cuanto se detectan daños, el usuario debe eliminar el contenedor dañado (colección, grafo o tabla) para que las copias de seguridad estén protegidas de ser sobrescritas con datos dañados.

## <a name="next-steps"></a>Pasos siguientes

Para replicar la base de datos en varios centros de datos, consulte [Distribución global de datos con Cosmos DB](distribute-data-globally.md). 

Para ponerse en contacto con el servicio de soporte técnico de Azure, [presente una incidencia de soporte técnico en Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

