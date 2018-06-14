---
title: Migración de la solución a SQL Data Warehouse | Microsoft Docs
description: Guía de migración para llevar una solución a la plataforma Azure SQL Data Warehouse.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: 5a609fb2da1f9dba1247358f64b284fc3e3ef5bc
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
ms.locfileid: "31523482"
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
