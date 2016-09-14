<properties
   pageTitle="Migración de una solución a Almacenamiento de datos SQL | Microsoft Azure"
   description="Guía de migración para llevar una solución a la plataforma Almacenamiento de datos SQL de Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="barbkess;jrj;sonyama"/>

# Migración de una solución a Almacenamiento de datos SQL

Almacenamiento de datos SQL es un sistema de base de datos distribuidas que se amplía elásticamente para satisfacer sus necesidades. Para mantener tanto el rendimiento como la escala, no se implementan todas las características de SQL Server en Almacenamiento de datos SQL. En los siguientes temas sobre migración se abordan algunos de los factores clave para migrar una solución a Almacenamiento de datos SQL. El diseño del almacenamiento de datos para escala presenta diferentes patrones de diseño, de manera que los enfoques tradicionales no son siempre la mejor opción. Por lo tanto, puede que observe que adaptar su solución existente garantiza que saca el máximo rendimiento de la plataforma distribuida que ofrece Almacenamiento de datos SQL.

También es importante recordar que Almacenamiento de datos SQL es una plataforma basada en Microsoft Azure. Por lo tanto, parte de la migración también puede incluir la transferencia de datos a la nube. La transferencia de datos es un tema en sí mismo y debe considerarse detenidamente, en especial a medida que aumentan los volúmenes. La transferencia y la carga de datos son temas discretos.

## Guía de migración

Lea estos artículos para asegurarse de que entiende algunas de las diferencias y los conceptos fundamentales del producto antes de comenzar la migración.

- [Migración del esquema][]
- [Migración de los datos][]
- [Migración del código][]

## Pasos siguientes

El equipo de asesoramiento al cliente también cuenta con buenas directrices sobre Almacenamiento de datos SQL que publican a través de blogs. Eche un vistazo a su artículo [Migrating data to Azure SQL Data Warehouse in practice][] \(Migración de datos a Almacenamiento de datos SQL en la práctica) para obtener instrucciones acerca de la migración.

<!--Image references-->

<!--Article references-->
[Migración del esquema]: sql-data-warehouse-migrate-schema.md
[Migración de los datos]: sql-data-warehouse-migrate-data.md
[Migración del código]: sql-data-warehouse-migrate-code.md


<!--MSDN references-->


<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/

<!---HONumber=AcomDC_0831_2016-->