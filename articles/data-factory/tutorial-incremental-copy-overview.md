---
title: Copia incremental de datos con Azure Data Factory | Microsoft Docs
description: "En estos tutoriales se muestra cómo copiar datos de forma incremental de un almacén de datos de origen a un almacén de datos de destino. La primera de ellas copia los datos de una tabla."
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
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: e7582b2ea209e608abce721ef0cd3555e5ccec93
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Carga incremental de datos de un almacén de datos de origen a un almacén de datos de destino

En una solución de integración de datos, la carga incremental (o diferencial) de los datos después de una carga completa de los datos es un método ampliamente usado. En los tutoriales de esta sección se muestran distintas formas de cargar datos de forma incremental mediante la versión 2 de Azure Data Factory.

## <a name="delta-data-loading-by-using-a-watermark"></a>Carga de datos diferenciales mediante una marca de agua
En este caso, definirá una marca de agua en la base de datos de origen. Una marca de agua es una columna que tiene la marca de tiempo de la última actualización o una clave de incremento. La solución de carga diferencial carga los datos modificados entre una marca de agua antigua y una nueva marca de agua. En el siguiente diagrama se representa el flujo de trabajo de este enfoque: 

![Flujo de trabajo de uso de una marca de agua](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Consulte los siguientes temas para obtener instrucciones paso a paso: 

- [Carga de datos de forma incremental de Azure SQL Database a Azure Blob Storage](tutorial-incremental-copy-powershell.md)
- [Carga incremental de datos de varias tablas de SQL Server a Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)


## <a name="delta-data-loading-by-using-the-change-tracking-technology"></a>Carga de datos diferencial mediante la tecnología de control de cambios
La tecnología de control de cambios es una solución ligera de SQL Server y Azure SQL Database que ofrece un mecanismo eficaz de control de cambios para las aplicaciones. Así, permite que una aplicación identifique fácilmente los datos que se insertaron, actualizaron o eliminaron. 

En el siguiente diagrama se representa el flujo de trabajo de este enfoque:

![Flujo de trabajo del control de cambios](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Para ver instrucciones paso a paso, consulte el siguiente tutorial: <br/>
[Carga incremental de datos de Azure SQL Database a Azure Blob Storage mediante la información de control de cambios](tutorial-incremental-copy-change-tracking-feature-powershell.md)


## <a name="next-steps"></a>pasos siguientes
Avance al siguiente tutorial: 

> [!div class="nextstepaction"]
>[Carga de datos de forma incremental de Azure SQL Database a Azure Blob Storage](tutorial-incremental-copy-powershell.md)
