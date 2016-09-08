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
   ms.date="08/17/2016"
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
Para obtener más sugerencias de desarrollo, consulte la [información general sobre desarrollo][].

También puede consultar la [referencia de Transact-SQL][] para obtener más información.

Por último, consulte la [información general sobre la carga][]. En este artículo se describen varias opciones de carga de datos y se facilitan instrucciones paso a paso.

<!--Image references-->

<!--Article references-->
[Migración del esquema]: sql-data-warehouse-migrate-schema.md
[Migración de los datos]: sql-data-warehouse-migrate-data.md
[Migración del código]: sql-data-warehouse-migrate-code.md

[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md
[información general sobre la carga]: sql-data-warehouse-overview-load.md
[referencia de Transact-SQL]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0824_2016-->