---
title: Copia de datos de forma incremental con Azure Data Factory | Microsoft Docs
description: "En estos tutoriales se muestra cómo copiar datos de forma incremental de un almacén de datos de origen a un almacén de datos de destino. La primera de ellas copia los datos de una tabla. "
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/05/2017
ms.author: shlo
ms.openlocfilehash: 2ae6cb42685dfb227bd75f83e73dfdf646ab909f
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Carga incremental de datos de un almacén de datos de origen a un almacén de datos de destino

En una solución de integración de datos, la carga incremental (o diferencial) de los datos después de una carga completa de los datos es un método ampliamente usado. En los tutoriales de esta sección se muestran distintas formas de cargar datos de forma incremental mediante la versión 2 de Azure Data Factory.

## <a name="delta-data-loading-using-a-watermark"></a>Carga de datos diferencial con una marca de agua
En este caso, definirá una marca de agua en la base de datos de origen. Una marca de agua es una columna que tiene la marca de tiempo actualizada por última vez o una clave de incremento. La solución de carga diferencial carga los datos modificados entre una marca de agua antigua y una nueva marca de agua. En el siguiente diagrama se representa el flujo de trabajo de este enfoque: 

![Flujo de trabajo de uso de una marca de agua](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Consulte los siguientes temas para obtener instrucciones paso a paso: 

- [Incrementally copy data form one table in Azure SQL Database to Azure Blob Storage](tutorial-incremental-copy-powershell.md) (Copia incremental de datos de una tabla de Azure SQL Database a Azure Blob Storage)
- [Incrementally copy data from multiple tables in an on-premises SQL Server to Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md) (Copia incremental de datos de varias tablas de una instancia local de SQL Server a Azure SQL Database)


## <a name="delta-data-loading-using-the-change-tracking-technology"></a>Carga de datos diferencial mediante la tecnología de control de cambios
La tecnología de control de cambios es una solución ligera de SQL Server y Azure SQL Database que ofrece un mecanismo eficaz de control de cambios para las aplicaciones. Así, permite que una aplicación identifique fácilmente los datos que se han insertado, actualizado o eliminado. 

En el siguiente diagrama se representa el flujo de trabajo de este enfoque:

![Flujo de trabajo del control de cambios](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Para ver instrucciones paso a paso, consulte el siguiente tutorial: <br/>
[Incrementally copy data from Azure SQL Database to Azure Blob Storage using Change Tracking technology](tutorial-incremental-copy-change-tracking-feature-powershell.md) (Copia incremental de datos de Azure SQL Database a Azure Blob Storage mediante la tecnología de control de cambios)


## <a name="next-steps"></a>Pasos siguientes
Avance al siguiente tutorial: 

> [!div class="nextstepaction"]
>[Incrementally copy data form one table in Azure SQL Database to Azure Blob Storage](tutorial-incremental-copy-powershell.md) (Copia incremental de datos de una tabla de Azure SQL Database a Azure Blob Storage)
