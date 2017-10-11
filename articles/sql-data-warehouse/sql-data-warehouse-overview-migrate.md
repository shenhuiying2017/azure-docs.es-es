---
title: "Migración de la solución a SQL Data Warehouse | Microsoft Docs"
description: "Guía de migración para llevar una solución a la plataforma Almacenamiento de datos SQL de Azure."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 198365eb-7451-4222-b99c-d1d9ef687f1b
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 06/27/2017
ms.author: joeyong;barbkess
ms.openlocfilehash: 771b9456e66b8a1e41f72340b695b19e2adaf793
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="migrate-your-solution-to-azure-sql-data-warehouse"></a>Migración de una solución a Azure SQL Data Warehouse
Vea lo que supone migrar una solución de base de datos existente a Azure SQL Data Warehouse. 

## <a name="profile-your-workload"></a>Generación del perfil de la carga de trabajo
Antes de migrar, debería estar seguro de que SQL Data Warehouse sea la solución adecuada para su carga de trabajo. SQL Data Warehouse es un sistema distribuido diseñado para realizar análisis con datos de gran tamaño.  Para migrar a SQL Data Warehouse, son necesarios algunos cambios de diseño que no son demasiado difíciles de entender, pero que pueden tardar algún tiempo en implementarse. Si su negocio requiere un almacenamiento de datos de clase empresarial, las ventajas merecen la pena. Sin embargo, si no necesita la capacidad de SQL Data Warehouse, es más rentable usar SQL Server o Azure SQL Database.

Considere usar SQL Data Warehouse cuando:
- Tenga uno o varios terabytes de datos
- Planee ejecutar análisis en grandes cantidades de datos
- Necesite la capacidad de escalar el almacenamiento y los procesos 
- Desee ahorrar costos pausando los recursos de procesos cuando no los necesite.

No use SQL Data Warehouse para las cargas de trabajo operativas (OLTP) que tengan:
- Una elevada frecuencia de lecturas y escrituras
- Gran número de selecciones de singleton
- Grandes volúmenes de inserción de filas únicas
- Necesidades de procesamiento fila por fila
- Formatos incompatibles (JSON, XML)


## <a name="plan-the-migration"></a>Planeamiento de la migración

Una vez que haya decidido migrar una solución existente a SQL Data Warehouse, es importante planear la migración antes de comenzar. 

Uno de los objetivos del planeamiento es asegurarse de que los datos, los esquemas de tabla y el código sean compatibles con SQL Data Warehouse. Existen algunas diferencias de compatibilidad que solventar entre el sistema actual y SQL Data Warehouse. Además, migrar grandes cantidades de datos a Azure lleva tiempo. Un planeamiento cuidadoso permite pasar los datos a Azure más rápidamente. 

Otro objetivo del planeamiento es realizar ajustes de diseño para asegurarse de que la solución aproveche el elevado rendimiento de consultas que SQL Data Warehouse está diseñado para proporcionar. El diseño del almacenamiento de datos para escala presenta diferentes patrones de diseño, de manera que los enfoques tradicionales no son siempre la mejor opción. Aunque puede realizar algunos ajustes de diseño después de la migración, hacerlos antes en el proceso le ahorrará tiempo más adelante.

Para llevar a cabo una migración correcta, debe migrar los esquemas de tabla, el código y los datos. Para instrucciones sobre estos temas de migración, consulte:

-  [Migración de los esquemas](sql-data-warehouse-migrate-schema.md)
-  [Migración del código](sql-data-warehouse-migrate-code.md)
-  [Migración de los datos](sql-data-warehouse-migrate-data.md) 

<!--
## Perform the migration


## Deploy the solution


## Validate the migration

-->

## <a name="next-steps"></a>Pasos siguientes
El equipo de asesoramiento al cliente también cuenta con buenas directrices sobre SQL Data Warehouse, que publican a través de blogs.  Eche un vistazo a su artículo [Migrating data to Azure SQL Data Warehouse in practice][Migrating data to Azure SQL Data Warehouse in practice] (Migración de datos a Azure SQL Data Warehouse en la práctica) para obtener más instrucciones acerca de la migración.

<!--Image references-->

<!--Article references-->

<!--MSDN references-->

<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
